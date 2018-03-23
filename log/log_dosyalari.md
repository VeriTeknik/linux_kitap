# Log Dosyaları

Log dosyaları LINUX ve UNIX sistemlerin en önemli ve ayrılmaz parçalarından biridir. Diğer işletim sistemlerinde şahit olduğumuz "hatır-şinas" loglama bu sistemlerde "ciddi" loglama olarak karşımıza çıkıyor. Genelde /var/log dizini altında yer alan log dosyalarının her birinin bir ya da birden çok servisi logladığını görebilirsiniz.

Diğer işletim sistemlerinin aksine, LINUX tek satırlık log girdileri tutar, okumayı kolaylaştıran bu sistem sayesinde loglar içerisinde kaybolmazsınız. Ayrıca standart girdi/çıktı araçlarıyla hataları ayıklamanız kolaylaşır, öyle ki aşağıdaki şu basit sorgu bile bir kaç saniyede hataları ayıklamanıza yardımcı olacaktır:

```
[root@ckaraca~]# tailf /var/log/httpd/access.log | grep -i 404
```

tailf komutu ile log dosyasının sonunu okumakla birlikte, yeni gelen her satır da ekranınıza düşmektedir, son eklenen satırları okumak için yeniden başlatmanıza gerek yoktur. Bazı sistemlerde tailf aliası olmaması nedeniyle tail -f kullanılması gerekebilir. Bu akan log ekranını pipe ile grep'e yönlendirdiğimizde ise \(-i\) direktifi sayesinde büyük/küçük harf duyarsızlaştırdığımız sonuçlar arasından 404 hatasını aramış oluyoruz, örnek sonuç aşağıdadır:

```
[root@ckaraca~]# tailf /var/log/httpd/access_log | grep -i 404
66.249.64.94 - - [23/Mar/2018:22:44:46 +0300] "GET /forum/member.php?u=14 HTTP/1.1" 404 3645 "-" "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
```

Web Hosting Sistem Uzmanının sürekli takip etmesi gereken log dosyaları şunlardır:

| LOG | Yolu | Amacı |
| --- | --- | --- |
| Messages | /var/log/messages | Genel amaçlı log dosyasıdır, bir çok sistem servisi bu dosyaya loglarını yazar, sistem açılış mesajları, disk arızaları da bu dosyaya yazılır |
| dmesg | /var/log/dmesg | kernel tüm mesajlarını bu dosya içerisine atar, mrneğin PCI portlarıi diskler ve diğer aygıtlar hakkında bu dosyadan bilgi alabilirsiniz. |
| HTTP | /var/log/http/access.log | Apache'nin genel erişim kayıt dosyasıdır. Tüm erişim bilgileri bu dosyada saklanır. |
| HTTP | /var/log/http/error\_log | Apache'nin genel hata kütüğüdür, tüm hataları buradan takip edebilirsiniz |

Bu dosyaların yanı sıra, kernel'in döngülü hafızasında bulunan mesajları da dmesg komutu ile görüntüleyebilirsiniz.Aynı şekilde, hangi kullanıcının en son ne zaman giriş yaptığını görüntülemek için 'lastlog' komutunu kullanabilirsiniz.

### SYSLOG

Syslog programlama yaparken, işletim sistemi hatalarını ve uyarılarını takip ederken kullanılan standart bir loglama mekanizmasıdır, siz kullanmasanız bile o hep ordadır, logluyordur ve hiç durmaz.

