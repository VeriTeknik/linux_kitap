# Logrotate Servisi

Logrotate Linux kerneli, syslog ve diğer yazılımlar tarafından oluşturulan SYSLOG formatındaki logların arşivlenmesi ve zaman içerisinde belirlenen sürede yok edilmesini sağlar. Bu şekilde hem loglarınız yer kaplamayacak şekilde sıkıştırılır hem de logların genişlemesinden dolayı oluşabilecek arızaların önüne geçilmiş olur.

Logrotate çoğu sistemde önyüklü olarak gelmektedir, RedHat, Fedora ve CentOS sistemlerde şu komutla paketin var olup olmadığını görüntüleyebilirsiniz:
```bash
rpm -qa | grep -i logrotate
#logrotate-3.8.6-7.el7_2.x86_64

#yüklü degilse
yum install logrotate -y
```




FreeBSD sistemlerde ise sisteme entegre olarak çalışan newsyslog yapılandırması ile log dönüşümü sağlanır