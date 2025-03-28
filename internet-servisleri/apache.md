# Apache HTTP Sunucusu

Apache HTTP Sunucusu (genellikle kısaca "Apache" veya RHEL tabanlı sistemlerde "httpd" olarak anılır), dünyanın en yaygın kullanılan açık kaynaklı web sunucusu yazılımlarından biridir. Apache Software Foundation tarafından geliştirilir ve Apache Lisansı altında dağıtılır. HTTP ve HTTPS protokolleri üzerinden web içeriği sunmak için kullanılır.

## Temel Özellikler

*   **Modüler Yapı:** Apache'nin işlevselliği, dinamik olarak yüklenebilen modüller aracılığıyla genişletilebilir (örn. SSL/TLS, URL yeniden yazma, proxy, kimlik doğrulama modülleri).
*   **Esnek Yapılandırma:** Direktif tabanlı yapılandırma dosyaları (`httpd.conf`, `.htaccess`) ile detaylı kontrol imkanı sunar.
*   **Sanal Ana Bilgisayarlar (Virtual Hosts):** Tek bir sunucu üzerinde birden fazla web sitesini barındırmaya olanak tanır.
*   **Platform Bağımsızlığı:** Çeşitli Unix benzeri sistemlerde (Linux, BSD) ve Windows üzerinde çalışabilir.
*   **Geniş Topluluk Desteği:** Yaygın kullanımı sayesinde geniş bir kullanıcı topluluğuna ve bol miktarda belgeye sahiptir.

## Kurulum (Paket Yöneticisi ile - Önerilen)

Apache'yi kaynak koddan derlemek yerine, dağıtımınızın paket yöneticisini kullanmak **şiddetle tavsiye edilir**. Bu, kurulumu basitleştirir, güncellemeleri kolaylaştırır ve sistemle uyumluluğu sağlar.

*   **RHEL Tabanlı (CentOS Stream, Rocky, AlmaLinux, Fedora):**
    Paket adı genellikle `httpd`'dir. SSL modülü (`mod_ssl`) ayrı kurulabilir.
    ```bash
    sudo dnf install httpd mod_ssl -y
    ```
*   **Debian Tabanlı (Debian, Ubuntu, Mint):**
    Paket adı genellikle `apache2`'dir. SSL modülü genellikle varsayılan olarak gelir veya `a2enmod ssl` ile etkinleştirilir.
    ```bash
    sudo apt update
    sudo apt install apache2 -y
    ```

## Servis Yönetimi (`systemctl`)

Apache servisi systemd ile yönetilir. Servis adı dağıtıma göre değişir:

*   **RHEL Tabanlı:** `httpd.service`
*   **Debian Tabanlı:** `apache2.service`

```bash
# Servisi başlatma
sudo systemctl start httpd # veya apache2

# Servisi durdurma
sudo systemctl stop httpd # veya apache2

# Servisi yeniden başlatma
sudo systemctl restart httpd # veya apache2

# Yapılandırmayı yeniden yükleme (daha hızlı)
sudo systemctl reload httpd # veya apache2

# Servisin durumunu kontrol etme
sudo systemctl status httpd # veya apache2

# Sistem başlangıcında otomatik çalışmasını sağlama
sudo systemctl enable httpd # veya apache2

# Sistem başlangıcında otomatik çalışmasını engelleme
sudo systemctl disable httpd # veya apache2
```

## Yapılandırma Dosyaları ve Yapısı

Apache'nin yapılandırma dosyalarının konumu ve organizasyonu dağıtım aileleri arasında farklılık gösterir:

**1. RHEL Tabanlı Sistemler (`/etc/httpd/`)**

*   **Ana Yapılandırma:** `/etc/httpd/conf/httpd.conf`
*   **Modül Yapılandırmaları:** `/etc/httpd/conf.modules.d/`
*   **Ek Yapılandırmalar/Sanal Ana Bilgisayarlar:** `/etc/httpd/conf.d/`
    *   Genellikle sanal ana bilgisayar (Virtual Host) tanımları bu dizine `.conf` uzantılı dosyalar halinde eklenir (örn. `/etc/httpd/conf.d/vhost-example.com.conf`).
    *   `httpd.conf` dosyasının sonunda genellikle `IncludeOptional conf.d/*.conf` satırı bulunur.

**2. Debian Tabanlı Sistemler (`/etc/apache2/`)**

