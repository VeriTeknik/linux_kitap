# CentOS, Debian ve FreeBSD Gibi Ana Sürümlerin Farkları

Öncelikle, sürümlere Servis Sağlayıcı penceresinden bakıldığını söylememiz gerekir, her ne kadar FreeBSD bir BSD sürümü olsa da temel yakınlıklar nedeniyle LINUX sürümleri ile karşılaştırılmıştır. Ayrıca RedHat ve Solaris gibi ticari değeri olan Unix ve türevleri karşılaştırmaya dahil edilmemiştir.

Geliştiricilerin, sunucu sistemi kullanıcılarının ve son kullanıcıların tercihleri birbirinden farklı olmaktadır, UNIX ve türevlerinde sistemin kullanılacağı mimari, uygulama ve süre tercih nedenini değiştirebilmektedir. Aşağıdaki tabloda "Sunucu Sistemi" topolojisi için tercih nedenleri puanlanmıştır.

## Sistem Yöneticileri Hangi Sürümü Tercih Etmektedir

|Tercih| CentOS | Debian | FreeBSD |
| -- | -- | -- | -- |
|Hızlı Kurulum | 10 | 8 | 5 |
| Desktop Kullanımı | 7 | 8 | 3 | 
| Kolay Yönetim | 10 | 10 | 3 |
| Stabilite | 7 | 7 | 10 |
| Sürüm Geçişi ve Güncelleme | 1 | 8 | 10 |
| Cevap Bulma | 10 | 8 | 2 |
| Donanım Desteği | 10 | 10 | 5 |


Kişisel deneyimlerimize göre şekillendirdiğimiz bu tablo Linux kullanıcıları arasında sürekli tartışılsa da, genel kanı olarak stabilite ve sürdürülebilirlik göz önünde bulundurulduğunda FreeBSD tartışmasız lider konumundadır, ancak deneyimsiz ellerde tamamen felakete dönüşebilecek bu sistemi kullanmak yüksek bilgi ve deneyim gerektirir. 

Debian ve CentOS arasında tercih yapmak ise, tutulan futbol takımı kadar rastlantısal olabilir, kullanıcıların ilk başarılı deneyimi bu iki sürüm arasında genelde tercih nedeni olmaktadır. Google trends üzerinden yaptığımız incelemede iki sürümün de aynı oranda popüler olduğunu göstermektedir:
![CentOS ve Debian](capture_001_09112015_040419.jpg)

İşletim sistemlerinin bir felsefesinin olduğu, bu sistemleri eleştirirken felsefelerin gözönünde bulundurulması gerektiğini hatırlatmak isteriz. Unix her zaman "kullanıcının ne yaptığını bildiği" varsayımına dayanan bir işletim sistemi olmuştur ve bugünkü Linux türevleri bu anlayışı sürdürür. Doug Gwyn'in bu konudaki sözü, bu felsefenin gerekçesini net bir biçimde ortaya koymaktadır:

*"Unix was not designed to stop its users from doing stupid things, as that would also stop them from doing clever things."
([Kaynak](http://opensource.com/business/14/12/linux-philosophy)*)



