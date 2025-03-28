# SELinux (Security-Enhanced Linux)

SELinux, Linux çekirdeğine entegre edilmiş güçlü bir **Zorunlu Erişim Kontrolü (MAC - Mandatory Access Control)** güvenlik mekanizmasıdır. Geleneksel Linux **İsteğe Bağlı Erişim Kontrolü (DAC - Discretionary Access Control)** modeline (yani dosya izinleri - sahip/grup/diğer, rwx) ek bir güvenlik katmanı sağlar. RHEL tabanlı dağıtımlarda (Fedora, CentOS Stream, Rocky Linux, AlmaLinux) varsayılan olarak etkin gelir.

## Temel Kavramlar

SELinux'un temel amacı, süreçlerin (processes) erişebileceği kaynakları (dosyalar, portlar, diğer süreçler vb.) en az yetki prensibine (principle of least privilege) göre sıkı bir şekilde sınırlamaktır. Bir süreç, normal dosya izinleri izin verse bile, SELinux politikası izin vermiyorsa belirli bir kaynağa erişemez.

*   **Güvenlik Bağlamları (Security Contexts / Labels):** SELinux, sistemdeki her nesneye (süreç, dosya, port, arayüz vb.) bir güvenlik etiketi veya bağlam atar. Bu bağlam genellikle `kullanıcı:rol:tip:seviye` formatındadır (örn. `system_u:object_r:httpd_sys_content_t:s0`). En önemli kısım genellikle **tip**'tir.
*   **Politika (Policy):** Hangi tipteki sürecin, hangi tipteki kaynağa nasıl erişebileceğini (örn. `httpd_t` tipindeki sürecin `httpd_sys_content_t` tipindeki dosyaları okumasına izin verilir) tanımlayan kurallar bütünüdür. Dağıtımlar genellikle varsayılan bir politika ile gelir (`targeted` politikası en yaygın olanıdır).
*   **Çalışma Modları:**
    *   `Enforcing`: Politika aktif olarak uygulanır. İzin verilmeyen eylemler engellenir ve `/var/log/audit/audit.log` dosyasına (eğer `auditd` servisi çalışıyorsa) kaydedilir. Bu, varsayılan ve önerilen moddur.
    *   `Permissive`: Politika uygulanmaz (eylemler engellenmez), ancak politika ihlalleri yine de loglanır. Sorun giderme sırasında, bir problemin SELinux kaynaklı olup olmadığını anlamak için geçici olarak bu moda geçilebilir.
    *   `Disabled`: SELinux tamamen devre dışıdır. Bu moddan tekrar `Permissive` veya `Enforcing` moda geçmek genellikle dosya sisteminin yeniden etiketlenmesini gerektirir ve **önerilmez**.
*   **Booleans:** Politikanın belirli yönlerini kolayca açıp kapatmak için kullanılan anahtarlardır. Örneğin, Apache'nin ağa bağlanmasına veya kullanıcı ev dizinlerini okumasına izin veren boolean'lar olabilir. Bu, tüm politikayı yeniden yazmadan belirli davranışlara izin vermeyi sağlar.

## Temel Yönetim Komutları

*   **Durumu Kontrol Etme:**
    ```bash
    # Mevcut modu göster (Enforcing, Permissive, Disabled)
    getenforce 

    # Daha detaylı durum bilgisi (mod, politika tipi vb.)
    sestatus 
    ```
*   **Modu Geçici Olarak Değiştirme:** (Sistem yeniden başlayana kadar geçerli)
    ```bash
    # Permissive moda geç
    sudo setenforce 0

    # Enforcing moda geç
    sudo setenforce 1
    ```
*   **Modu Kalıcı Olarak Değiştirme:**
    `/etc/selinux/config` (veya `/etc/sysconfig/selinux`) dosyasını düzenleyin ve `SELINUX=` satırını `enforcing`, `permissive` veya `disabled` olarak ayarlayın. **`disabled` moduna geçmek veya bu moddan çıkmak için sistemin yeniden başlatılması gerekir.** `enforcing` ve `permissive` arasında geçiş için `setenforce` yeterlidir, ancak kalıcı ayar için dosya düzenlenmelidir.
    ```bash
    # Örnek /etc/selinux/config içeriği
    SELINUX=enforcing 
    SELINUXTYPE=targeted 
    ```
*   **Bağlamları Görüntüleme:**
    ```bash
    # Dosya/dizin bağlamlarını göster
    ls -Z /var/www/html

    # Süreç bağlamlarını göster
    ps auxZ | grep httpd
    ```
