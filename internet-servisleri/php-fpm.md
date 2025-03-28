# PHP-FPM (FastCGI Process Manager)

PHP, web geliştirmede yaygın olarak kullanılan popüler bir betik dilidir. PHP kodunu bir web sunucusu (Apache, Nginx vb.) ile çalıştırmanın çeşitli yolları vardır. Eski yöntemlerden biri Apache'nin `mod_php` modülünü kullanmaktı, ancak bu yöntem genellikle daha az esnek ve performanslıdır.

Modern ve önerilen yaklaşım, **PHP-FPM** (FastCGI Process Manager) kullanmaktır. PHP-FPM, PHP için alternatif bir FastCGI daemon'ıdır ve web sunucusundan bağımsız olarak çalışır. Web sunucusu, gelen PHP isteklerini FastCGI protokolü üzerinden PHP-FPM'e iletir, PHP-FPM isteği işler ve sonucu web sunucusuna geri gönderir.

**PHP-FPM'in Avantajları:**

*   **Performans:** Genellikle `mod_php`'den daha iyi performans sunar.
*   **Esneklik:** Farklı web siteleri veya uygulamalar için ayrı PHP-FPM havuzları (pools) tanımlanabilir. Her havuz farklı kullanıcı/grup kimliğiyle, farklı PHP ayarlarıyla (`php.ini`) ve farklı kaynak limitleriyle çalıştırılabilir. Bu, güvenlik ve izolasyonu artırır.
*   **Kaynak Yönetimi:** İşlem yönetimi (process management - `pm`) ayarları (`static`, `dynamic`, `ondemand`) ile kaynak kullanımı daha iyi kontrol edilebilir.
*   **Web Sunucusu Bağımsızlığı:** Hem Apache hem de Nginx gibi farklı web sunucularıyla kullanılabilir.

## Kurulum

PHP-FPM ve ilgili PHP paketleri genellikle dağıtımın paket yöneticisi ile kurulur. PHP sürümü (örn. 8.1, 8.2) dağıtıma göre değişir. Belirli bir sürümü kurmak için ek depolar (örn. REMI, Ondřej Surý PPA) gerekebilir.

*   **RHEL Tabanlı (dnf):** (Genellikle `remi-release` deposu etkinleştirildikten sonra)
    ```bash
    # Örnek: PHP 8.2 ve yaygın modülleri kurma
    sudo dnf module enable php:remi-8.2 -y 
    sudo dnf install php php-fpm php-mysqlnd php-gd php-xml php-mbstring php-intl -y
    ```
