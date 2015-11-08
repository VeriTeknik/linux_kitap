# Yardım Dosyaları

Geleneksel UNIX sistemlerde yardım dosyaları **man** komutu kullanılarak erişilen "manual" dosyalarıdır. Bu dosyalar sistemin tamamı için yazılmış ve bir hiyerarşi barındırmaktadır. man dosyaları **troff** yapısını kullanırlar, bu yapı sayesinde dosyaların çıktı alınca da rahat okunacak şekilde yazılması sağlanmıştır. GNU Projesi dahilinde **man**ual'ların hiyerarşisi çağdışı bulunmuş, troff yapısının hem çıktı kaygısının yersiz olduğu düşünülmüştür. Bu yüzden lternatifi olarak **info** komutu ve yapısı oluşturulmuştur. Kullandığınız programa ve sisteme göre edineceğiniz dökümantasyon farklı olacaktır ancak bugün hemen her komutun **man** ve **info** sayfası mevcuttur. Her ne kadar **man** kullanımı eski olsa da, sistem yöneticilerinin alışkanlıkları arasındadır.

![](https://imgs.xkcd.com/comics/manual_override.png)

*Kaynak: https://xkcd.com/912/*

Her ne kadar **man** sayfaları Unix'in bütün araçları için geliştirilmiş olsa da, her programcı gibi Ken Thompson ve Dennis Ritchie de dökümantasyon yazmaktan pek hoşlanmazlardı. Unix'in ilk iki yılın boyunca hiç dökümantasyonu olmamıştır. En sonunda Thompson ve Ritchie yöneticileri Doug McIlroy'un ısrarları sonucu ilk **man** dosyalarını 1971'de yazmıştır.

## man Bölümleri

man sayfaları çeşitli bölümlerden oluşur. Bunun temel nedeni, aynı isimde birden fazla sayfa olabilmesidir. Örneğin **mkdir** için iki sayfa mevcuttur, birisi shell komutu olanı için, diğeri de System call olanı içindir. Bu yüzden iki farklı bölümde iki farklı mkdir man sayfası bulunur. Birisi birinci bölümde bulunur ve **mkdir(1)** şeklinde gösterilir, diğeriyse ikinci bölümde bulunur ve **mkdir(2)** şeklinde gösterilir.

man sayfaları aşağıdaki gibi bölümlendirilmiştir:

1. Programlar veya shell komutları - rm(1)
2. Unix ve C Sistem çağrıları - ioctl(2)
3. C Programlar için kütüphane çağrıları - printf(3)
4. Özel Dosyalar - null(4)
5. Dosya Biçimleri - ext4(5)
6. Oyunlar - fortune(6)
7. Diğerleri - regex(7)
8. Bakım - 1. bölümde yer alan programların bakımları ile ilgili

Herhangi bir komutun **man** sayfasına erişmek için, man komutuna kendisini parametre olarak göndermek yeterlidir.

```bash
man mkdir
```

Yukarıdaki örnekte man komutu, mkdir'in 1. bölümdeki sayfasını getirecektir, çünkü en düşük bu seviyede dosyası bulunur. Örneğin **null** için bu komut 4. bölümdeki sayfayı getirir, çünkü 1. 2. ve 3. bölümlerde sayfası yok.

Oysa **mkdir** komutunun 2. bölümdeki sayfasına erişmek isteseydik, yazmamız gereken komut şöyle olurdu:

```bash
man 2 mkdir
```