*   **Dosya Bağlamını Geçici Olarak Değiştirme (`chcon`):**
    Bir dosyanın veya dizinin güvenlik bağlamını geçici olarak değiştirir. `restorecon` çalıştırıldığında veya sistem yeniden etiketlendiğinde varsayılan değere döner.
    ```bash
    # Apache'nin okuyabilmesi için tipini değiştir
    sudo chcon -t httpd_sys_content_t /srv/web/index.html 
    ```
*   **Varsayılan Dosya Bağlamını Geri Yükleme (`restorecon`):**
    Bir dosyanın veya dizinin (ve altındakilerin `-R` ile) politika tarafından tanımlanan varsayılan güvenlik bağlamını geri yükler. Yanlış bağlam nedeniyle oluşan erişim sorunlarını çözmek için sıkça kullanılır.
    ```bash
    # Belirtilen dizin ve altındakilerin bağlamlarını düzelt
    sudo restorecon -Rv /var/www/html 
    ```
*   **Kalıcı Bağlam Ayarları (`semanage`):**
    Dosya yolları için varsayılan bağlamları, portları, boolean'ları vb. kalıcı olarak yönetmek için kullanılır. Bu komut genellikle `policycoreutils-python-utils` (veya benzeri) paketinin kurulmasını gerektirir.
    ```bash
    # Apache'nin 8080 portunu dinlemesine izin ver
    sudo semanage port -a -t http_port_t -p tcp 8080

    # /srv/www dizininin varsayılan bağlamını ayarla
    sudo semanage fcontext -a -t httpd_sys_content_t "/srv/www(/.*)?"
    sudo restorecon -Rv /srv/www # Ayarı uygula
    ```
*   **Boolean'ları Yönetme (`getsebool`, `setsebool`):**
    ```bash
    # Tüm boolean'ları ve durumlarını listele
    getsebool -a

    # Belirli bir boolean'ı listele (örn. httpd'nin ağa bağlanması)
    getsebool httpd_can_network_connect

    # Bir boolean'ı geçici olarak aç/kapat
    sudo setsebool httpd_can_network_connect on

    # Bir boolean'ı kalıcı olarak aç/kapat (-P)
    sudo setsebool -P httpd_can_network_connect on 
    ```

## Sorun Giderme

SELinux, izin sorunlarının yaygın bir kaynağı olabilir. Bir uygulamanın beklendiği gibi çalışmamasının nedeni SELinux ise, genellikle `/var/log/audit/audit.log` dosyasında `type=AVC` içeren bir kayıt bulunur.

1.  **Audit Loglarını Kontrol Etme:** `auditd` servisinin çalıştığından emin olun (`sudo systemctl status auditd`). Ardından logları inceleyin:
    ```bash
    # Son SELinux engellemelerini göster
    sudo ausearch -m AVC -ts recent 

    # Anlaşılır bir açıklama için audit2why kullanın
    sudo ausearch -m AVC -ts recent | audit2why
    ```
2.  **Permissive Moda Geçme:** Sorunun gerçekten SELinux kaynaklı olup olmadığını anlamak için geçici olarak permissive moda geçin (`sudo setenforce 0`) ve uygulamayı tekrar deneyin. Eğer sorun düzelirse, sorun SELinux politikasıyla ilgilidir. **İşi bitince tekrar `sudo setenforce 1` ile enforcing moda dönmeyi unutmayın!**
3.  **Çözüm Yolları:**
    *   **Dosya Bağlamı:** En yaygın sorun, dosyaların yanlış güvenlik bağlamına sahip olmasıdır. `restorecon` genellikle sorunu çözer. Gerekirse `semanage fcontext` ile kalıcı kural ekleyin.
    *   **Boolean:** İlgili işlemi yapmaya izin veren bir boolean olabilir. `getsebool -a | grep <ilgili_kelime>` ile arama yapıp `setsebool -P <boolean> on` ile etkinleştirin.
    *   **Port Bağlamı:** Uygulama standart olmayan bir port kullanıyorsa, `semanage port` ile o porta uygun bağlamı atayın.
    *   **Özel Politika Modülü:** Yukarıdakiler işe yaramazsa veya daha karmaşık bir izin gerekiyorsa, `audit2allow` aracı ile loglardaki engellemelere izin veren özel bir politika modülü oluşturulabilir (ileri düzey konu).

SELinux güçlü bir güvenlik katmanıdır. Kapatmak yerine, karşılaşılan sorunları anlayıp politikayı uygun şekilde ayarlamak genellikle daha güvenli bir yaklaşımdır.
