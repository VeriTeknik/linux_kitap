# PHP-FPM

İntenet'in ilk yıllarında web sitelerini dinamikleştirmek için ya "Server Side Include"lar ya da CGI prosesleri kullanılıyordu, SSI kolay olmasına rağmen fonksiyonelitesi çok azdı, CGI ve perl çok güçlü diller olsada, karmaşık yapıları, zor yazılması ve hatanın trace edilmesindeki güçlükler nedeniyle çok tercih edilemiyordu. Öyle ki, basit betikleriniz için C/C++ üzerinde kendi betik yorumlayıcınızı yazabilir, CGI'dan daha az karmaşık bir sistem çıkarabilirdiniz.

PHP, doksanlı yıllarda çıkagelmiş bu C/C++'ta yazılmış yorumlayıcılardan biridir. Açılımı "Kişisel Ana Sayfa" (Personal Home Page) olan PHP zaman içerisinde gelişmiş, obje oryantasyonlu bir dil haline gelmiştir.
PHP güçlü bir betik (script) yorumlayıcı (interpreter) olmasına rağmen, UNIX standartlarında olmayan dosya okuma, yazma ve çalıştırma yapısı ve bağlı olduğu kullanıcı altında çalışmaması nedeniyle bir burada PHP-FPM şeklinde yüklenmiş PHP'yi göreceğiz.

PHP genelde tüm işletim sistemlerinde bulunur ancak, güncelliği sistemden sisteme değişir. Biz bu çalışmada güncelliği korumak için Centos 7 tercih ettik, biz bu yazıyı yazarken PHP 5.4.16 yum repolarında bulunmaktaydı. Yüklemek için yapmanız gereken:

```bash
yum install php php-mysql php-fpm php-gd httpd net-tools lsof
systemctl enable httpd.service

``` 
Apache ve PHP'yi yüklediğimizde /etc/httpd/conf.d içerisinde php.conf dosyasının oluştuğunu görebiliriz, PHP-FPM için bu dosyaya ihtiyacımız yok ve kaldırılması gerekmekte.

```bash
cp /etc/php-fpm.d/www.conf /etc/php-fpm.d/web.conf

listen.owner = web
listen.group = web

user = web
group = web
```

```bash
systemctl start php-fpm
systemctl enable php-fpm
systemctl start httpd.service
```




