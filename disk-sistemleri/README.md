# Disk Sistemleri ve Dosya Sistemleri

Linux sistemlerde verilerin depolandığı disklerin yönetimi ve bu diskler üzerindeki dosya sistemleri kritik öneme sahiptir. Bu bölüm, yaygın olarak kullanılan bazı dosya sistemlerini ve disk yönetim teknolojilerini ele almaktadır.

İncelenecek konular şunlardır:

*   **ext Dosya Sistemleri (ext2, ext3, ext4):** Linux'un geleneksel ve en yaygın kullanılan dosya sistemi ailesi. Özellikle `ext4`, günümüzde birçok dağıtım için varsayılan seçenektir ve günlükleme (journaling) gibi özellikler sunar.
    *   [ext](ext.md)
*   **LVM (Logical Volume Management):** Fiziksel diskleri veya bölümleri soyutlayarak daha esnek bir disk yönetimi katmanı sunar. Mantıksal birimleri (Logical Volumes) kolayca oluşturma, yeniden boyutlandırma ve anlık görüntü (snapshot) alma gibi avantajlar sağlar. Sunucu ortamlarında sıklıkla kullanılır.
    *   [LVM](lvm.md)
*   **ZFS:** Geleneksel dosya sistemi ve birim yöneticisi kavramlarını birleştiren gelişmiş bir dosya sistemidir. Veri bütünlüğü koruması, anlık görüntüler, klonlama, dahili RAIDZ (RAID benzeri) özellikleri, sıkıştırma ve tekilleştirme (deduplication) gibi birçok gelişmiş özellik sunar. Özellikle FreeBSD'de popülerdir ve Linux üzerinde de (ZFS on Linux projesi ile) kullanılabilir.
    *   [ZFS](zfs.md)

Doğru disk yönetimi ve dosya sistemi seçimi, sistemin performansı, güvenilirliği ve yönetilebilirliği açısından önemlidir.
