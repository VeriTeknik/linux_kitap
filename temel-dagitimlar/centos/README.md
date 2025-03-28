# RHEL Tabanlı Dağıtımlar (CentOS Stream, Rocky Linux, AlmaLinux)

Bu bölüm, Red Hat Enterprise Linux (RHEL) ekosistemindeki güncel dağıtımlara odaklanmaktadır. Eskiden çok popüler olan CentOS Linux, RHEL'in birebir ücretsiz bir kopyası olarak hizmet veriyordu. Ancak CentOS projesi, RHEL'in bir sonraki sürümünün geliştirme dalı olan **CentOS Stream**'e odaklanmıştır. CentOS Linux'un yerini doldurmak için ise topluluk tarafından **Rocky Linux** ve **AlmaLinux** gibi RHEL ile birebir uyumlu (bug-for-bug compatible) dağıtımlar geliştirilmiştir.

## Genel Özellikler

*   **Köken:** Hepsi Red Hat Enterprise Linux (RHEL) kod tabanını temel alır. RHEL, kurumsal düzeyde destek ve uzun vadeli kararlılık sunan ticari bir üründür.
*   **CentOS Stream:** RHEL'in bir sonraki küçük sürümüne (minor release) giden geliştirme adımlarını içerir. RHEL'den biraz daha ileride, güncel paketler sunar ancak RHEL klonları kadar uzun vadeli stabilite garantisi vermeyebilir. Geliştirme ve test ortamları için veya en son RHEL özelliklerine erken erişim isteyenler için uygundur.
*   **Rocky Linux & AlmaLinux:** CentOS Linux'un misyonunu devralan, RHEL ile tam uyumluluk hedefleyen, topluluk destekli ücretsiz dağıtımlardır. Kararlı sunucu ortamları için tasarlanmışlardır ve RHEL ile aynı yaşam döngüsünü takip etmeyi hedeflerler.
*   **Paket Yönetimi:** RPM paket formatını ve modern `dnf` paket yöneticisini kullanırlar (`yum` eski sürümlerde kullanılıyordu ve genellikle `dnf`'e bir sembolik linktir).
    *   [RPM Paketleri](rpm-paketleri.md)
    *   [DNF Paket Yöneticisi (yum yerine)](yum-paket-yoeneticisi.md) (Dosya adı eski kalmış, içerik güncellenmeli)
*   **Depo Yönetimi:** Yazılım depoları (repositories) `/etc/yum.repos.d/` dizini altındaki `.repo` dosyaları ile yönetilir.
    *   [Repo Ayarları](repo-ayarlari.md)
*   **Ağ Yönetimi:** Genellikle NetworkManager servisi ve `nmcli` komut satırı aracı veya `nmtui` metin arayüzü ile yönetilir. Eski `network` servisi ve `/etc/sysconfig/network-scripts/` altındaki `ifcfg-*` dosyaları hala kullanılabilir olsa da, NetworkManager modern standarttır.
    *   [Network Ayarları](network-ayarlari.md)
*   **Güvenlik:** SELinux (Security-Enhanced Linux) genellikle varsayılan olarak etkindir ve zorunlu erişim kontrolü (MAC) sağlar. Firewall yönetimi için `firewalld` kullanılır.
*   **Hedef Kitle:** Kararlı sunucu ortamları, kurumsal uygulamalar, RHEL ekosistemiyle uyumluluk arayanlar. CentOS Stream daha çok geliştiricilere ve en son özellikleri test etmek isteyenlere yöneliktir.

**Kurulum:**
Bu dağıtımların kurulumu genellikle Anaconda grafiksel veya metin tabanlı yükleyici ile yapılır. Kurulum adımları (dil, klavye, saat dilimi, disk bölümlendirme, ağ ayarları, yazılım seçimi, kullanıcı oluşturma) dağıtımlar arasında büyük ölçüde benzerlik gösterir. Detaylı kurulum kılavuzları için ilgili dağıtımın resmi belgelerine başvurulmalıdır:
*   CentOS Stream: [https://www.centos.org/centos-stream/](https://www.centos.org/centos-stream/)
*   Rocky Linux: [https://rockylinux.org/](https://rockylinux.org/)
*   AlmaLinux: [https://almalinux.org/](https://almalinux.org/)

Bu bölümdeki diğer dosyalar, bu modern RHEL tabanlı dağıtımlarda paket yönetimi, depo ayarları ve ağ yapılandırması gibi temel konuları ele alacaktır (içeriklerinin güncellenmesi gerekmektedir).
