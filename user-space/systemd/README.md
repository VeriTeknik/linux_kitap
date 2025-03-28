# systemd

`systemd`, modern Linux dağıtımlarının büyük çoğunluğu tarafından benimsenen gelişmiş bir **sistem ve servis yöneticisidir**. Geleneksel System V init (SysVinit) sisteminin yerini almıştır ve PID 1 olarak çalışarak sistemi başlatmaktan, servisleri yönetmekten ve sistemin genel durumunu denetlemekten sorumludur.

## Temel Kavramlar ve Özellikler

*   **Unit Dosyaları:** systemd, yönettiği kaynakları (servisler, mount noktaları, cihazlar, soketler vb.) **unit** adı verilen yapılandırma dosyaları aracılığıyla tanımlar. Bu dosyalar genellikle `.service`, `.socket`, `.mount`, `.target` gibi uzantılara sahiptir ve `/usr/lib/systemd/system/` (dağıtım tarafından sağlananlar) veya `/etc/systemd/system/` (sistem yöneticisi tarafından oluşturulan veya düzenlenenler) dizinlerinde bulunur. Unit dosyaları, basit bir `INI` benzeri sözdizimine sahiptir.
    *   [Birimler ve Birim Tipleri](birimler-ve-birim-tipleri.md)
*   **Servis Yönetimi (`systemctl`):** Servisleri (ve diğer unit'leri) yönetmek için ana komut `systemctl`'dir. Servisleri başlatma, durdurma, yeniden başlatma, durumunu kontrol etme, başlangıçta etkinleştirme/devre dışı bırakma gibi işlemler bu komutla yapılır.
    *   [Servislerin Yönetimi](servislerin-yoenetimi.md)
*   **Target Birimleri:** SysVinit'teki runlevel konseptinin yerini alan **target** birimleri, sistemin belirli bir çalışma durumunu veya hedefini temsil eder. Target'lar, diğer unit'leri gruplayarak belirli bir amaca hizmet eden bir dizi servisin başlatılmasını sağlar (örn. `multi-user.target`, `graphical.target`, `rescue.target`).
*   **Paralel Başlatma:** systemd, servisler arasındaki bağımlılıkları analiz ederek mümkün olduğunca çok servisi aynı anda (paralel olarak) başlatmaya çalışır. Bu, genellikle SysVinit'in sıralı başlatmasına göre daha hızlı bir sistem açılışı sağlar.
*   **Soket Aktivasyonu:** Servisler, ilgili sokete bir bağlantı geldiğinde otomatik olarak başlatılabilir. Bu, servisin sürekli çalışmasına gerek kalmadan kaynak tasarrufu sağlar.
*   **Günlük Yönetimi (Journald):** systemd, sistem ve servis günlüklerini merkezi ve yapılandırılmış bir şekilde toplayan ve yöneten `journald` servisini içerir. Günlüklere `journalctl` komutu ile erişilir.
*   **Diğer Bileşenler:** systemd ayrıca `systemd-networkd` (ağ yönetimi), `systemd-resolved` (DNS çözümleme), `systemd-timesyncd` (zaman senkronizasyonu), `systemd-logind` (kullanıcı oturum yönetimi) gibi birçok alt bileşen içerir.

## Avantajları

*   Daha hızlı sistem açılışı (paralel başlatma).
*   Daha gelişmiş ve esnek servis bağımlılık yönetimi.
*   Servislerin daha iyi izlenmesi ve otomatik yeniden başlatılması.
*   Merkezi ve yapılandırılmış günlük kaydı (journald).
*   Cgroups aracılığıyla kaynak yönetimi entegrasyonu.
*   Daha standart ve güçlü servis yönetim komutları (`systemctl`).

systemd, modern Linux sistem yönetiminin temel taşı haline gelmiştir ve komutlarını ve kavramlarını anlamak, sistem yöneticileri için önemlidir. Bu bölümdeki diğer dosyalar, systemd'nin temel bileşenlerini ve kullanımını daha detaylı olarak ele alacaktır.
