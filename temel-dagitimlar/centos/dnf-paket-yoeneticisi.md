# DNF Paket Yöneticisi

RHEL tabanlı modern Linux dağıtımlarında (Fedora, CentOS Stream, Rocky Linux, AlmaLinux, RHEL 8+) paket yönetimi için kullanılan standart araç **dnf** (Dandified YUM)'dir. `dnf`, eski paket yöneticisi `yum`'un (Yellowdog Updater Modified) yeni nesil versiyonudur ve daha iyi performans, daha düşük bellek kullanımı, geliştirilmiş bağımlılık çözümü gibi avantajlar sunar.

Çoğu modern sistemde, geriye dönük uyumluluk için `yum` komutu `dnf`'e yönlendirilmiş bir sembolik link veya alias olabilir. Ancak, yeni komutları ve özellikleri kullanmak için doğrudan `dnf` komutunu öğrenmek ve kullanmak tavsiye edilir.

`dnf`, sisteminize yazılım kurmak, güncellemek, kaldırmak ve yapılandırılmış yazılım depolarını (repositories) yönetmek için kullanılır. Temel olarak RPM paketleri üzerinde çalışır ancak bağımlılıkları otomatik olarak çözer.

## Paket Arama ve Listeleme

Bir paketin depolarda mevcut olup olmadığını aramak için `search` kullanılır:
```bash
# İçinde 'htop' geçen paketleri ara
sudo dnf search htop 
```

Belirli bir paketin veya tüm paketlerin listelenmesi için `list` kullanılır:
```bash
# 'htop' paketinin kurulu olup olmadığını ve mevcut sürümünü listele
sudo dnf list htop

# Sistemde kurulu tüm paketleri listele
sudo dnf list installed | less

# Depolarda mevcut tüm paketleri listele
sudo dnf list available | less

# Tüm paketleri (kurulu ve mevcut) listele
sudo dnf list all | less 
```
Kurulu paketleri listelemek için `rpm -qa` komutu da kullanılabilir.

## Paket Kurma (`install`)

Bir veya daha fazla paketi kurmak için `install` komutu kullanılır. `dnf`, gerekli bağımlılıkları otomatik olarak bulup kurmaya çalışacaktır.
```bash
# htop paketini kur
sudo dnf install htop
```
Kurulum sırasında onay istenecektir. Onay vermeden doğrudan kurmak için `-y` parametresi eklenebilir:
```bash
sudo dnf install -y htop
```
Yerel bir RPM dosyasını kurarken de `dnf` kullanmak, bağımlılıkların depolardan çözülmesini sağlar:
```bash
sudo dnf install /path/to/local-package.rpm
```

## Paket Kaldırma (`remove`)

Bir veya daha fazla paketi kaldırmak için `remove` (veya `erase`) komutu kullanılır:
```bash
# htop paketini kaldır
sudo dnf remove htop
```
Yine onay istenir ve `-y` ile atlanabilir. `dnf remove`, kaldırılan pakete bağımlı olan ve başka hiçbir paket tarafından kullanılmayan diğer paketleri de kaldırmayı önerebilir (bkz. `autoremove`).

## Paket Güncelleme (`upgrade`)

Sistemdeki kurulu tüm paketleri en son sürümlerine güncellemek için `upgrade` komutu kullanılır:
```bash
# Tüm sistemi güncelle
sudo dnf upgrade -y
```
Sadece belirli bir paketi güncellemek için:
```bash
# Sadece vim paketini güncelle
sudo dnf upgrade vim
```
Sadece güvenlik güncellemelerini uygulamak için:
```bash
sudo dnf upgrade --security
```
Hangi paketlerin güncellemesinin mevcut olduğunu görmek için `check-update` kullanılır:
```bash
sudo dnf check-update
```

## Paket Bilgisi (`info`)

Bir paket hakkında (kurulu olsun veya olmasın) detaylı bilgi almak için `info` kullanılır:
```bash
sudo dnf info htop
```
Bu komut paketin sürümünü, mimarisini, boyutunu, özetini, açıklamasını ve hangi depodan geldiğini gösterir. Benzer bilgi `rpm -qi <paket-ismi>` ile de alınabilir (sadece kurulu paketler için).

