# CentOS 7 Tam Sistem Kurulumu

Web Hosting için eksiksiz sistem kurulumu bu kitabın bir sonucu olarak burada anlatılmıştır, mümkün olduğunca ek paket kullanmadan dahili reponun izin verdiği sürümler tercih edilmiştir.

Gerekli optimizasyonların da yapıldığı sistem "üretim ortamı" için uygundur.

Sistem CD'den yüklendikten sonra uygulanacak adımlar şunlardır

### Sistem optimizasyonu
```
sed -i 's/SELINUX=.*/SELINUX=disabled/' /etc/sysconfig/selinux
yum upgrade
yum install php-fpm php-mysql php-gd php-xml httpd \ 
mariadb-server wget bind-utils net-tools lsof iptraf tcpdump
systemctl stop avahi-daemon.socket avahi-daemon.service
systemctl disable avahi-daemon.socket avahi-daemon.service
yum remove avahi-autoipd avahi-libs avahi NetworkManager-libnm

```

### firewall ayarları
```bash
systemctl enable firewalld.service
firewall-cmd --zone=public --permanent --add-service=http
firewall-cmd --zone=public --permanent --add-service=https
systemctl start firewalld.service
```

###Kullanıcı ve Güvenlik ayarları
Kullanıcıyı açın, gerekirse şifre verin ve FTP sunucusu yükleyin, tavsiyemiz FTP sunucusunu ihtiyacınız olduğu zaman kullanmanızdır.


```bash
useradd web
passwd web
#ssh login denemelerini 3 ile sinirlandirin
sed -i 's/#MaxAuthTries.*/MaxAuthTries 3/' /etc/ssh/sshd_config 
```


### logrotate ayarları
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

### MySQL (MariaDB) Kurulumu
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


