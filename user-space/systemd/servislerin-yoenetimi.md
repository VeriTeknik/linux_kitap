# Servislerin Yönetimi (`systemctl`)

`systemd` init sisteminde, servisleri ve diğer sistem birimlerini (units) yönetmek için kullanılan ana komut `systemctl`'dir. Bu komut, eski `service`, `chkconfig`, `update-rc.d` gibi araçların işlevlerini birleştirir ve daha tutarlı bir arayüz sunar. Komutların çoğu `sudo` yetkisi gerektirir.

## Temel Servis Yönetimi Komutları

Aşağıda `<servis_adı>` yerine yönetmek istediğiniz servisin adını (örn. `sshd`, `nginx`, `apache2`) yazmalısınız. `.service` uzantısı genellikle isteğe bağlıdır.

*   **Servisi Başlatma (`start`):**
    Bir servisi hemen başlatır.
    ```bash
    sudo systemctl start <servis_adı>
    ```

*   **Servisi Durdurma (`stop`):**
    Çalışan bir servisi hemen durdurur.
    ```bash
    sudo systemctl stop <servis_adı>
    ```

*   **Servisi Yeniden Başlatma (`restart`):**
    Servisi durdurur ve ardından yeniden başlatır. Yapılandırma değişikliklerinden sonra veya servisin takılması durumunda kullanılır.
    ```bash
    sudo systemctl restart <servis_adı>
    ```

*   **Yapılandırmayı Yeniden Yükleme (`reload`):**
    Servise, yapılandırma dosyalarını yeniden okumasını söyler. Bu işlem genellikle servisi durdurmadan yapılır, ancak tüm servisler `reload` işlemini desteklemez.
    ```bash
    sudo systemctl reload <servis_adı>
    ```
    Eğer servis `reload` desteklemiyorsa, genellikle `restart` kullanmak gerekir.

*   **Koşullu Yeniden Başlatma (`try-restart`):**
    Sadece servis zaten çalışıyorsa yeniden başlatır.
    ```bash
    sudo systemctl try-restart <servis_adı>
    ```

*   **Koşullu Yeniden Yükleme (`reload-or-restart`):**
    Servis `reload` destekliyorsa yeniden yükler, desteklemiyorsa yeniden başlatır.
    ```bash
    sudo systemctl reload-or-restart <servis_adı>
    ```

## Servis Durumunu Kontrol Etme

*   **Detaylı Durum (`status`):**
    Bir servisin mevcut durumunu (aktif/pasif, etkin/devre dışı), PID'sini, bellek/CPU kullanımını ve en son günlük (log) mesajlarını gösterir.
    ```bash
    systemctl status <servis_adı>
    ```
    Çıktıdan `q` tuşu ile çıkılır.

*   **Aktif mi? (`is-active`):**
    Servisin o anda çalışıp çalışmadığını kontrol eder. Genellikle betiklerde kullanılır. Çıktısı `active` veya `inactive` (veya `activating`, `failed` vb.) olur.
    ```bash
    systemctl is-active <servis_adı>
    ```

*   **Başlangıçta Etkin mi? (`is-enabled`):**
    Servisin sistem başlangıcında otomatik olarak çalışacak şekilde etkinleştirilip etkinleştirilmediğini kontrol eder. Çıktısı `enabled`, `disabled`, `static` (manuel olarak başlatılamaz/durdurulamaz ama başka unit'ler tarafından kullanılabilir), `masked` vb. olur.
    ```bash
    systemctl is-enabled <servis_adı>
    ```

*   **Başarısız mı? (`is-failed`):**
    Servisin hata durumunda olup olmadığını kontrol eder.
    ```bash
    systemctl is-failed <servis_adı>
    ```

## Servisleri Başlangıçta Etkinleştirme/Devre Dışı Bırakma

*   **Etkinleştirme (`enable`):**
    Bir servisin sistem başlangıcında otomatik olarak çalışmasını sağlar. Gerekli sembolik linkleri oluşturur.
    ```bash
    sudo systemctl enable <servis_adı>
    ```
    `--now` seçeneği ile hem etkinleştirip hem de hemen başlatabilirsiniz:
    ```bash
    sudo systemctl enable --now <servis_adı>
    ```

*   **Devre Dışı Bırakma (`disable`):**
    Bir servisin sistem başlangıcında otomatik olarak çalışmasını engeller. Gerekli sembolik linkleri kaldırır. Servis o anda çalışıyorsa durdurmaz.
    ```bash
    sudo systemctl disable <servis_adı>
    ```
    `--now` seçeneği ile hem devre dışı bırakıp hem de hemen durdurabilirsiniz:
    ```bash
    sudo systemctl disable --now <servis_adı>
    ```

*   **Maskeleme (`mask`):**
    Bir servisi tamamen devre dışı bırakır. Servis manuel olarak veya başka bir servisin bağımlılığı olarak bile başlatılamaz. Bu işlem, genellikle `/etc/systemd/system/` altına servisin unit dosyasını `/dev/null`'a yönlendiren bir sembolik link oluşturarak yapılır.
    ```bash
    sudo systemctl mask <servis_adı>
    ```

*   **Maskeyi Kaldırma (`unmask`):**
    Maskelenmiş bir servisin maskesini kaldırır, ancak servisi otomatik olarak etkinleştirmez.
    ```bash
    sudo systemctl unmask <servis_adı>
    ```

`systemctl`, systemd sistemlerde servisleri ve diğer unit'leri yönetmek için merkezi ve güçlü bir araçtır.
