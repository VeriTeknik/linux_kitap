# CentOS 7 tam sistem kurulumu

Web Hosting için eksiksiz sistem kurulumu bu kitabın bir sonucu olarak burada anlatılmıştır, mümkün olduğunca ek paket kullanmadan dahili reponun izin verdiği sürümler tercih edilmiştir.

Gerekli optimizasyonların da yapıldığı sistem "üretim ortamı" için uygundur.

Sistem CD'den yüklendikten sonra uygulanacak adımlar şunlardır

## Sistem optimizasyonu

```
sed -i 's/SELINUX=.*/SELINUX=disabled/' /etc/sysconfig/selinux
yum upgrade
yum remove avahi-autoipd avahi-libs avahi NetworkManager-libnm
```

## firewall ayarları

```bash
systemctl enable firewalld.service
firewall-cmd --zone=public --permanent --add-service=http
firewall-cmd --zone=public --permanent --add-service=https
systemctl start firewalld.service
```

## Kullanıcı ve Güvenlik ayarları

Kullanıcıyı açın, gerekirse şifre verin ve FTP sunucusu yükleyin, tavsiyemiz FTP sunucusunu ihtiyacınız olduğu zaman kullanmanızdır.

```bash
useradd web
passwd web
#ssh login denemelerini 3 ile sinirlandirin
sed -i 's/#MaxAuthTries.*/MaxAuthTries 3/' /etc/ssh/sshd_config
```

## PHP Yüklemesi

PHP'yi remi repo üzerinden yüklemenizi tavsiye ederiz, hem yüklemesi kolaydır hem de "yum-config-manager --enable remi-php7X" şeklindeki komut ile PHP versiyonları üzerinde kolayca geçiş sağlayabiliriz, varsayılan olarak PHP72 seçmiş olalım, bu komut sayesinde versiyon kodu ile yüklemek zorunda kalmazsınız, örneğin PHP-7.2 yüklemek için "yum install php70-php-fpm" yerine her seferinde "yum install php-fpm" demeniz yeterli olacaktır. Böylelikle kolayca versiyonlar arasında geçiş sağlayabilirsiniz, ayrıca versiyona bağlı dizinlerden de kurtulmuş olursunuz. Tüm PHP dosyaları varsayılan yerinde duracaktır, /opt/remi/php72/etc/php.ini yerine /etc/php.ini gibi. Versiyon değişikliği yaparken eski versiyonu da kaldırmayı unutmamalısınız: --enable remi-php71 gibi

```
yum install epel-release yum-utils
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
subscription-manager repos --enable=rhel-7-server-optional-rpms
yum-config-manager --enable remi-php72
yum install php-fpm php-mysql php-gd php-xml httpd \
mariadb-server wget bind-utils net-tools lsof iptraf atop
systemctl stop avahi-daemon.socket avahi-daemon.service
systemctl disable avahi-daemon.socket avahi-daemon.service
```

Eğer dikkatinizi çektiyse, bir web sunucusu kurmanızı gerektirecek yüklemelerin yanı sıra atop, lsof, iptraf, bind-utils ve net-tools gibi her zaman işinize yarayacak paketleri de pratikte sunucuya kurmayı seviyoruz. Bu sayede sistem takibi kolaylaşmakla birlikte, soruna müdahale etmenizi gerektirecek durumlarda bu paketlerin yüklenmesi için gereken süreden de tasarruf sağlamış olursunuz. Bu kütüphaneler içerisindeki komutları özet geçecek olursak:

**lsof:** bir prosesin id'si ile ya da port ile açılmış dosyaları bulmak\
için kullanılır, örneğin:

```
lsof -p 12345 ya da lsof -i tcp:443 gibi
```

**dig:** alan adlarının DNS bilgisini verir, Windowstaki nslookup'ın benzeridir,\
örneğin:

```
dig veriteknik.com ya da dig NS veriteknik.com,
detaylı bilgi: dig +trace veriteknik.com, belli bir
DNS sunucusundan: dig veriteknik.com @127.0.0.1 gibi
```

**netstat:** Tüm network trafiğinin özet bilgilerini görüntüler: örneğin

```
netstat -apn ya da netstat -tulpn gibi
```

**iptraf:** Gerçek zamanlı IP trafik izleme aracı, Wireshark'ın dos versiyonu gibi düşünebilirsiniz.

**atop:** sistem ile yüklü gelen top alternatifidir, disk, network, CPU ve RAM utulizasyonu aynı anda görüntülenir.

## logrotate ayarları

```bash
echo "/home/*/logs/*log { 
        daily 
        rotate 720 
        missingok 
        compress 
        delaycompress 
        postrotate 
        /usr/sbin/apachectl graceful 
        endscript 
}" > /etc/logrotate.d/home
```

## MySQL (MariaDB) Kurulumu

```bash
systemctl enable mariadb
systemctl start mariadb
mysql_secure_installation
# kisa versiyon
# Asagida entera basiniz
Enter current password for root (enter for none):
# Yeni sifreyi girebilmek icin Y harfine basiniz
Set root password? [Y/n] Y
#New password: 
#Re-enter new password: 
#Password updated successfully!
#Reloading privilege tables..
# ... Success!
# Tum islemlere Y harfi ile devam ediniz
Remove anonymous users? [Y/n] Y
# ... Success!
Disallow root login remotely? [Y/n] Y
#  ... Success!
Remove test database and access to it? [Y/n] Y
# - Dropping test database...
# ... Success!
Reload privilege tables now? [Y/n] Y
# ... Success!

Thanks for using MariaDB!
```
