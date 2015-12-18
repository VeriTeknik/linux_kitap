# Apache Web Sunucusu

Apache Web Sunucusu, Apache Vakfı tarafından sunulan, ücretsiz, Apache lisansı ile dağıtılan bir sunucu yazılımıdır. Temel olarak RFC2616* direktifleri ve türevlerinin direktifleri doğrultusunda Web Yayım Hizmeti gerçekleştirir. HTTP OSI ISO katmanı 7'de anlamlı bilgi ve her türlü medyayı içeren dijital yayın hizmetinin temel ilkelerini belirler. Apache bu protokolün içeriğine katkıda bulunmadan, en güvenli, hızlı ve doğru biçimde sunulmasını sağlayan açık kaynak kodlu yazılımlardan biridir. Rakiplerine göre kullanım yaygınlığı, taşınabilirlik ve uyumluluk avantajları vardır. 

Adını Amerikan Yerli'lerinden alan bu yazılım, NCSA httpd'nin 1.3 sürmünü temel alan ilk sürümünü 1995 yılında yapmıştır. Genişletilebilirlik için modüler API sunması rakipleri ile arasındaki farkın açılmasına neden olmuş ve liderlik koltuğunu kapmıştır. Bu yazı yazılırken (2015) halen liderlik Apache'dedir**.

Apache'yi kaynak kodundan kurabileceğiniz gibi, sisteminize uygun binary arşivini de bulmanız mümkündür, kaynak koddan derlemenin günümüzde pek bir faydası olmayacağı aşikar olmakla birlikte, gerçekten kodun içerisine girip kendi yamalarınızı yapmayı düşünüyorsanız, tavsiye ederiz. Biz yine de kaynak kod derlemesinin nasıl yapıldığını göstereceğiz: 

Öncelikle gerekli olabilecek paketleri yükleyin, daha sonra bu paketleri kaldırabilirsiniz:
Yüklemek için
```bash
yum groupinstall "Development Tools"
yum install cmake wget ncurses-devel openssl-devel \
pcre-devel libxml2-devel curl-devel gd-devel libxslt-devel
```
En son kararlı sürümü indirmek için http://ftp.itu.edu.tr/Mirror/Apache/httpd/ adresini ziyaret ediniz.

```bash
wget http://ftp.itu.edu.tr/Mirror/Apache/httpd/httpd-2.2.31.tar.gz
tar zxvf httpd-2.2.31.tar.gz
cd httpd-2.2.31
```
Tüm özellikleri ile çalışan temel Apache sürümü için aşağıdaki config yapısını kullanabilirsiniz:

```bash
./configure \
        "--prefix=/etc/httpd" \
        "--exec-prefix=/etc/httpd" \
        "--bindir=/usr/bin" \
        "--sbindir=/usr/sbin" \
        "--sysconfdir=/etc/httpd/conf" \
        "--enable-so" \
        "--enable-dav" \
        "--enable-info" \
        "--enable-dav-fs" \
        "--enable-dav-lock" \
        "--enable-suexec" \
        "--enable-deflate" \
        "--enable-unique-id" \
        "--enable-mods-static=most" \
        "--enable-reqtimeout" \
        "--with-mpm=prefork" \
        "--with-suexec-caller=apache" \
        "--with-suexec-docroot=/" \
        "--with-suexec-gidmin=100" \
        "--with-suexec-logfile=/var/log/httpd/suexec_log" \
        "--with-suexec-uidmin=100" \
        "--with-suexec-userdir=public_html" \
        "--with-suexec-bin=/usr/sbin/suexec" \
        "--with-included-apr" \
        "--with-pcre=/usr" \
        "--includedir=/usr/include/apache" \
        "--libexecdir=/usr/lib/apache" \
        "--datadir=/var/www" \
        "--localstatedir=/var" \
        "--enable-logio" \
        "--enable-ssl" \
        "--enable-rewrite" \
        "--enable-proxy" \
        "--enable-expires" \
        "--with-ssl=/usr" \
        "--enable-headers"
```
configure hatasız bir şekilde tamamlandıktan sonra, "make" ile derleyip, "make install" ile yüklemeyi tamamlayabilirsiniz.

```bash
make
make install
```
Yükleme tamamlanınca her derlemede olduğu gibi README dosyasını okumayı unutmayınız. Yükleme tamamlanınca PREFIX/bin/apachectl start komutu ile yazılımı çalıştırabilir ya da init scripti ile başlangıçta açılacak şekilde ayarlayabilirsiniz

