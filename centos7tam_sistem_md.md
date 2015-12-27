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
##

