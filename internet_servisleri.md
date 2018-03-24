# İnternet Servisleri

GNU/Linux sunucular yapıları itibariyle her çeşit servisi bir çok farklı donanım üzerinden sunabilecek yapıya sahiptir, bu servisler Raspberry pi gibi sistemlerde çalışabildiği gibi, çok işlemcili mimarilerde de çalışabilmektedir. En sık kullanılan servisler Web \(http/hhtps\), mail, DNS, SNMP ve NTP'dir. IoT'nin gelişimi ile birlikte bir çok M2M \(Makinadan-Makinaya\) servisler de sıkça kullanılmaya başlanmıştır. Ancak sonuç pek değişmemektedir, kullanım kolaylığı açısından DNS yönlendirmede ve HTTPS ise güvenli iletişimi sağlamakta diğer bütün servislere kolayca ev sahipliği yapabilmektedir. Aşağıdaki tabloda sıkça kullanılan servisler ve açıklamaları yer almaktadır, kitap içerisinde de derince anlatılan bu konular kolay erişilebilirlik açısından burada kısaca değinilmiştir:

| Servis | Protokol/Port | Açıklama | Kullanım Alanları |
| :--- | :--- | :--- | :--- |
| HTTP | TCP\(80\) | Şifrelenmemiş Web servisleri  | Web içeriği, Web Servisleri |
| HTTPS | TCP\(443\) | Şifrelenmiş Web Servisleri | Web içeriği, Web Servisleri |
| DNS | UDP\(53\), TCP\(53\) | Karmaşık IP adreslerinin sabit bir alan adı ile eşleştirilmesinde kullanılır. | Alan adına karşılık gelen IPv4 ya da IPv6 adresini verir |
| NTP | UDP\(123\) | Zaman Sunucusu | Tüm sunucuların aynı zaman senkronizasyonunda kalmasını sağlar |
| SMTP | TCP\(25,587\) | E-Posta gönderim servisi | E-Posta iletim servisidir |

Web sitenizi servis etmek için sadece HTTP ve HTTPS yeterlidir, diğer servisleri harici bir servis sağlayıcıdan almanız mümkündür, yine de web siteniz form doldurulması neticesinde email gönderiyorsa basit bir MTA \(sendmail gibi\) kurmanız gerekecektir, ayrıca isterseniz DNS hizmetini de kendi sunucunuzdan verebilirsiniz.

DNS hizmetini kendi sunucunuzdan verecekseniz bu sunucunun alan adınız için bir otorite olması gerekmektedir, bu otorite ilişkisini sağlamak için alan adını aldığınız sağlayıcıdan bu alan adı için alt nameserver oluşturmanız gerekecektir. Bu alt alan adlarının tipik isimleri ise ns1, ns2,..nsx şeklindedir. Bu isimlerin karşılığına da sunucunuzun IP adresini yazabilirsiniz. Daha sonra alan adınız için nameserver ayarlarına ise bu sunuculara verdiğiniz isimleri girmelisiniz.

Sunucunuza MTA kurmanız durumunda, bu sunucunun dışarıya doğrudan e-posta atmasından ziyade, belirlenmiş olan bir smarthost'a e-postaları iletmesinde fayda vardır, bu şekilde sunucunuzdan herhangi bir şekilde SPAM çıkışının önüne geçmiş olursunuz ama en doğru çözüm hiç MTA kurmadan SMTP üzerinden doğrudan posta sunucuna posta göndermektir. Artık neredeyse bütün betik dillerinde SMTP kütüphanesi standart olarak gelmektedir.

