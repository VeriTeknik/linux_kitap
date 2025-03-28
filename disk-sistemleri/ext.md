# ext Dosya Sistemleri (ext2, ext3, ext4)

**ext** (Extended File System - Genişletilmiş Dosya Sistemi), Linux için özel olarak geliştirilmiş ve uzun yıllar boyunca standart kabul edilmiş dosya sistemi ailesidir. Zaman içinde çeşitli iyileştirmelerle evrimleşmiştir.

## Evrim

*   **ext2 (Second Extended Filesystem):** Linux'un ilk yaygınlaşan dosya sistemlerinden biridir. Sağlam ve basit bir yapıya sahiptir, ancak önemli bir eksiği **günlükleme (journaling)** özelliğinin olmamasıdır. Bu nedenle, sistem beklenmedik şekilde kapandığında (elektrik kesintisi, çökme vb.) dosya sisteminde tutarsızlıklar oluşabilir ve sistem yeniden başladığında uzun süren bir dosya sistemi kontrolü (`fsck`) gerektirebilir. Günümüzde genellikle özel durumlar (örn. flash sürücülerde yazma sayısını azaltmak) dışında pek tercih edilmez.
*   **ext3 (Third Extended Filesystem):** ext2'nin üzerine en önemli eklemesi **günlükleme** özelliğidir. Günlükleme, dosya sistemi üzerinde yapılacak değişiklikleri önce özel bir alana (günlük) yazarak, beklenmedik kapanmalar sonrasında dosya sisteminin çok daha hızlı ve güvenilir bir şekilde tutarlı hale getirilmesini sağlar. ext2 ile tam uyumludur; mevcut bir ext2 dosya sistemi veri kaybı olmadan ext3'e dönüştürülebilir ve ext3 dosya sistemi ext2 olarak bağlanabilir (günlükleme özellikleri devre dışı kalır). Üç farklı günlükleme modu sunar: `journal` (en güvenli, hem veri hem metadata günlüğü), `ordered` (varsayılan, sadece metadata günlüğü, veri yazıldıktan sonra metadata güncellenir), `writeback` (en hızlı, sadece metadata günlüğü, yazma sırası garanti edilmez).
*   **ext4 (Fourth Extended Filesystem):** Günümüzde çoğu Linux dağıtımının **varsayılan** dosya sistemidir ve ext3 üzerine kurulu önemli geliştirmeler içerir:
    *   **Daha Büyük Dosya Sistemi ve Dosya Boyutları:** Çok daha büyük diskleri (Exabyte seviyesinde) ve tekil dosyaları (Tebibyte seviyesinde) destekler.
    *   **Extent'ler:** Büyük dosyaları disk üzerinde daha verimli bir şekilde saklamak için blok listeleri yerine "extent" (başlangıç bloku + blok sayısı) kullanır. Bu, performansı artırır ve parçalanmayı (fragmentation) azaltır.
    *   **Gecikmeli Ayırma (Delayed Allocation):** Veri diske yazılana kadar blok ayırma işlemini geciktirir. Bu, dosya sistemi yöneticisinin daha iyi blok yerleştirme kararları almasını sağlar, performansı artırır ve parçalanmayı azaltır.
    *   **Daha Hızlı `fsck`:** Dosya sistemi kontrol süreleri, özellikle büyük disklerde, ext3'e göre önemli ölçüde kısalmıştır.
    *   **Kalıcı Ön Ayırma (Persistent Pre-allocation):** Uygulamaların belirli bir disk alanını önceden ayırmasına olanak tanır, bu da bazı durumlarda performansı artırabilir.
    *   **Journal Checksums:** Günlük verilerinin bütünlüğünü kontrol etmek için sağlama toplamları eklenmiştir, güvenilirliği artırır.
    *   **Multiblock Allocator:** Tek seferde birden fazla blok ayırarak performansı artırır ve parçalanmayı azaltır.

## ext4 Kullanımı

*   **Oluşturma:** Bir disk bölümünü ext4 olarak formatlamak için `mkfs.ext4` komutu kullanılır:
    ```bash
    # /dev/sda1 bölümünü ext4 olarak formatla
    sudo mkfs.ext4 /dev/sda1 
    ```
    Çeşitli seçeneklerle inode boyutu, blok boyutu, ayrılmış blok yüzdesi gibi parametreler ayarlanabilir (`man mkfs.ext4`).
*   **Bağlama (Mount):** Dosya sistemini bir dizine bağlamak için `mount` komutu kullanılır. Kalıcı hale getirmek için `/etc/fstab` dosyasına eklenir.
    ```bash
    # Geçici olarak bağlama
    sudo mount /dev/sda1 /mnt/data

    # /etc/fstab örneği
    # UUID=<uuid>    /mnt/data    ext4    defaults    0    2
    ```
    `defaults` seçeneği genellikle `rw,suid,dev,exec,auto,nouser,async` anlamına gelir. `noatime`, `nodiratime` gibi seçenekler performans için eklenebilir.
*   **Kontrol ve Onarım (`fsck`/`e2fsck`):** Dosya sistemini kontrol etmek ve hataları onarmak için kullanılır. Genellikle dosya sistemi bağlı değilken çalıştırılmalıdır.
    ```bash
    # Bağlı olmayan /dev/sda1'i kontrol et
    sudo fsck.ext4 -f /dev/sda1 
    # veya
    sudo e2fsck -f /dev/sda1 
    ```
    `-f` force (zorla kontrol), `-y` tüm sorulara otomatik evet yanıtı verir (dikkatli kullanılmalıdır).
*   **Yeniden Boyutlandırma (`resize2fs`):** ext3/ext4 dosya sistemlerini yeniden boyutlandırmak için kullanılır. Genellikle LVM ile birlikte kullanılır. Dosya sistemini **büyütmek** çoğu zaman bağlıyken (online) yapılabilir, ancak **küçültmek** için dosya sisteminin bağlı **olmaması** gerekir.
    ```bash
    # LVM mantıksal birimini büyüttükten sonra dosya sistemini büyütme (online)
    sudo resize2fs /dev/mapper/vg0-data 

    # Dosya sistemini belirli bir boyuta küçültme (önce umount edilmeli)
    sudo umount /mnt/data
    sudo e2fsck -f /dev/mapper/vg0-data # Küçültmeden önce kontrol et
    sudo resize2fs /dev/mapper/vg0-data 50G # 50GB'a küçült
    sudo mount /dev/mapper/vg0-data /mnt/data
    ```
*   **Parametre Ayarlama (`tune2fs`):** Dosya sistemi parametrelerini görüntülemek ve değiştirmek için kullanılır (örn. etiket ayarlama, ayrılmış blok yüzdesini değiştirme, özellik ekleme/çıkarma).
    ```bash
    # Dosya sistemi bilgilerini göster
    sudo tune2fs -l /dev/sda1

    # Etiket ata
    sudo tune2fs -L DataVolume /dev/sda1
    ```

ext4, güvenilirliği, performansı ve yaygın desteği nedeniyle çoğu Linux masaüstü ve sunucu kurulumu için hala sağlam bir tercihtir. Ancak XFS (özellikle büyük dosyalar ve paralel I/O için) veya Btrfs/ZFS (gelişmiş özellikler için) gibi alternatifler de bulunmaktadır.
