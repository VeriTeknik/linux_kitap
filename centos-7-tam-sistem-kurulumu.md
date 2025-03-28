# CentOS 7 Tam Sistem Kurulumu (Arşiv Amaçlı)

**UYARI:** CentOS Linux 7, 30 Haziran 2024 tarihinde **ömrünü tamamlamıştır (End-of-Life - EOL)**. Bu tarihten sonra güvenlik güncellemeleri almamaktadır ve **üretim ortamlarında kullanılması kesinlikle önerilmez**. Bu bölümdeki bilgiler sadece **tarihsel referans ve arşiv amacıyla** korunmaktadır. Modern sistemler için güncel RHEL tabanlı dağıtımlar (örn. Rocky Linux, AlmaLinux) veya diğer güncel Linux dağıtımları kullanılmalıdır.

Bu bölümde, CentOS 7 üzerine temel bir web hosting ortamı kurmak için izlenen adımlar özetlenmiştir.

Sistem CD/ISO'dan kurulduktan sonra uygulanacak temel adımlar şunlardır:

## Sistem Güncelleme ve Temel Ayarlar

```bash
# SELinux'u permissive moda al (veya enforcing bırakıp kuralları ayarlayın - disabled önerilmez)
# sudo sed -i 's/SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config 
# Geçici olarak: sudo setenforce 0

# Sistemi güncelle (yum CentOS 7'de hala kullanılır, ancak dnf daha moderndir)
sudo yum update -y 

# Gereksiz bazı servisleri kaldır (isteğe bağlı)
# sudo yum remove avahi-autoipd avahi-libs avahi NetworkManager-libnm -y
```
**Not:** SELinux'u tamamen devre dışı bırakmak yerine `permissive` modda çalıştırmak veya kuralları doğru şekilde ayarlamak (`enforcing` mod) güvenlik açısından daha iyidir.

## Güvenlik Duvarı Ayarları (`firewalld`)

```bash
systemctl enable firewalld.service
firewall-cmd --zone=public --permanent --add-service=http
firewall-cmd --zone=public --permanent --add-service=https
systemctl start firewalld.service
```

## Kullanıcı ve SSH Güvenlik Ayarları

Web sitesi dosyalarını barındırmak için ayrı bir kullanıcı oluşturmak iyi bir pratiktir. FTP yerine daha güvenli olan SFTP (SSH üzerinden) genellikle yeterlidir.

```bash
useradd web
passwd web
#ssh login denemelerini 3 ile sinirlandirin
sed -i 's/#MaxAuthTries.*/MaxAuthTries 3/' /etc/ssh/sshd_config
```

## Web Sunucusu ve PHP Kurulumu

**Not:** Aşağıdaki PHP 7.2 kurulumu **çok eskidir** ve güvenlik açıkları içerir. Modern uygulamalar için PHP 8.x sürümleri kullanılmalıdır. Bu adımlar sadece tarihsel referans içindir.

REMI deposu gibi üçüncü parti depolar, CentOS 7 için daha güncel PHP sürümleri sağlıyordu.
```bash
# Gerekli depoları ekle (EPEL ve REMI)
sudo yum install epel-release yum-utils -y
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm -y

# Gerekliyse RHEL Optional deposunu etkinleştir (bazı bağımlılıklar için)
# sudo subscription-manager repos --enable=rhel-7-server-optional-rpms 

# REMI deposundan PHP 7.2'yi etkinleştir
sudo yum-config-manager --enable remi-php72

# Gerekli paketleri kur (Apache httpd, MariaDB, PHP 7.2 ve modülleri, yardımcı araçlar)
sudo yum install httpd mariadb-server php php-fpm php-mysqlnd php-gd php-xml php-mbstring wget bind-utils net-tools lsof iptraf atop -y

# (İsteğe bağlı) Avahi servisini kapat
# sudo systemctl stop avahi-daemon.socket avahi-daemon.service
# sudo systemctl disable avahi-daemon.socket avahi-daemon.service
```

Kurulumla birlikte gelen Apache (`httpd`) ve MariaDB (`mariadb`) servislerinin yanı sıra PHP-FPM (`php-fpm`) servisi de kurulmuş olur. Bu servislerin yapılandırılması ve başlatılması ilgili bölümlerde anlatılmıştır.

Ayrıca `atop`, `lsof`, `iptraf`, `bind-utils` (`dig` içerir) ve `net-tools` (`netstat` içerir, `ss` daha modern alternatifidir) gibi sistem izleme ve ağ tanılama araçlarının kurulması da faydalıdır:

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

**netstat:** Ağ bağlantılarını, yönlendirme tablolarını, arayüz istatistiklerini vb. gösterir (Modern alternatif: `ss`). Örneğin:
```bash
netstat -tulnp 
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
