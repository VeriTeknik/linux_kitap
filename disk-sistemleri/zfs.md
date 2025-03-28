# ZFS Dosya Sistemi

ZFS, geleneksel dosya sistemi ve birim yöneticisi (volume manager) kavramlarını birleştiren, veri bütünlüğü, ölçeklenebilirlik ve yönetim kolaylığına odaklanan gelişmiş bir dosya sistemidir. Başlangıçta Sun Microsystems tarafından Solaris için geliştirilmiş olup, günümüzde açık kaynaklı **OpenZFS** projesi altında Linux, FreeBSD ve diğer platformlarda yaygın olarak kullanılmaktadır.

## Temel Kavramlar ve Özellikler

ZFS, geleneksel disk bölümleme, `mkfs` ve LVM gibi adımları tek bir komut seti altında birleştirir.

*   **Depolama Havuzları (Storage Pools / `zpool`):** ZFS'nin temelidir. Bir veya daha fazla sanal aygıttan (vdev) oluşan depolama havuzlarıdır. ZFS, diskleri değil, havuzları yönetir. Havuzlar, `zpool create` komutu ile oluşturulur.
*   **Sanal Aygıtlar (Virtual Devices / vdevs):** Havuzları oluşturan yapı taşlarıdır. Bir vdev şunlar olabilir:
    *   Tek bir disk veya disk bölümü.
    *   **Mirror (Ayna):** RAID1 benzeri, iki veya daha fazla diskin tam kopyasını tutar, yüksek okuma performansı ve hata toleransı sağlar.
    *   **RAID-Z (raidz1, raidz2, raidz3):** ZFS'nin entegre yazılımsal RAID uygulamasıdır. RAID5/RAID6'ya benzer ancak "write hole" sorununa karşı daha dayanıklıdır. raidz1 bir disklik, raidz2 iki disklik, raidz3 üç disklik hata toleransı sağlar.
    *   Disk dosyaları (test amaçlı).
    *   Log (ZIL SLOG) ve Cache (L2ARC) aygıtları (genellikle hızlı SSD'ler).
*   **Veri Kümeleri (Datasets):** Havuz içinde oluşturulan, bağlanabilir (mountable) dosya sistemleridir (`zfs create pool/dataset`). Havuzun özelliklerini miras alırlar ancak sıkıştırma, kotalar, mount noktası gibi kendi özelliklerine sahip olabilirler. Yönetimi çok kolaydır.
*   **Birimler (Volumes / zvols):** Havuz içinde oluşturulan blok aygıtlarıdır (`zfs create -V <boyut> pool/volume`). Genellikle sanal makinelerin diskleri veya iSCSI hedefleri için kullanılırlar.
*   **Copy-on-Write (CoW):** ZFS'deki temel mekanizmadır. Veriler asla doğrudan üzerine yazılmaz. Bir blok değiştirildiğinde, yeni veri boş bir bloğa yazılır ve ardından üst seviye metadata güncellenerek yeni bloğu işaret eder. Bu işlem atomiktir.
    *   **Avantajları:** Güç kesintisi veya çökme anında dosya sistemi tutarsızlığı riskini ortadan kaldırır (geleneksel `fsck` ihtiyacı yoktur), anlık görüntü (snapshot) oluşturmayı çok verimli hale getirir.
*   **Anlık Görüntüler (Snapshots):** Bir dataset veya volume'ün belirli bir andaki salt okunur (read-only) kopyalarıdır (`zfs snapshot pool/dataset@snapname`). CoW sayesinde anlık olarak oluşturulurlar ve başlangıçta neredeyse hiç yer kaplamazlar (sadece metadata). Zamanla orijinal veri değiştikçe, snapshot eski blokları tutmaya devam ettiği için yer kaplamaya başlar. Yedekleme, geri alma ve veri klonlama için temel oluştururlar.
*   **Klonlar (Clones):** Bir snapshot'ın yazılabilir kopyalarıdır (`zfs clone pool/dataset@snapname pool/newdataset`). Başlangıçta snapshot ile aynı blokları paylaşırlar, sadece değişiklikler ek yer kaplar. Test ortamları veya sanal makine şablonları oluşturmak için kullanışlıdır.
*   **Veri Bütünlüğü (Data Integrity):**
    *   **Checksumming:** Tüm veri blokları ve metadata için sağlama toplamları (checksums, örn. fletcher4, sha256) hesaplanır ve metadata içinde saklanır. Veri okunduğunda checksum tekrar hesaplanır ve eşleşmezse hata tespit edilir.
    *   **Self-Healing (Kendini Onarma):** Eğer havuzda yedeklilik varsa (mirror veya raidz), ZFS bozuk bir blok tespit ettiğinde otomatik olarak diğer kopyadan doğru veriyi okuyup bozuk olanı düzeltebilir. Bu, "sessiz veri bozulması" (bit rot) sorununa karşı güçlü bir koruma sağlar.
*   **RAID-Z:** Yazılımsal RAID'i doğrudan havuz seviyesinde yönetir. Geleneksel donanımsal RAID kartlarına olan ihtiyacı azaltabilir.
*   **Sıkıştırma (Compression):** Şeffaf ve anlık (on-the-fly) veri sıkıştırma desteği sunar (`zfs set compression=lz4 pool/dataset`). `lz4` (varsayılan ve önerilen), `gzip`, `zstd` gibi algoritmalar desteklenir. CPU kullanımı karşılığında disk alanı kazancı ve bazen I/O performans artışı sağlar.
*   **Tekilleştirme (Deduplication):** (Dikkatli Kullanılmalıdır!) Aynı veri bloklarının sadece bir kopyasını saklayarak disk alanı kazancı sağlar (`zfs set dedup=on pool/dataset`). Ancak çok yüksek miktarda RAM (genellikle TB başına 1-5 GB RAM) gerektirir ve yazma performansını önemli ölçüde düşürebilir. Genellikle özel durumlar dışında önerilmez.
*   **Önbellekleme (Caching):**
    *   **ARC (Adaptive Replacement Cache):** RAM'i okuma önbelleği olarak kullanır. ZFS, ARC için sistemdeki boş RAM'in önemli bir kısmını kullanabilir.
    *   **L2ARC (Level 2 ARC):** Hızlı bir SSD gibi ikincil bir aygıtı okuma önbelleği olarak kullanır. ARC dolduğunda sık erişilen veriler L2ARC'a taşınabilir.
    *   **ZIL (ZFS Intent Log) / SLOG (Separate Log Device):** Senkron yazma işlemlerini hızlandırmak için kullanılır. Normalde ZIL havuzdaki diskler üzerine yazılır. Hızlı bir SSD ayrı bir log aygıtı (SLOG) olarak eklendiğinde, senkron yazmalar önce SLOG'a yazılır ve daha sonra ana havuz disklerine aktarılır. Bu, özellikle NFS veya veritabanı gibi senkron yazma ağırlıklı iş yüklerinde performansı artırır.

## Temel Yönetim Komutları

ZFS yönetimi için iki ana komut kullanılır: `zpool` (havuzlar için) ve `zfs` (datasetler, volume'lar, snapshot'lar için). Komutlar genellikle `sudo` gerektirir.

**Havuz Yönetimi (`zpool`):**

```bash
# Havuz oluşturma (örnek: iki disklik mirror)
sudo zpool create mypool mirror /dev/sda /dev/sdb

# Havuz oluşturma (örnek: üç diskli raidz1)
sudo zpool create mypool raidz1 /dev/sda /dev/sdb /dev/sdc

# Havuzları listeleme
sudo zpool list

# Havuz durumunu detaylı gösterme (hataları kontrol etme)
sudo zpool status mypool 

# Havuza aygıt ekleme (örn. mirror'a disk ekleyerek kapasite artmaz, yedeklilik artar; veya yeni vdev ekleme)
# sudo zpool add mypool mirror /dev/sdd /dev/sde 

# Havuzu yok etme (DİKKAT! TÜM VERİ SİLİNİR!)
# sudo zpool destroy mypool

# Başka sistemden taşınan havuzu içeri alma (import)
# sudo zpool import 
# sudo zpool import mypool

# Havuzu dışarı çıkarma (export - başka sisteme taşımadan önce)
# sudo zpool export mypool
```

**Dataset/Volume/Snapshot Yönetimi (`zfs`):**

```bash
# Dataset oluşturma (otomatik olarak bağlanır, örn. /mypool/data)
sudo zfs create mypool/data

# Volume (zvol) oluşturma (10GB boyutunda)
sudo zfs create -V 10G mypool/myvolume

# Tüm ZFS dosya sistemlerini/volume'ları listeleme
sudo zfs list

# Snapshot oluşturma
sudo zfs snapshot mypool/data@backup_20231027

# Snapshot'ları listeleme
sudo zfs list -t snapshot

# Özellik ayarlama (örn. sıkıştırmayı açma)
sudo zfs set compression=lz4 mypool/data

# Özellik değerini görme
sudo zfs get compression mypool/data

# Dataset'i yok etme (DİKKAT! İçindeki veri silinir!)
# sudo zfs destroy mypool/data

# Snapshot'ı yok etme
# sudo zfs destroy mypool/data@backup_20231027

# Snapshot'tan klon oluşturma
# sudo zfs clone mypool/data@backup_20231027 mypool/data_clone

# Dataset'i/Snapshot'ı başka bir havuza/sunucuya gönderme/alma (yedekleme/replikasyon)
# sudo zfs send mypool/data@snap | ssh user@remote_host 'sudo zfs receive otherpool/data_backup'
```

## ZFS on Linux (ZoL)

ZFS'nin lisanslama uyumsuzlukları nedeniyle doğrudan Linux çekirdeğine dahil edilmesi tartışmalıdır. Ancak **OpenZFS** projesi, ZFS'yi Linux üzerinde kullanmayı mümkün kılar. Kurulum genellikle dağıtıma göre değişir:

*   **Ubuntu:** Genellikle `zfsutils-linux` paketi ile kurulabilir.
*   **Debian:** Benzer şekilde `zfsutils-linux` paketi ile.
*   **RHEL/CentOS/Fedora:** Genellikle ek bir depo (örn. ZFS on Linux deposu veya EPEL) aracılığıyla `zfs` paketleri kurulur. DKMS (Dynamic Kernel Module Support) kullanılarak çekirdek modüllerinin derlenmesi gerekebilir.

ZFS, sunduğu gelişmiş özellikler ve veri bütünlüğü garantileri ile özellikle depolama sunucuları, NAS sistemleri ve kritik verilerin saklandığı ortamlar için güçlü bir seçenektir. Ancak LVM ve ext4/XFS gibi geleneksel çözümlere göre daha fazla sistem kaynağı (özellikle RAM) gerektirebilir ve yönetimi biraz daha farklıdır.
