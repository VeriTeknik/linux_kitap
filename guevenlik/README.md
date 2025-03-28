# Linux Güvenliği Temelleri

Linux sistemlerinin güvenliği, modern bilişim altyapılarının temel taşlarından biridir. Ancak güvenlik, tek seferlik bir işlem değil, sürekli dikkat ve güncelleme gerektiren bir süreçtir. Güvenlik açıkları çeşitli nedenlerle ortaya çıkabilir:

*   **Teknolojik Değişimler:** Yazılımlarda ve protokollerde zamanla keşfedilen yeni zafiyetler (örn. kütüphanelerdeki açıklar, işlemci açıkları).
*   **Yapılandırma Hataları:** Servislerin veya sistemin yanlış veya eksik yapılandırılması (örn. gereksiz açık portlar, zayıf şifreler, yanlış izinler).
*   **Kullanıcı Hataları:** Güvenli olmayan yazılımların kullanılması, kimlik bilgilerinin zayıf olması veya paylaşılması, sosyal mühendislik saldırıları.

Bu bölümde, Linux sistemlerinin güvenliğini artırmak için alınabilecek temel önlemler ve dikkat edilmesi gereken noktalar ele alınacaktır. Bu öneriler %100 güvenlik garantisi vermese de, yaygın saldırıların ve hataların büyük bir kısmını engellemeye yardımcı olabilir.

## Genel Güvenlik Önerileri

**1. Ağ Güvenliği ve Güvenlik Duvarı (Firewall)**

*   **Firewall Kullanımı:** Sunucu üzerinde mutlaka bir güvenlik duvarı (firewall) etkinleştirilmelidir. Modern dağıtımlar genellikle `firewalld` (RHEL tabanlı) veya `ufw` (Uncomplicated Firewall - Debian/Ubuntu tabanlı) gibi daha kullanıcı dostu araçlar sunar. Bunlar arka planda `iptables` veya `nftables` gibi çekirdek mekanizmalarını yönetir.
    *   **Prensip:** Varsayılan olarak tüm gelen bağlantıları engelle (`DROP` veya `REJECT`) ve sadece gerekli olan servislere (örn. SSH - 22, HTTP - 80, HTTPS - 443) belirli kaynaklardan izin ver (`ACCEPT`).
    *   İçeriden dışarıya giden trafiği de kısıtlamak (egress filtering) güvenliği artırabilir.
    *   [Firewalld ile Güvenlik Duvarı Yönetimi](firewalld.md)
    *   [IPTables](iptables/README.md) (Daha düşük seviyeli, güçlü ama karmaşık)