## Dosyanın Hangi Pakete Ait Olduğunu Bulma (`provides`)

Belirli bir dosyanın veya komutun hangi paket tarafından sağlandığını bulmak için `provides` (veya `whatprovides`) kullanılır:
```bash
# /usr/bin/htop komutunu hangi paketin sağladığını bul
sudo dnf provides /usr/bin/htop

# httpd.conf dosyasını sağlayan paketi bul
sudo dnf provides '*/httpd.conf' 
```

## Paket Grupları (`group`)

İlgili paketler genellikle gruplar halinde sunulur (örneğin, "Web Server", "Development Tools").

Mevcut grupları listelemek için:
```bash
sudo dnf group list
sudo dnf group list hidden # Gizli grupları da göster
```
Bir grup hakkında bilgi almak için (içerdiği zorunlu, varsayılan, opsiyonel paketler):
```bash
sudo dnf group info "Development Tools"
```
Bir grubu kurmak, güncellemek veya kaldırmak için:
```bash
sudo dnf group install "Development Tools"
sudo dnf group upgrade "Development Tools"
sudo dnf group remove "Development Tools"
```
Grup kurulumlarında varsayılan olarak hangi tür paketlerin (mandatory, default, optional) kurulacağını `/etc/dnf/dnf.conf` dosyasındaki `group_package_types` direktifi belirler.

## İşlem Geçmişi (`history`)

`dnf`, yapılan tüm kurulum, kaldırma, güncelleme işlemlerinin kaydını tutar. Bu, değişiklikleri takip etmek ve gerekirse geri almak için çok kullanışlıdır.

Geçmiş işlemleri listelemek için:
```bash
sudo dnf history list
```
Belirli bir işlem hakkında detaylı bilgi almak için (ID'si ile):
```bash
sudo dnf history info 15 
```
Belirli bir işlemi geri almak için (örneğin 15 numaralı kurulumu geri al):
```bash
sudo dnf history undo 15
```
Geri alınan bir işlemi tekrar uygulamak için:
```bash
sudo dnf history redo 15
```

## Diğer Kullanışlı `dnf` Komutları

*   **`autoremove`**: Başka hiçbir kurulu paketin ihtiyaç duymadığı bağımlılıkları (artık gereksiz olan paketleri) kaldırmak için kullanılır. Genellikle bir `remove` işleminden sonra çalıştırılır.
    ```bash
    sudo dnf autoremove
    ```
*   **`downgrade`**: Bir paketi önceki bir sürümüne düşürmek için kullanılır.
    ```bash
    sudo dnf downgrade <paket-ismi>
    ```
*   **`reinstall`**: Bir paketi yeniden kurmak için kullanılır (yapılandırma dosyaları genellikle korunur).
    ```bash
    sudo dnf reinstall <paket-ismi>
    ```
*   **`clean`**: `dnf` tarafından önbelleğe alınan verileri temizlemek için kullanılır (örn. `dnf clean all`).

## Modüller (RHEL 8+ / CentOS Stream 8+)

RHEL 8 ve sonrası tabanlı dağıtımlarda "Application Streams" (Uygulama Akışları) ve `dnf module` komutları tanıtılmıştır. Bu, aynı paketin farklı sürümlerini (örneğin Python 3.6, 3.8, 3.9) aynı anda depolarda bulundurmayı ve kullanıcının hangisini kuracağını seçmesini sağlar.

Mevcut modülleri listelemek için:
```bash
sudo dnf module list
```
Belirli bir modül akışını (stream) etkinleştirmek ve kurmak için:
```bash
sudo dnf module install <modül-adı>:<akış-adı> 
# Örnek: sudo dnf module install python39
```

## Yapılandırma

`dnf`'in ana yapılandırma dosyası `/etc/dnf/dnf.conf`'tur. Yazılım depoları ise `/etc/yum.repos.d/` dizini altındaki `.repo` uzantılı dosyalarda tanımlanır (bkz. [Repo Ayarları](repo-ayarlari.md)).

`dnf`, RHEL tabanlı sistemlerde paket yönetiminin temel taşıdır ve `rpm` komutuna göre çok daha kullanıcı dostu ve güvenli bir deneyim sunar.