ilk syslog çalışmaları 2001 yılında başlamıştır, RFC 3164[1](http://www.rfc-editor.org/info/rfc3164) syslogun temelini oluşturur, syslog logların nasıl tutulacağı, ağırlığı ve önemine kadar bir çok noktayı tanımlar, syslog sadece kendi domaini ya da sunucusu ile sınırlı değildir, diğer bütün cihazlardan ya da sunuculardan da log dosyalarını toplayabilir.

Syslog'a gelen hata ve uyarı mesajları gönderen servise göre sınıflandırılabilir, buna orjinal dilinde "Facility" denilmektedir. Ayrıca kullanıcının kendi özel servisleri ya da uzak sunucuları için kullanabileceği 8 ayrı facility bulunmaktadır.

| Servis Kodu | Anahtar Kelime | Açıklama |
| --- | --- | --- |
| 0 | kern | Kernel Mesajları |
| 1 | user | kullanıcı seviyesi mesajlar |
| . | .. | .. |
| 5 | syslog | syslog tarafından üretilen mesajlar |
| . | .. | .. |
| 16 | local0 | kişisel kullanım için local0 |

Syslog log önem derecesi şu şekilde sınıflandırılabilir:

| Değer | Önem | Anahtar Kelime | Açıklama | örnek |
| :--- | :--- | :--- | :--- | :--- |
| 0 | Kriz | emerg | Sistem kullanılamaz | kernel panik |
| 1 | Alarm | alert | Acilen düzeltilmesi gereken durum | IO ya da RAID hataları |
| 2 | Kritik | crit | Kritik durumlar | Disk Dolması gibi |
| 3 | Hata | err | Uygulama Hataları | Apache config hatası |
| 4 | Uyarı | warning | Uyarı mesajları | PHP.ini dosyasında date alanının tanımsız olması |
| 5 | Bildirim | notice | Hata mahiyetinde olmayan ancak bildirim gerektiren durumlar | depreciated komutlar |
| 6 | Bilgi Amaçlı | info | Operasyonel mesajlar | Örneğin bir işlem tamamlandığında |
| 7 | Debug | debug | tasarımcılar için özel debug mesajları | Fonksiyon giriş ve çıkış noktaları |

### Kişisel Log Dosyaları ve Rotasyon

Kişisel uygulamalarınız ya da Virtual Host yapılandırmalarınız için de mutlaka log dosyası ayarlamalısınız. Kendini seven Sistem Yöneticisi kurduğu her servis için log dosyası ayarlamalı ve bu log dosyalarının rotasyonunu sağlamalıdır, rotasyon yapmazsanız o bir gün gelecek ve sunucunuzdaki tüm disk alanınız dolacaktır. Tabi siz bunu MySQL çalışmıyor şikayeti alarak öğreneceksiniz, bu durumda yapmanız gereken ilk önce df -h ve df -i ile disk dolu mu ona bakmak olacaktır. Rotasyon aynı zamanda log tutma konusundaki yasal yükümlülüklerinizi yerine getirmenizde de yardımcı olacaktır, bu şekilde eskimiş log dosyalarınızı sıkıştırabilir ve arşivleyebilirsiniz.

Apache Virtual Host kullanan bir web sitesi için php-fpm yüklemesi ile birlikte kullanıcı bazlı log yapılandırması ve logrotate şu şekilde ayarlanabilir, diyelim ki kullanıcı adı veriteknik, web sitesi ismi de sanallastirma.com olsun, dizin hiyerarşisi şu şekilde olacaktır:

```
/                            
└─home
  ├─veriteknik
    ├─logs
    ├─sanallastirma.com
    ├─session
    ├─tmp
```

Yukarıdaki dizin yapısına göre log dosyalarını rotate\(evirecek\) edecek yapılandırma şu şekilde oluşturulmalıdır:

```
[root@ckaraca~]# echo "/home/*/logs/*log { 
        daily 
        rotate 720 
        missingok 
        compress 
        delaycompress 
        postrotate 
        /usr/sbin/apachectl graceful 
        endscript 
}" > /etc/logrotate.d/veriteknik
```

Komutunu çalıştırdığımızda /etc/logrotate.d/veriteknik betiği içerisinde tüm /home/ dizini altındaki tüm kullanıcıların logs dizini içerisindeki log uzantılı dosyaları rotate edecek komutu yazmış oluruz. Bu betik toplam 720 günlük log tutacağı gibi, bu logları sıkıştırıp apache'yi de konu hakkında bilgilendiriyor. Bu şekilde sonradan açacağınız her kullanıcı için yeniden ayar yapmanıza gerek kalmaz. Yaptığınız ayarların doğru çalışıp çalışmadığını da aşağıdaki komut ile test edebilirsiniz:

```
logrotate -df /etc/logrotate.d/veriteknik
```

İşlem sonucunda log dosyalarınızın arşivlendiğini görebilirsiniz.