*   **Donanımsal Firewall/Ağ Segmentasyonu:** Mümkünse, sunucuları donanımsal bir güvenlik duvarının arkasına yerleştirin. Farklı işlevlere sahip sunucuları (örn. web, veritabanı, yönetim) ayrı ağ segmentlerine (VLAN'lar) ayırın ve aralarındaki trafiği firewall ile kontrol edin. NAT kullanımı da dışarıdan doğrudan erişimi kısıtlayarak ek bir katman sağlayabilir.
*   **Gereksiz Servisleri Kapatma/Kaldırma:** Sistemde çalışmasına gerek olmayan servisleri durdurun ve başlangıçtan kaldırın (`systemctl disable <servis>`) veya tamamen kaldırın (`dnf remove`, `apt remove`). Hangi portların dinlendiğini `ss -tulnp` veya `netstat -tulnp` komutlarıyla kontrol edin.
*   **Yerel Servisleri Kısıtlama:** Sadece sunucunun kendisinden erişilmesi gereken servisleri (örn. veritabanı, bazı yönetim arayüzleri) sadece localhost'a (127.0.0.1, ::1) bağlanacak şekilde yapılandırın.

**2. Sistem Sertleştirme (Hardening)**

*   **Güçlü Şifre Politikaları:** Tüm kullanıcılar için güçlü ve benzersiz şifreler kullanılmasını zorunlu kılın (PAM modülleri ile ayarlanabilir). Şifrelerin düzenli aralıklarla değiştirilmesini sağlayın.
*   **SSH Güvenliği:**
    *   Şifre ile girişi devre dışı bırakıp sadece anahtar tabanlı kimlik doğrulamayı kullanın (`PasswordAuthentication no` in `sshd_config`).
    *   Doğrudan root girişini engelleyin (`PermitRootLogin no`).
    *   Standart olmayan bir port kullanın (`Port <port_no>`).
    *   Gerekmiyorsa protokol 1'i devre dışı bırakın (`Protocol 2`).
    *   Belirli kullanıcılara veya gruplara erişimi kısıtlayın (`AllowUsers`, `AllowGroups`).
    *   Başarısız giriş denemelerini sınırlayın (`MaxAuthTries`).
    *   `fail2ban` gibi araçlarla brute-force saldırılarını otomatik olarak engelleyin.
*   **`/tmp` Güvenliği:** `/tmp` dizinini (ve `/var/tmp`, `/dev/shm` gibi diğer geçici alanları) ayrı bir bölümde tutmak ve `noexec`, `nosuid`, `nodev` seçenekleriyle bağlamak (mount etmek), bu dizinlere yüklenen betiklerin çalıştırılmasını ve yetki yükseltme saldırılarını zorlaştırır. `/etc/fstab` dosyasında ilgili satırı düzenleyin:
    ```
    # Örnek /etc/fstab satırı
    UUID=...    /tmp    ext4    defaults,rw,nosuid,nodev,noexec    0    2 
    ```
    Değişikliği uygulamak için `sudo mount -o remount /tmp` komutunu çalıştırın veya sistemi yeniden başlatın.
*   **SELinux / AppArmor:** Mümkünse, dağıtımınızın sunduğu Zorunlu Erişim Kontrolü (MAC - Mandatory Access Control) sistemini (SELinux veya AppArmor) etkinleştirin ve yapılandırın. Bu sistemler, standart dosya izinlerinin ötesinde, süreçlerin hangi kaynaklara erişebileceğini kısıtlayarak potansiyel zararı sınırlar.
    *   [SELinux](selinux.md)
*   **Gereksiz Yazılımları Kaldırma:** Kullanılmayan derleyicileri, kütüphaneleri ve araçları sistemden kaldırın.

**3. Güncellemeler ve Yama Yönetimi**

*   **Düzenli Güncelleme:** İşletim sistemini ve kurulu tüm paketleri düzenli olarak güncelleyin. Güvenlik yamaları kritik öneme sahiptir.
*   **Otomatik Güncellemeler:** Güvenlik güncellemelerinin otomatik olarak uygulanmasını sağlayan araçları (örn. `dnf-automatic` (RHEL/CentOS), `unattended-upgrades` (Debian/Ubuntu)) yapılandırın. Dikkat: Otomatik güncellemeler nadiren de olsa uyumluluk sorunlarına yol açabilir, bu nedenle kritik sistemlerde dikkatli olunmalıdır.
*   **Kararlı Sürümler:** Mümkünse, dağıtımınızın uzun süreli destek (LTS) veya kararlı sürümlerini kullanın. Alpha, beta veya geliştirme sürümlerinden kaçının. Abonelik tabanlı dağıtımlar (RHEL, CloudLinux) genellikle daha hızlı güvenlik yamaları sunabilir.

**4. İzleme ve Denetim**

*   **Merkezi Log Yönetimi:** Birden fazla sunucunuz varsa, logları merkezi bir sistemde toplamak (örn. rsyslog/syslog-ng ile uzak sunucuya gönderme, ELK Stack, Graylog) ve analiz etmek önemlidir.
*   **NTP Senkronizasyonu:** Tüm sunucuların saatlerinin NTP ile senkronize olduğundan emin olun. Bu, logların ilişkilendirilmesi ve olayların doğru zaman sırasına konulması için kritiktir.
*   **Dosya Bütünlüğü İzleme:** `aide` veya `tripwire` gibi araçlarla önemli sistem dosyalarının değiştirilip değiştirilmediğini düzenli olarak kontrol edin.
*   **Sızma Testleri:** Periyodik olarak iç ve dış sızma testleri yaparak veya yaptırarak sistemlerinizdeki potansiyel açıkları tespit edin. Kali Linux gibi dağıtımlar bu amaçla birçok araç içerir.
*   **Güvenlik Taramaları:** `nmap` ile ağ taramaları, `OpenVAS` veya `Nessus` gibi araçlarla zafiyet taramaları yapın.

**5. Uygulama Güvenliği**

*   **Web Uygulamaları:** SQL Injection, Cross-Site Scripting (XSS), CSRF gibi yaygın web zafiyetlerine karşı önlem alın. Güvenli kodlama pratiklerini uygulayın. Web Application Firewall (WAF) kullanmayı değerlendirin.
*   **Veritabanı Güvenliği:** Güçlü şifreler kullanın, gereksiz kullanıcıları kaldırın, ağ erişimini kısıtlayın, düzenli yedek alın.
*   **Konfigürasyon Yönetimi:** Uygulamaların (Apache, Nginx, PHP, MySQL vb.) yapılandırma dosyalarını gözden geçirin, gereksiz modülleri/fonksiyonları devre dışı bırakın, güvenlik ayarlarını sıkılaştırın.

Güvenlik sürekli bir çaba gerektirir. Yeni tehditler ve zafiyetler ortaya çıktıkça sistemlerinizi güncel tutmak ve yapılandırmalarınızı gözden geçirmek önemlidir.