```bash
apachectl start
ps -aux | grep httpd #ile calisip calismadigini test edebilirsiniz.
#loglari kontrol edelim
cat /var/log/error.log
#[Tue Dec 08 01:21:37 2015] [warn] Init: Session Cache is not configured [hint: SSLSessionCache]
#[Tue Dec 08 01:21:37 2015] [notice] suEXEC mechanism enabled (wrapper: /usr/sbin/suexec)
#[Tue Dec 08 01:21:38 2015] [notice] Apache/2.2.31 (Unix) mod_ssl/2.2.31 OpenSSL/1.0.1e-fips DAV/2 configured -- \
# resuming normal operations
```

Apache yüklendiğinde, /etc/httpd içerisine conf dosyalarını atar, ana ayar dosyası /etc/httpd/conf içerisindeki httpd.conf dosyasıdır. Bu dosya gayet düz, okunabilir ve yapısaldır. Biraz sabırla okursanız tüm ayarları öğrenebilirsiniz. Sunucu direktifleri tek bir host için ayaralanıyormuş hissi uyandırırsa, tahminlerinizde yanılmazsınız. Bu servis ilk tasarlandığında bir IP adresi ya da sunucuda birden çok web sitesi tutulabileceği düşünülmemişti. Bu nedenle conf dosyasının %80'i varsayılan web sitesinin ayarlarını içermektedir. 

Daha sonraki yıllarda kullanılmaya başlayan virtual_host direktifi ile kullanıcılar için oluşturacağınız web sitelerinin ayarlarını barındırabilirsiniz.

### Virtual Host
Öncelikle kullanıcı oluşturalım:
```bash
useradd web
passwd web
chmod 755 /home/web
mkdir /home/web/{public_html,logs}
touch /home/web/logs/{error.log,access.log,php_error.log}
#ilk sayfanizi da olusturun
echo "Merhaba Dunya" > /home/web/public_html/index.html
chown -R web:web /home/web/
chown daemon:daemon /home/web/logs/php_error.log # config dosyasından kullanıcıyı değiştirebilirsiniz.
```
Sanal sunucu eklemek için "/etc/httpd/conf/extra" içerisindeki "httpd-vhosts.conf" dosyasını değiştirmeniz gerekir. Ben bu dosyayı değiştirmek yerine "/etc/httpd/conf.d/" içerisine kendi dosyalarımı atıyorum, bu şekilde ayar dosyalarınız daha taşınabilir şekilde oluyor. Yeni lokasyonun Apache tarafından taranması için "/etc/httpd/conf/httpd.conf" içerisine şu komut ile direktifi ekleyin:

```bash
echo "Include conf.d/*.conf" >> /etc/httpd/conf/httpd.conf
mkdir /etc/httpd/conf.d
#digerini de silin
mv /etc/httpd/conf/extra/httpd-vhosts.conf /etc/httpd/conf/extra/httpd-vhosts.conf.old
```
Yeni direktif dosyaınızı oluşturun, dosyanız asgari şu komutları içermelidir:
```bash
vi /etc/httpd/conf.d/z_web.conf
NameVirtualHost __IPADRESI__:80

<VirtualHost __IPADRESI__:80>
    ServerAdmin root@veriteknik.com
    DocumentRoot /home/web/public_html
    ServerName apachetest.veriteknik.com 
    ServerAlias apachetest2.veriteknik.local __IPADRESI__
    ErrorLog /home/web/logs/error.log
    CustomLog /home/web/logs/access.log common

    <Directory "/home/web/public_html">
        order deny,allow
        allow from all
        Options None
    </Directory>
</VirtualHost>
```

Sanal Sunucunuza IP adresi ile de giriş yapılmasını istiyorsanız NameVirtualHost direktifini kullanmanız gerekir

##Sunucu İzleme
Apache web sunucusunu izlemek için araçlar sunmaktadır, extra dizini altında "httpd-info.conf" dosyasını aşağıdaki şekilde edit ediniz:
```bash
<Location /sunucu-durumu>
    SetHandler server-status
    Order deny,allow
    Deny from all
    Allow from 192.168.16.
</Location>

ExtendedStatus On

<Location /sunucu-bilgisi>
    SetHandler server-info
    Order deny,allow
    Deny from all
    Allow from 192.168.16.
</Location>
```
*** Sunucu bilgisinin çıkabilmesi için mod_info'nun yüklü ya da statik olarak derlenmiş olması gerekmeketedir,  apachectl -l | grep mod_info sorusuna cevap alabiliyorsanız modül yüklüdür
```bash
apachectl -l | grep mod_info
mod_info.c
```
```Module Name kısmı altında, Module Directives kısmında "none" yazıyorsa bu modülü kullanmıyorsunuz anlamına gelir, kullanmadığınız modülleri güvenle kaldırabilirsiniz```

######* Bkz: https://tools.ietf.org/html/rfc2616
######** http://news.netcraft.com/archives/category/web-server-survey/
