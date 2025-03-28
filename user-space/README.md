# User Space ve Init Sistemleri

Çekirdek (kernel) başlatıldıktan ve temel donanım ayarlarını yaptıktan sonra, kontrolü kullanıcı alanındaki (user space) ilk işleme, yani **PID 1**'e devreder. Bu ilk işlem, **init sistemi** olarak bilinir ve görevi, sistemin geri kalanını (servisler, kullanıcı oturumları vb.) başlatmak ve yönetmektir.

Linux tarihinde farklı init sistemleri kullanılmıştır. Bu bölümde iki ana init sistemini inceleyeceğiz:

1.  **System V init (SysVinit):** Geleneksel Unix init sistemidir. Betik tabanlıdır ve "runlevel" konseptini kullanır. Eski sistemlerde yaygın olarak kullanılmıştır, ancak modern dağıtımların çoğunda yerini systemd'ye bırakmıştır.
    *   [System V](system-v/README.md)
2.  **systemd:** Modern Linux dağıtımlarının büyük çoğunluğu tarafından benimsenen, daha gelişmiş ve paralel başlatma yeteneklerine sahip bir sistem ve servis yöneticisidir. "Unit" dosyaları ve "target" konseptini kullanır.
    *   [systemd](systemd/README.md)

Ayrıca bu bölümde, çekirdekten init sistemine geçişte rol oynayan diğer kavramlara da değinilecektir:

*   [Başlangıç: Init](baslangic-init.md): PID 1'in rolü.
*   [Init Versiyonunu Belirlemek](init-versiyonunu-belirlemek.md): Sisteminizde hangi init sisteminin çalıştığını anlama.
*   [Başlangıç RAM Dosya Sistemi](baslangic-ram-dosya-sistemi.md): Initramfs/initrd'nin user space başlangıcındaki rolü (Kernel Boot bölümüyle ilişkili).
*   [Acil Durumlar ve Tek Kullanıcı Modu](acil-durumlar-ve-tek-kullanici-modu.md): Sistem kurtarma modları.

Init sistemini anlamak, servislerin nasıl yönetildiğini, sistemin nasıl başladığını ve sorunların nasıl giderileceğini anlamak için kritik öneme sahiptir.
