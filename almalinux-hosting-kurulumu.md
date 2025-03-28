# AlmaLinux ile Hosting Ortamı Kurulumu (Örnek)

Bu bölümde, modern bir RHEL tabanlı dağıtım olan AlmaLinux (veya Rocky Linux / RHEL 9+) üzerine temel bir web hosting ortamının (Apache/Nginx, MariaDB, PHP-FPM) nasıl kurulabileceğine dair örnek adımlar sunulmaktadır.

**Not:** Bu rehber temel bir kurulumu kapsar. Üretim ortamları için daha detaylı güvenlik sıkılaştırmaları, performans optimizasyonları ve yedekleme stratejileri gereklidir.

## 1. Sistem Güncelleme ve Temel Paketler

```bash
# Sistemi en güncel hale getir
sudo dnf update -y

# Gerekli temel araçları ve depoları kur
sudo dnf install epel-release yum-utils wget bind-utils lsof atop net-tools policycoreutils-python-utils -y 
```

## 2. Güvenlik Ayarları

*   **SELinux:** `Enforcing` modda bırakılması önerilir. Sorun yaşanırsa, logları (`/var/log/audit/audit.log`) kontrol edin ve `audit2why`, `audit2allow` veya `setsebool` ile gerekli izinleri ayarlayın. Geçici olarak `sudo setenforce 0` ile permissive moda alıp test edebilirsiniz.
    ```bash
    # SELinux durumunu kontrol et
    sestatus
    getenforce
    ```
*   **Firewalld:** Gerekli servisler için portları açın.
    ```bash
    # SSH, HTTP ve HTTPS servislerine kalıcı olarak izin ver
    sudo firewall-cmd --permanent --add-service={ssh,http,https}

    # Değişiklikleri uygula
    sudo firewall-cmd --reload

    # İzin verilenleri listele
    sudo firewall-cmd --list-all
    ```
*   **SSH Güvenliği:** `/etc/ssh/sshd_config` dosyasında en azından şunları yapın:
    *   `PermitRootLogin no`
    *   `PasswordAuthentication no` (SSH anahtarı kullanılıyorsa)
    *   Gerekirse `AllowUsers` veya `AllowGroups` ile erişimi kısıtlayın.
    *   `sudo systemctl restart sshd`

## 3. Web Sunucusu Kurulumu (Apache veya Nginx)

**Seçenek A: Apache (`httpd`)**
```bash
sudo dnf install httpd mod_ssl -y
sudo systemctl enable --now httpd 
```

**Seçenek B: Nginx**
```bash
sudo dnf install nginx -y
sudo systemctl enable --now nginx
```
(Nginx için SSL yapılandırması genellikle ayrı yapılır).

## 4. Veritabanı Sunucusu Kurulumu (MariaDB)

```bash
sudo dnf install mariadb-server -y
sudo systemctl enable --now mariadb

# İlk güvenlik ayarlarını yap
sudo mariadb-secure-installation
```

## 5. PHP ve PHP-FPM Kurulumu

AlmaLinux 9 ve sonrası, AppStream deposu üzerinden farklı PHP sürümleri sunar (`dnf module list php`).

```bash
# Mevcut PHP modüllerini listele
dnf module list php

# Örnek: PHP 8.1 modülünü etkinleştir ve kur
sudo dnf module enable php:8.1 -y 
sudo dnf install php php-fpm php-mysqlnd php-gd php-xml php-mbstring php-intl php-opcache -y

# PHP-FPM servisini başlat ve etkinleştir
sudo systemctl enable --now php-fpm
```

## 6. Web Sunucusu ve PHP-FPM Entegrasyonu

*   **Apache ile:**
    *   Gerekli modüllerin (`proxy`, `proxy_fcgi`) yüklü ve aktif olduğundan emin olun.
    *   `/etc/httpd/conf.d/` altında sanal ana bilgisayar yapılandırmanızı oluşturun.
    *   PHP isteklerini PHP-FPM'e yönlendirmek için `<FilesMatch>` veya `ProxyPassMatch` kullanın (genellikle Unix soketi `/run/php-fpm/www.sock` üzerinden):
        ```apache
        <FilesMatch \.php$>
            SetHandler "proxy:unix:/run/php-fpm/www.sock|fcgi://localhost/"
        </FilesMatch>
        ```
    *   Gerekirse SELinux boolean'ını ayarlayın: `sudo setsebool -P httpd_can_network_connect 1` (Eğer TCP soketi kullanılıyorsa) veya dosya izinlerini/bağlamlarını kontrol edin.
    *   `sudo systemctl restart httpd`

*   **Nginx ile:**
    *   `/etc/nginx/conf.d/` altında sunucu bloğu (`server { ... }`) yapılandırmanızı oluşturun.
    *   PHP `location` bloğunda `fastcgi_pass` direktifini PHP-FPM soketine yönlendirin:
        ```nginx
        location ~ \.php$ {
            include /etc/nginx/fastcgi_params; 
            fastcgi_pass unix:/run/php-fpm/www.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
        ```
    *   Gerekirse SELinux veya dosya/soket izinlerini kontrol edin.
    *   `sudo systemctl restart nginx`

## 7. Web Sitesi Dosyaları ve İzinler

*   Web sitesi kullanıcısı oluşturun (örn. `webuser`).
*   Site dosyaları için dizin oluşturun (örn. `/var/www/example.com/html`).
*   Dosyaların sahipliğini ve izinlerini ayarlayın (örn. `sudo chown -R webuser:webuser /var/www/example.com`, `sudo chmod -R 755 /var/www/example.com`).
*   SELinux bağlamlarını kontrol edin ve gerekirse düzeltin:
    ```bash
    # Web içeriği için doğru bağlamı ayarla
    sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/example.com(/.*)?"
    sudo restorecon -Rv /var/www/example.com
    ```

Bu adımlar, AlmaLinux üzerinde temel bir LAMP (Linux, Apache, MariaDB, PHP) veya LEMP (Linux, Nginx, MariaDB, PHP) yığını kurmak için bir başlangıç noktasıdır. Her adımın detayları ve ek yapılandırma seçenekleri ilgili bölümlerde daha ayrıntılı olarak ele alınmıştır.