*   **Ana Yapılandırma:** `/etc/apache2/apache2.conf`
*   **Portlar:** `/etc/apache2/ports.conf`
*   **Modüller:**
    *   Mevcut Modüller: `/etc/apache2/mods-available/`
    *   Etkin Modüller: `/etc/apache2/mods-enabled/` (buradakiler `mods-available`'a sembolik linklerdir)
    *   Etkinleştirme/Devre Dışı Bırakma: `a2enmod <modül_adı>`, `a2dismod <modül_adı>`
*   **Site Yapılandırmaları (Sanal Ana Bilgisayarlar):**
    *   Mevcut Siteler: `/etc/apache2/sites-available/` (örn. `example.com.conf`)
    *   Etkin Siteler: `/etc/apache2/sites-enabled/` (buradakiler `sites-available`'a sembolik linklerdir)
    *   Etkinleştirme/Devre Dışı Bırakma: `a2ensite <site_dosya_adı>`, `a2dissite <site_dosya_adı>`
*   **Ek Yapılandırma Parçaları:**
    *   Mevcut Yapılandırmalar: `/etc/apache2/conf-available/`
    *   Etkin Yapılandırmalar: `/etc/apache2/conf-enabled/`
    *   Etkinleştirme/Devre Dışı Bırakma: `a2enconf <conf_dosya_adı>`, `a2disconf <conf_dosya_adı>`

Debian tabanlı sistemlerdeki bu yapı, yapılandırmayı daha modüler hale getirir ve siteleri/modülleri kolayca etkinleştirip devre dışı bırakmayı sağlar.

## Temel Sanal Ana Bilgisayar (Virtual Host) Yapılandırması (Apache 2.4+)

Aşağıda, `example.com` alan adı için basit bir sanal ana bilgisayar tanımı örneği verilmiştir (Apache 2.4 ve sonrası sözdizimi ile).

**RHEL Tabanlı (`/etc/httpd/conf.d/example.com.conf`):**
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@example.com
    ServerName example.com
    ServerAlias www.example.com # İsteğe bağlı ek alan adları

    DocumentRoot /var/www/example.com/html # Web sitesi dosyalarının konumu

    ErrorLog /var/log/httpd/example.com-error.log
    CustomLog /var/log/httpd/example.com-access.log combined

    <Directory "/var/www/example.com/html">
        AllowOverride None # .htaccess kullanımını kontrol eder
        Require all granted # Apache 2.4+ erişim kontrolü
    </Directory>
</VirtualHost>
```

**Debian Tabanlı (`/etc/apache2/sites-available/example.com.conf`):**
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@example.com
    ServerName example.com
    ServerAlias www.example.com

    DocumentRoot /var/www/example.com/html 

    ErrorLog ${APACHE_LOG_DIR}/example.com-error.log
    CustomLog ${APACHE_LOG_DIR}/example.com-access.log combined

    <Directory "/var/www/example.com/html">
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```
*   `<VirtualHost *:80>`: Port 80'e gelen tüm IP adreslerindeki istekler için sanal ana bilgisayarı tanımlar. HTTPS için `<VirtualHost *:443>` kullanılır.
*   `ServerName`: Sanal ana bilgisayarın birincil alan adı.
*   `ServerAlias`: İsteğe bağlı ek alan adları.
*   `DocumentRoot`: Bu siteye ait web dosyalarının bulunduğu kök dizin.
*   `ErrorLog`, `CustomLog`: Bu siteye özel log dosyaları.
*   `<Directory ...>`: Belirtilen dizin için erişim izinleri ve seçenekleri tanımlar.
    *   `AllowOverride None`: `.htaccess` dosyalarının direktifleri geçersiz kılmasına izin verilmez (daha güvenli ve performanslı).
    *   `Require all granted`: Apache 2.4'te dizine erişime izin verir. Eski `Order allow,deny` ve `Allow from all` direktiflerinin yerini almıştır.

Debian tabanlı sistemlerde, bu dosyayı oluşturduktan sonra siteyi etkinleştirmek gerekir:
```bash
sudo a2ensite example.com.conf
sudo systemctl reload apache2
```

## Sunucu Durumu ve Bilgisi (mod_status, mod_info)

Apache'nin çalışma durumunu (aktif bağlantılar, yük vb.) izlemek için `mod_status`, yapılandırma ve modül bilgilerini görmek için `mod_info` modülleri kullanılabilir. Bu modüller genellikle ayrı yapılandırma dosyalarıyla etkinleştirilir ve güvenlik nedeniyle erişimleri kısıtlanmalıdır.

**Örnek Yapılandırma (Debian: `/etc/apache2/conf-available/server-status.conf`):**
```apache
<IfModule mod_status.c>
    <Location /server-status>
        SetHandler server-status
        Require local # Sadece localhost'tan erişime izin ver
        # Require ip 192.168.1.0/24 # Belirli bir IP bloğuna izin ver
    </Location>
    ExtendedStatus On # Daha detaylı bilgi göster
</IfModule>
```
Etkinleştirmek için:
```bash
sudo a2enconf server-status
sudo a2enmod status # Gerekliyse modülü de etkinleştir
sudo systemctl reload apache2
```
Ardından `http://localhost/server-status` adresinden erişilebilir. `mod_info` için de benzer bir yapılandırma (`<Location /server-info>`) yapılır.

## HTTPS/TLS (SSL)

Güvenli web siteleri için HTTPS (HTTP over TLS/SSL) kullanmak zorunludur. Apache'de HTTPS'i etkinleştirmek için `mod_ssl` modülünün aktif olması, 443 portunun dinlenmesi ve bir SSL sertifikasının (genellikle Let's Encrypt ile ücretsiz alınabilir) yapılandırılması gerekir. Sanal ana bilgisayar tanımı `<VirtualHost *:443>` bloğu içinde yapılır ve `SSLEngine on`, `SSLCertificateFile`, `SSLCertificateKeyFile` gibi direktifler eklenir.

Apache, esnekliği ve geniş modül ekosistemi ile güçlü bir web sunucusudur. Alternatifi olarak Nginx, özellikle yüksek trafikli sitelerde ve ters proxy olarak popülerlik kazanmıştır.
