# FreeBSD

FreeBSD, Berkeley Software Distribution (BSD) temelinden gelen, açık kaynaklı, Unix benzeri bir işletim sistemidir. Linux'tan farklı bir çekirdeğe ve temel kullanıcı alanı araçlarına (userland) sahiptir. Genellikle kararlılığı, yüksek performansı (özellikle ağ konusunda) ve gelişmiş özellikleriyle bilinir.

## Genel Özellikler

*   **Köken:** Kaliforniya Üniversitesi, Berkeley'de geliştirilen BSD Unix'ten türemiştir.
*   **Çekirdek ve Userland:** Linux dağıtımlarının aksine, FreeBSD çekirdeği ve temel kullanıcı alanı araçları (C kütüphanesi, kabuk, temel komutlar vb.) tek bir proje kapsamında birlikte geliştirilir. Bu, daha entegre ve tutarlı bir sistem sağlar.
*   **Lisans:** Linux'un kullandığı GPL yerine daha esnek olan BSD lisansını kullanır.
*   **Paket Yönetimi:** İki ana sistem kullanılır:
    *   **Ports Collection:** `/usr/ports` altında bulunan, yazılımları kaynak koddan derleyip kurmayı sağlayan bir sistemdir. Yüksek düzeyde özelleştirme imkanı sunar.
    *   **pkg:** Önceden derlenmiş ikili (binary) paketleri yönetmek için kullanılan modern paket yöneticisidir. Hızlı kurulum sağlar.
    *   [Ports ve pkg Yönetimi](port-ve-pck_add-yoenetimi.md) (Dosya adı eski `pkg_add`'ı içeriyor, içerik güncellenmeli)
*   **Sistem Güncelleme:** Temel sistem ve kurulan paketler ayrı ayrı güncellenir. Temel sistem genellikle `freebsd-update` aracıyla, paketler ise `pkg upgrade` ile güncellenir.
    *   [Güncelleme Teknikleri](guencelleme-teknikleri.md)
*   **Ağ Yönetimi:** Ağ yapılandırması genellikle `/etc/rc.conf` dosyası üzerinden yapılır. `ifconfig` komutu arayüzleri yapılandırmak için kullanılır.
    *   [Network Ayarları](network-ayarlari.md)
*   **Öne Çıkan Özellikler:** Güçlü ve kararlı ağ yığını (network stack), ZFS dosya sisteminin gelişmiş entegrasyonu, Jails (hafif işletim sistemi seviyesinde sanallaştırma/konteyner mekanizması), DTrace (dinamik izleme aracı) gibi özellikleriyle tanınır.
*   **Hedef Kitle:** Sunucular (özellikle ağ servisleri), yüksek performans ve kararlılık gerektiren sistemler, gömülü sistemler, Unix felsefesine ve BSD lisansına yakınlık duyanlar.

**Kurulum:**
FreeBSD kurulumu genellikle `bsdinstall` metin tabanlı yükleyici ile yapılır. Kurulum sırasında klavye düzeni, ana bilgisayar adı, kurulacak bileşenler (Ports, src vb.), disk bölümlendirme (UFS veya ZFS), ağ ayarları, saat dilimi, root şifresi ve kullanıcı oluşturma gibi adımlar bulunur. Detaylı kurulum kılavuzları için resmi FreeBSD El Kitabı'na başvurulmalıdır:
*   FreeBSD El Kitabı - Kurulum: [https://docs.freebsd.org/en/books/handbook/bsdinstall/](https://docs.freebsd.org/en/books/handbook/bsdinstall/)

Bu bölümdeki diğer dosyalar, FreeBSD sistemlerde paket yönetimi, sistem güncelleme ve ağ yapılandırması gibi temel konuları ele alacaktır (içeriklerinin güncellenmesi gerekmektedir).
