# Logrotate Servisi

Logrotate Linux kerneli, syslog ve diğer yazılımlar tarafından oluşturulan SYSLOG formatındaki logların arşivlenmesi ve zaman içerisinde belirlenen sürede yok edilmesini sağlar. Bu şekilde hem loglarınız yer kaplamayacak şekilde sıkıştırılır hem de logların genişlemesinden dolayı oluşabilecek arızaların önüne geçilmiş olur.

Logrotate çoğu sistemde önyüklü olarak gelmektedir, RedHat, Fedora ve CentOS sistemlerde şu komutla paketin var olup olmadığını görüntüleyebilirsiniz:
```bash
rpm -qa | grep -i logrotate
#logrotate-3.8.6-7.el7_2.x86_64

#yüklü degilse
yum install logrotate -y
```




FreeBSD sistemlerde ise sisteme entegre olarak çalışan newsyslog yapılandırması ile log dönüşümü sağlanır, /etc/newsyslog.conf içerisinde log dönüşümü sağlanan dosya isimlerini görebilirsiniz:
```bash
# logfilename          [owner:group]    mode count size when  flags [/pid_file] [sig_num]
/var/log/all.log                        600  7     *    @T00  J
/var/log/amd.log                        644  7     100  *     J
/var/log/auth.log                       600  7     100  @0101T JC
```

* [owner:group]: istenirse bu kısıma kullanıcı ve grup bilgisi yazılabilir
* mode: oluşturulacak dosyanın modunu temsil eder, güvenlik nedeniyle log dosyalarının sahibini [root:root] yapıp modu da 600 yaparsanız, bu dosyaları sadece root okuyabilir.
* count: Geri dönük kaç adet log dosyası tutulacağını ifade eder, Hosting ortamında maillog ve httpd loglarının iki sene tutulmasında fayda vardır, bu nedenle bu değeri 730 olarak ayarlayabilirsiniz. Diğer dosyalarda bir hafta (7) yeterli olacaktır.
* size: Eğer dosyanın boyutu burada belirtilen değerin üzerindeyse (kB) rotasyon yapılır, * bu değeri göz ardı et demektir.
* when: bir önceki niceliğe skalar bir değer verdiyseniz, örneğin 100 kB, bu değer için (*) kullanabilirsiniz, bir önceki değer (*) ise bu değer ISO 8601 standardında aşağıdakilerden biri olabilir:
```bash
    $D0     Her gece yarısı rotasyon yap (@T00 ile aynı)
    $D23    her gece 23:00'da rotasyon yap (@T23 ile aynı)
	$W0D23  Her haftanın ilk günü (pazar) saat 23:00'da
    $W5D16  Her haftanın 5. günü saat 16:00'da
    $M1D0   Her ayın ilk günü gece yarısında
    $M5D6   Her ayın 5. günü saat 6:00'da
```
* flags:
    - C: dosya olmayabilir, yoksa oluştur
    - J: Dosyayı sıkıştır (bzip ile)
    - X: Dosyayı sıkıştır (xz ile)
    - Z: Dosyayı sıkıştır (gzip kullanılır | tavsiye edilir)
* [/pid_file]: log rotasyonu tamamlandığında logu üreten prosesin sinyallenmesi gerekiyorsa, prosesin pid bilgisi buraya yazılır, örneğin: /var/run/pflogd.pid


