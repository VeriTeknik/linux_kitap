# Yardım Dosyaları

Geleneksel UNIX sistemlerde yardım dosyaları **man** komutu kullanılarak erişilen "manual" dosyalarıdır. Bu dosyalar sistemin tamamı için yazılmış ve bir hiyerarşi barındırmaktadır. man dosyaları **troff** yapısını kullanırlar, bu yapı sayesinde dosyaların çıktı alınca da rahat okunacak şekilde yazılması sağlanmıştır. GNU Projesi dahilinde **man**ual'ların hiyerarşisi çağdışı bulunmuş, troff yapısının hem çıktı kaygısının yersiz olduğu düşünülmüştür. Bu yüzden lternatifi olarak **info** komutu ve yapısı oluşturulmuştur. Kullandığınız programa ve sisteme göre edineceğiniz dökümantasyon farklı olacaktır ancak bugün hemen her komutun **man** ve **info** sayfası mevcuttur. Her ne kadar **man** kullanımı eski olsa da, sistem yöneticilerinin alışkanlıkları arasındadır.

![](https://imgs.xkcd.com/comics/manual_override.png)

*Kaynak: https://xkcd.com/912/*

Her ne kadar **man** sayfaları Unix'in bütün araçları için geliştirilmiş olsa da, her programcı gibi Ken Thompson ve Dennis Ritchie de dökümantasyon yazmaktan pek hoşlanmazlardı. Unix'in ilk iki yılın boyunca hiç dökümantasyonu olmamıştır. En sonunda Thompson ve Ritchie yöneticileri Doug McIlroy'un ısrarları sonucu ilk **man** dosyalarını 1971'de yazmıştır.