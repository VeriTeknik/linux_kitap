# Log Dosyaları

Log dosyaları LINUX ve UNIX sistemlerin en önemli ve ayrılmaz parçalarından biridir. Diğer işletim sistemlerinde şahit olduğumuz "hatır-şinas" loglama bu sistemlerde "ciddi" loglama olarak karşımıza çıkıyor. Genelde /var/log dizini altında yer alan log dosyalarının her birinin bir ya da birden çok servisi logladığını görebilirsiniz. 

Diğer işletim sistemlerinin aksine, LINUX tek satırlık log girdileri tutar, okumayı kolaylaştıran bu sistem sayesinde loglar içerisinde kaybolmazsınız.

Web Hosting yapan bir Sistem Uzmanının sürekli takip etmesi gereken log dosyaları şunlardır:

| LOG | Yolu | Amacı |
| -- | -- | -- |
| Messages | /var/log/messages | Genel amaçlı log dosyasıdır, bir çok sistem servisi bu dosyaya loglarını yazar, sistem açılış mesajları, disk arızaları da bu dosyaya yazılır |
| dmesg | /var/log/dmesg | kernel tüm mesajlarını bu dosya içerisine atar, mrneğin PCI portlarıi diskler ve diğer aygıtlar hakkında bu dosyadan bilgi alabilirsiniz. |
| HTTP | /var/log/http/access.log | Apache'nin genel erişim kayıt dosyasıdır. Tüm erişim bilgileri bu dosyada saklanır.|
| HTTP | /var/log/http/error_log | Apache'nin genel hata kütüğüdür, tüm hataları buradan takip edebilirsiniz |



Bu dosyaların yanı sıra, kernel'in döngülü hafızasında bulunan mesajları da dmesg komutu ile görüntüleyebilirsiniz.Aynı şekilde, hangi kullanıcının en son ne zaman giriş yaptığını görüntülemek için 'lastlog' komutunu kullanabilirsiniz.


### SYSLOG
Syslog programlama yaparken, işletim sistemi hatalarını ve uyarılarını takip ederken kullanılan standart bir loglama mekanizmasıdır, siz kullanmasanız bile o hep ordadır, logluyordur ve hiç durmaz.

ilk syslog çalışmaları 2001 yılında başlamıştır, RFC 3164[1] syslogun temelini oluşturur, syslog logların nasıl tutulacağı, ağırlığı ve önemine kadar bir çok noktayı tanımlar, syslog sadece kendi domaini ya da sunucusu ile sınırlı değildir, diğer bütün cihazlardan ya da sunuculardan da log dosyalarını toplayabilir. 

Syslog'a gelen hata ve uyarı mesajları gönderen servise göre sınıflandırılabilir, buna orjinal dilinde "Facility" denilmektedir. Ayrıca kullanıcının kendi özel servisleri ya da uzak sunucuları için kullanabileceği 8 ayrı facility bulunmaktadır.

|Servis Kodu|Anahtar Kelime|Açıklama|
| -- | -- | -- |
|0|kern| Kernel Mesajları|
|1|user|kullanıcı seviyesi mesajlar|
|.|..|..|
|5|syslog|syslog tarafından üretilen mesajlar|
|.|..|..|
|16|local0|kişisel kullanım için local0|



Syslog log önem derecesi şu şekilde sınıflandırılabilir:

|Değer|	Önem|	Anahtar Kelime|	Açıklama|örnek|
| -- | -- | -- | -- | -- |
|0|	Kriz	| emerg	| Sistem kullanılamaz| kernel panik |
|1|	Alarm	| alert	| Acilen düzeltilmesi gereken durum | IO ya da RAID hataları|
|2|	Kritik	| crit	| Kritik durumlar | Disk Dolması gibi |
|3|	Hata	| err	| Hata durumları | Uygulama Hataları | Apache config hatası|
|4|	Uyarı | warning | Uyarı mesajları | PHP.ini dosyasında date alanının tanımsız olması|
|5|	Bildirim| notice |	Hata mahiyetinde olmayan ancak bildirim gerektiren durumlar| depreciated komutlar|
|6| Bilgi Amaçlı |info| Operasyonel mesajlar | Örneğin bir işlem tamamlandığında |
|7|	Debug|	debug| tasarımcılar için özel debug mesajları| Fonksiyon giriş ve çıkış noktaları|

[1]: http://www.rfc-editor.org/info/rfc3164

