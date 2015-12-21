# CentOS 7 Firewall

CentOS 7'de yapılan değişikliklerden firewall servisi de nasibini almıştır, eski sürümlerdeki iptables'a nazaran kullanımı kolay ve anlaşılabilir olan firewalld servisi gelmiştir.

Servisi sunucu açılışında aktif yapmak için 
```bash
systemctl enable firewalld.service
```
Çalıştırmak için:
```bash
systemctl start firewalld.service
```
CentOS 7 üzerindeki firewall servisi firewall-cmd komutu ile yönetilmektedir.

İhtiyacınız olabilecek temel Komutlar şunlardır:
```bash
#Firewall durumunu goruntuleme
firewall-cmd --state
#running

#varsayilan aktif bolge
firewall-cmd --get-default-zone
#public

#tum kurallari listeleme
firewall-cmd --list-all
#public (default)
#  interfaces: 
#  sources: 
#  services: dhcpv6-client ssh
#  ports: 
#  masquerade: no
#  forward-ports: 
#  icmp-blocks: 
#  rich rules: 
```