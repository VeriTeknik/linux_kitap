# Debian GNU/Linux

Debian, tamamen gönüllüler tarafından geliştirilen, özgür yazılım ilkelerine sıkı sıkıya bağlı, köklü ve yaygın olarak kullanılan bir Linux dağıtımıdır. Kararlılığı, güvenliği ve devasa paket deposu ile bilinir. Popüler Ubuntu dağıtımı da Debian'ı temel alır.

## Genel Özellikler

*   **Köken:** Bağımsız bir topluluk projesidir. "Debian Sosyal Sözleşmesi" ve "Debian Özgür Yazılım Yönergeleri" projenin temelini oluşturur.
*   **Sürümler:** Debian'ın genellikle üç ana sürüm dalı bulunur:
    *   `stable` (Kararlı): Uzun süre test edilmiş, kararlı ve güvenilir sürümdür. Sunucular için genellikle bu sürüm tercih edilir, ancak paketleri biraz daha eski olabilir.
    *   `testing` (Test): Bir sonraki kararlı sürüm için hazırlanan paketleri içerir. `stable`'a göre daha günceldir ancak daha az test edilmiştir.
    *   `unstable` (Sid): En güncel paketlerin bulunduğu geliştirme dalıdır. Kararlılık garantisi yoktur.
*   **Paket Yönetimi:** `.deb` paket formatını ve `apt` (Advanced Package Tool) paket yöneticisi ailesini (örn. `apt`, `apt-get`, `apt-cache`) kullanır. `dpkg` ise `.deb` dosyalarıyla doğrudan çalışmak için kullanılan düşük seviyeli araçtır.
    *   [deb Paketleri](deb-paketleri.md)
    *   [apt Paket Yöneticisi (aptitude yerine)](aptitude-paket-yoeneticisi.md) (Dosya adı eski kalmış, içerik güncellenmeli)
*   **Depo Yönetimi:** Yazılım depoları `/etc/apt/sources.list` dosyasında ve `/etc/apt/sources.list.d/` dizini altındaki `.list` dosyalarında tanımlanır.
    *   [Repo Ayarları](repo-ayarlari.md)
*   **Ağ Yönetimi:** Geleneksel olarak `/etc/network/interfaces` dosyası ve `ifup`/`ifdown` komutları ile yönetilirdi. Modern Debian sürümleri (özellikle masaüstü kurulumları) NetworkManager'ı da destekler ve kullanabilir. Sunucu kurulumlarında hala `/etc/network/interfaces` yaygın olarak kullanılır, ancak `systemd-networkd` de bir alternatiftir.
    *   [Network Ayarları](network-ayarlari.md)
*   **Felsefe:** Özgür yazılım, topluluk, kararlılık ve evrensel bir işletim sistemi olma hedefleri ön plandadır.
*   **Hedef Kitle:** Sunucular, masaüstü kullanıcıları, geliştiriciler, özgür yazılım meraklıları, kararlılık ve geniş paket seçeneği arayanlar.

**Kurulum:**
Debian kurulumu, metin tabanlı veya grafiksel bir yükleyici ile yapılır. Kurulum sırasında dil, klavye, ağ, disk bölümlendirme, yazılım seçimi gibi adımlar bulunur. Detaylı kurulum kılavuzları için resmi Debian belgelerine başvurulmalıdır:
*   Debian Kurulum Kılavuzu: [https://www.debian.org/releases/stable/installmanual](https://www.debian.org/releases/stable/installmanual)

Bu bölümdeki diğer dosyalar, Debian sistemlerde paket yönetimi, depo ayarları ve ağ yapılandırması gibi temel konuları ele alacaktır (içeriklerinin güncellenmesi gerekmektedir).
