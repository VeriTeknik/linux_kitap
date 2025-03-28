# Ubuntu ile Hosting Ortamı Kurulumu (Örnek)

Bu bölümde, popüler bir Debian tabanlı dağıtım olan Ubuntu LTS (Uzun Süreli Destek) üzerine temel bir web hosting ortamının (Apache/Nginx, MariaDB/MySQL, PHP-FPM) nasıl kurulabileceğine dair örnek adımlar sunulmaktadır.

**Not:** Bu rehber temel bir kurulumu kapsar. Üretim ortamları için daha detaylı güvenlik sıkılaştırmaları, performans optimizasyonları ve yedekleme stratejileri gereklidir. Ubuntu'nun farklı sürümlerinde komutlar veya varsayılan ayarlar küçük farklılıklar gösterebilir.

## 1. Sistem Güncelleme ve Temel Paketler

```bash
# Sistemi en güncel hale getir
sudo apt update
sudo apt upgrade -y

# Gerekli temel araçları kur
sudo apt install wget curl bind9-utils dnsutils net-tools lsof atop ufw -y
```
(`bind9-utils` `dig` içerir, `dnsutils` `nslookup` içerir, `ufw` güvenlik duvarı aracıdır).

## 2. Güvenlik Ayarları

*   **UFW (Uncomplicated Firewall):** Ubuntu'nun varsayılan güvenlik duvarı aracıdır. Kullanımı `firewalld`'ye göre daha basittir.
    ```bash
    # UFW durumunu kontrol et (genellikle başlangıçta inaktiftir)
    sudo ufw status

    # Varsayılan olarak gelenleri engelle, gidenlere izin ver
    sudo ufw default deny incoming
    sudo ufw default allow outgoing

    # SSH, HTTP ve HTTPS'e izin ver
    sudo ufw allow ssh  # veya sudo ufw allow 22/tcp
    sudo ufw allow http # veya sudo ufw allow 80/tcp
    sudo ufw allow https # veya sudo ufw allow 443/tcp

    # UFW'yi etkinleştir
    sudo ufw enable 

    # Durumu tekrar kontrol et
    sudo ufw status verbose
    ```
*   **SSH Güvenliği:** `/etc/ssh/sshd_config` dosyasında en azından şunları yapın:
    *   `PermitRootLogin no`
    *   `PasswordAuthentication no` (SSH anahtarı kullanılıyorsa)
    *   Gerekirse `AllowUsers` veya `AllowGroups` ile erişimi kısıtlayın.
    *   `sudo systemctl restart sshd`
*   **AppArmor:** Ubuntu, SELinux yerine AppArmor'ı MAC sistemi olarak kullanır. Genellikle varsayılan profiller yeterlidir, ancak özel durumlar için profiller düzenlenebilir (`/etc/apparmor.d/`).

## 3. Web Sunucusu Kurulumu (Apache veya Nginx)

**Seçenek A: Apache (`apache2`)**
```bash
sudo apt install apache2 -y
sudo systemctl enable --now apache2 
# Gerekli modülleri etkinleştir (örn. SSL, rewrite, proxy_fcgi)
sudo a2enmod ssl rewrite proxy proxy_fcgi
sudo systemctl restart apache2
```

**Seçenek B: Nginx**
```bash
sudo apt install nginx -y
sudo systemctl enable --now nginx
```

## 4. Veritabanı Sunucusu Kurulumu (MariaDB veya MySQL)

Ubuntu depolarında genellikle hem MariaDB hem de MySQL bulunur.

**Seçenek A: MariaDB (Genellikle Önerilen)**
```bash
sudo apt install mariadb-server -y
# Servis genellikle otomatik başlar ve etkinleştirilir
# sudo systemctl status mariadb

# İlk güvenlik ayarlarını yap
sudo mariadb-secure-installation
```

**Seçenek B: MySQL**
```bash
sudo apt install mysql-server -y
# Servis genellikle otomatik başlar ve etkinleştirilir
# sudo systemctl status mysql

# İlk güvenlik ayarlarını yap
sudo mysql_secure_installation
```

## 5. PHP ve PHP-FPM Kurulumu

Ubuntu depoları genellikle güncel bir PHP sürümü içerir. Daha spesifik veya en yeni sürümler için Ondřej Surý PPA'sı yaygın olarak kullanılır.

```bash
# Depodaki varsayılan sürümü kurma (örn. Ubuntu 22.04 için PHP 8.1 olabilir)
sudo apt install php-fpm php-mysql php-gd php-xml php-mbstring php-intl php-opcache -y
# Kurulan sürümü kontrol edin, örn: php -v

# PHP-FPM servisini başlat ve etkinleştir (sürüm numarasını kontrol edin)
sudo systemctl enable --now php8.1-fpm # Örnek sürüm
```

## 6. Web Sunucusu ve PHP-FPM Entegrasyonu

*   **Apache ile:**
    *   Gerekli modüllerin (`proxy_fcgi`, `setenvif`) etkin olduğundan emin olun (`sudo a2enmod proxy_fcgi setenvif`).
    *   `/etc/apache2/sites-available/` altında sanal ana bilgisayar yapılandırmanızı oluşturun.
    *   PHP isteklerini PHP-FPM'e yönlendirmek için `SetHandler` kullanın (genellikle Unix soketi `/run/php/php<sürüm>-fpm.sock` üzerinden):
        ```apache
        <FilesMatch \.php$>
            SetHandler "proxy:unix:/run/php/php8.1-fpm.sock|fcgi://localhost/"
        </FilesMatch>
        ```
    *   Siteyi etkinleştirin: `sudo a2ensite <site_dosya_adı>`
    *   `sudo systemctl restart apache2`

*   **Nginx ile:**
    *   `/etc/nginx/sites-available/` altında sunucu bloğu (`server { ... }`) yapılandırmanızı oluşturun.
    *   PHP `location` bloğunda `fastcgi_pass` direktifini PHP-FPM soketine yönlendirin:
        ```nginx
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php8.1-fpm.sock; 
            # fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; # Genellikle fastcgi-php.conf içinde bulunur
        }
        ```
    *   Siteyi etkinleştirin (genellikle `/etc/nginx/sites-enabled/` altına sembolik link oluşturarak).
    *   `sudo systemctl restart nginx`

## 7. Web Sitesi Dosyaları ve İzinler

*   Web sitesi kullanıcısı oluşturun (örn. `webuser`).
*   Site dosyaları için dizin oluşturun (örn. `/var/www/example.com/html`).
*   Dosyaların sahipliğini ve izinlerini ayarlayın (örn. `sudo chown -R webuser:www-data /var/www/example.com`, `sudo chmod -R 775 /var/www/example.com`). `www-data` genellikle Apache/Nginx'in çalıştığı gruptur.
*   Gerekirse AppArmor profillerini kontrol edin.

Bu adımlar, Ubuntu üzerinde temel bir LAMP veya LEMP yığını kurmak için bir başlangıç noktasıdır. Dağıtım sürümleri ve kullanılan yazılım versiyonlarına göre küçük farklılıklar olabilir.
