# PHP-FPM

İntenet'in ilk yıllarında web sitelerini dinamikleştirmek için ya "Server Side Include"lar ya da CGI prosesleri kullanılıyordu, SSI kolay olmasına rağmen fonksiyonelitesi çok azdı, CGI ve perl çok güçlü diller olsada, karmaşık yapıları, zor yazılması ve hatanın trace edilmesindeki güçlükler nedeniyle çok tercih edilemiyordu. Öyle ki, basit betikleriniz için C/C++ üzerinde kendi betik yorumlayıcınızı yazabilir, CGI'dan daha az karmaşık bir sistem çıkarabilirdiniz.

PHP, doksanlı yıllarda çıkagelmiş bu C/C++'ta yazılmış yorumlayıcılardan biridir. Açılımı "Kişisel Ana Sayfa" (Personal Home Page) olan PHP zaman içerisinde gelişmiş, obje oryantasyonlu bir dil haline gelmiştir. PHP güçlü bir betik (script) yorumlayıcı (interpreter) olmasına rağmen, UNIX standartlarında olmayan dosya okuma, yazma ve çalıştırma yapısı ve bağlı olduğu kullanıcı altında çalışmaması nedeniyle bir burada PHP-FPM şeklinde yüklenmiş PHP'yi göreceğiz.

PHP genelde tüm işletim sistemlerinde bulunur ancak, güncelliği sistemden sisteme değişir. Biz bu çalışmada güncelliği korumak için Centos 7 tercih ettik, biz bu yazıyı yazarken PHP 5.4.16 yum repolarında bulunmaktaydı. Yüklemek için yapmanız gereken:

```bash
yum install php php-mysql php-fpm php-gd httpd net-tools lsof mysql-server
```

Apache ve PHP'yi yüklediğimizde /etc/httpd/conf.d içerisinde php.conf dosyasının oluştuğunu görebiliriz, PHP-FPM için bu dosyaya ihtiyacımız yok ve kaldırılması gerekmekte.

```bash
systemctl start php-fpm
systemctl enable php-fpm
systemctl enable httpd.service
systemctl start httpd.service
```

Apache VirtualHost dosyasını aşağıdaki gibi düzenleyebilirsiniz, yeni Apache versiyonlarında NameVirtualHost direktifi bulunmuyor:

```bash
vi /etc/httpd/conf.d/z_web.conf
```

dosya şu şekilde olmalı

```bash
<VirtualHost __IP_ADRESI__:80>
    ServerAdmin root@veriteknik.com
    DocumentRoot /home/web/public_html
    ServerName apachetest.veriteknik.com
    ServerAlias apachetest2.veriteknik.local __IP_ADRESI__
    ErrorLog /home/web/logs/error.log
    CustomLog /home/web/logs/access.log common

<IfModule proxy_module>
  ProxyPassMatch ^/(.*\.php(/.*)?)$ fcgi://127.0.0.1:9001/home/web/public_html/$1
</IfModule>

DirectoryIndex index.php

    <Directory "/home/web/public_html">
        order deny,allow
        allow from all
        Options +FollowSymLinks
    </Directory>
</VirtualHost>
```

ve fcgi ayarlamalarımızı da yapalım, vi /etc/php-fpm.d/web.conf

```bash
; Start a new pool named 'www'.
[web]
listen = 127.0.0.1:9001
listen.owner = web
listen.group = web
user = web
group = web
```

NOT: yukarıda sadece değişiklikler verilmiştir.

## Wordpress kurulumu

```bash
su -l web
cd public_html/
wget https://tr.wordpress.org/wordpress-4.4-tr_TR.zip
unzip wordpress-4.4-tr_TR.zip 
rm index.php 
mv wordpress/* .
rmdir wordpress
rm wordpress-4.4-tr_TR.zip
```