*   **Debian Tabanlı (apt):** (Genellikle `ondrej/php` PPA'sı eklendikten sonra veya dağıtımın kendi sürümü)
    ```bash
    # Örnek: PHP 8.2 ve yaygın modülleri kurma
    sudo apt update
    sudo apt install php8.2 php8.2-fpm php8.2-mysql php8.2-gd php8.2-xml php8.2-mbstring php8.2-intl -y
    ```

## Servis Yönetimi (`systemctl`)

PHP-FPM servisi systemd ile yönetilir. Servis adı genellikle PHP sürümünü içerir.

```bash
# Servisi başlatma (Örnek: PHP 8.2)
sudo systemctl start php8.2-fpm 

# Sistem başlangıcında otomatik çalışmasını sağlama
sudo systemctl enable php8.2-fpm

# Servisin durumunu kontrol etme
sudo systemctl status php8.2-fpm

# Yapılandırma değişikliğinden sonra yeniden başlatma
sudo systemctl restart php8.2-fpm 
```

## PHP-FPM Havuz (Pool) Yapılandırması

PHP-FPM, farklı ayarlar ve kullanıcılarla çalışabilen **havuzlar (pools)** kullanır. Varsayılan havuz genellikle `www` olarak adlandırılır ve yapılandırması `/etc/php/<sürüm>/fpm/pool.d/www.conf` (Debian/Ubuntu) veya `/etc/php-fpm.d/www.conf` (RHEL/CentOS) gibi dosyalarda bulunur.

Her web sitesi veya uygulama için ayrı bir havuz oluşturmak iyi bir pratiktir.

**Örnek Havuz Yapılandırması (`/etc/php-fpm.d/example.com.conf`):**
```ini
[example.com] ; Havuz adı

; Çalıştırılacak Kullanıcı ve Grup
user = example_user
group = example_group

; Dinlenecek Soket (Unix soketi önerilir)
listen = /run/php-fpm/example.com.sock 

; Soket Sahibi ve Grubu (Web sunucusunun erişebilmesi için önemlidir)
listen.owner = nginx ; veya apache, httpd, www-data vb.
listen.group = nginx
listen.mode = 0660

; İşlem Yönetimi (Process Management)
pm = dynamic ; veya ondemand, static
pm.max_children = 10     ; Aynı anda çalışacak maksimum işlem sayısı
pm.start_servers = 2     ; Başlangıçta çalıştırılacak işlem sayısı (dynamic için)
pm.min_spare_servers = 1 ; Minimum boşta bekleyecek işlem sayısı (dynamic için)
pm.max_spare_servers = 3 ; Maksimum boşta bekleyecek işlem sayısı (dynamic için)
; pm.process_idle_timeout = 10s; ; Boştaki işlemin ne kadar süre sonra kapatılacağı (ondemand için)
; pm.max_requests = 500    ; Bir işlemin kaç istekten sonra yeniden başlatılacağı

; PHP Ayarları (isteğe bağlı, php.ini'yi geçersiz kılar)
; php_admin_value[memory_limit] = 128M
; php_admin_flag[log_errors] = on
; php_admin_value[error_log] = /var/log/php-fpm/example.com-error.log
```
Yeni bir havuz dosyası oluşturduktan veya mevcut olanı değiştirdikten sonra PHP-FPM servisini yeniden başlatmanız (`sudo systemctl restart php<sürüm>-fpm`) gerekir.

## Web Sunucusu Yapılandırması

Web sunucusunun, PHP dosyalarına gelen istekleri ilgili PHP-FPM havuzunun dinlediği sokete (veya porta) FastCGI protokolü üzerinden iletmesi gerekir.

**1. Apache ile PHP-FPM (mod_proxy_fcgi kullanarak)**

Gerekli Apache modüllerini etkinleştirin:
```bash
# Debian/Ubuntu
sudo a2enmod proxy proxy_fcgi setenvif

# RHEL/CentOS (genellikle httpd ile gelir, conf.modules.d/ içinde kontrol edin)
# Gerekirse: sudo systemctl restart httpd 
sudo systemctl restart apache2 # Debian/Ubuntu için
```

Apache sanal ana bilgisayar yapılandırmasına (`<VirtualHost>` bloğu içine) aşağıdaki gibi bir direktif ekleyin (Unix soketi kullanarak):
```apache
# .php dosyalarına gelen istekleri PHP-FPM soketine yönlendir
<FilesMatch \.php$>
    SetHandler "proxy:unix:/run/php-fpm/example.com.sock|fcgi://localhost/"
</FilesMatch>

# Veya TCP soketi kullanılıyorsa (örn. 127.0.0.1:9001):
# <FilesMatch \.php$>
#    SetHandler "proxy:fcgi://127.0.0.1:9001"
# </FilesMatch>

# index.php'nin varsayılan dizin indeksi olmasını sağla
DirectoryIndex index.php index.html
```
Yapılandırmayı kontrol edip Apache'yi yeniden yükleyin:
```bash
sudo apachectl configtest
sudo systemctl reload apache2 # veya httpd
```

**2. Nginx ile PHP-FPM**

Nginx sanal ana bilgisayar yapılandırmasındaki (`server` bloğu içine) `location` bloğunu PHP isteklerini işleyecek şekilde düzenleyin (Unix soketi kullanarak):
```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/example.com/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf; # Dağıtımınızdaki standart fastcgi parametrelerini içerir

        # PHP-FPM soketine yönlendir
        fastcgi_pass unix:/run/php-fpm/example.com.sock;

        # Veya TCP soketi kullanılıyorsa:
        # fastcgi_pass 127.0.0.1:9001;
    }

    # .htaccess dosyalarına erişimi engelle (güvenlik)
    location ~ /\.ht {
        deny all;
    }
}
```
Yapılandırmayı kontrol edip Nginx'i yeniden yükleyin:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

PHP-FPM, modern PHP uygulamalarını web sunucularıyla entegre etmek için standart ve performanslı bir yöntemdir. Farklı havuzlar kullanarak uygulamalar arasında daha iyi izolasyon ve kaynak yönetimi sağlar.
