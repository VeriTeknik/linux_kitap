# Apache Web Sunucusu

Apache Web Sunucusu, Apache Vakfı tarafından sunulan, ücretsiz, apache lisansı ile lisanslanmış bir sunucu yazılımıdır. Temel olarak RFC2616* direktifleri ve türevlerinin direktifleri doğrultusunda Web Yayım Hizmeti gerçekleştirir. HTTP OSI ISO katmanı 7'de anlamlı bilgi ve her türlü medyayı içeren dijital yayın hizmetinin temel ilkelerini belirler. Apache bu protokolün içeriğine katkıda bulunmadan, en güvenli, hızlı ve doğru biçimde sunulmasını sağlayan açık kaynak kodlu yazılımlardan biridir. Rakiplerine göre kullanım yaygınlığı, taşınabilirlik ve uyumluluk avantajları vardır. 

Adını Amerikan Yerli'lerinden alan bu yazılım, NCSA httpd'nin 1.3 sürmünü temel alan ilk sürümünü 1995 yılında yapmıştır. Genişletilebilirlik için modüler API sunması rakipleri ile arasındaki farkın açılmasına neden olmuş ve liderlik koltuğunu kapmıştır. Bu yazı yazılırken (2015) halen liderlik Apache'dedir**.

Apache'yi kaynak kodundan kurabileceğiniz gibi, sisteminize uygun binary arşivini de bulmanız mümkündür, kaynak koddan derlemenin günümüzde pek bir faydası olmayacağı aşikar olmakla birlikte, gerçekten kodun içerisine girip kendi yamalarınızı yapmayı düşünüyorsanız, tavsiye ederiz. Biz yine de kaynak kod derlemesinin nasıl yapıldığını göstereceğiz: 

Öncelikle gerekli olabilecek paketleri yükleyin, daha sonra bu paketleri kaldırabilirsiniz:
Yüklemek için
```bash
yum groupinstall "Development Tools"
yum install cmake wget ncurses-devel openssl-devel pcre-devel libxml2-devel curl-devel gd-devel libxslt-devel
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
#[Tue Dec 08 01:21:38 2015] [notice] Apache/2.2.31 (Unix) mod_ssl/2.2.31 OpenSSL/1.0.1e-fips DAV/2 configured -- resuming normal operations
```

Apache yüklendiğinde, /etc/httpd içerisine conf dosyalarını atar, ana ayar dosyası /etc/httpd/conf içerisindeki httpd.conf dosyasıdır. Bu dosya gayet düz, okunabilir ve yapısaldır. Biraz sabırla okursanız tüm ayarları öğrenebilirsiniz. Sunucu direktifleri tek bir host için ayaralanıyormuş hissi uyandırırsa, tahminlerinizde yanılmazsınız. Bu servis ilk tasarlandığında bir IP adresi ya da sunucuda birden çok web sitesi tutulabileceği düşünülmemişti. Bu nedenle conf dosyasının %80'i varsayılan web sitesinin ayarlarını içermektedir. 

######* Bkz: https://tools.ietf.org/html/rfc2616
######** http://news.netcraft.com/archives/category/web-server-survey/
