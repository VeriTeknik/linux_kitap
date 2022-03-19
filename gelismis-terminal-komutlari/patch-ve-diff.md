# patch ve diff

İki dosya arasındaki farkı görmenin en kolay yolu, **diff** komutunu kullanmaktır. Örneğin

```bash
eaydin@dixon ~/calisma $ cat test.txt 
birinci satir
ikinci satir
ucuncu satir
eaydin@dixon ~/calisma $ cat test2.txt 
birinci satir
ikinci baska satir
eaydin@dixon ~/calisma $ diff test.txt test2.txt 
2,3c2
< ikinci satir
< ucuncu satir
---
> ikinci baska satir
```

diff çıktısına baktığımızda, dosyalar arasında fark olduğunu görebiliyoruz. Farkın belirtildiği satırların üstündeyse **2,3c2** gibi bir ifade var. Bu ifade **patch** programının yorumlaması için gerekiyor. Bir patch dosyasını oluşturmak için diff programının **-u** parametresi kullanılabilir.

```bash
eaydin@dixon ~/calisma $ diff -u test.txt test2.txt 
--- test.txt    2015-11-28 14:56:16.268143061 +0200
+++ test2.txt    2015-11-28 14:56:27.888143494 +0200
@@ -1,3 +1,2 @@
 birinci satir
-ikinci satir
-ucuncu satir
+ikinci baska satir
```

Yukarıda, `test.txt` dosyasından `test2.txt` dosyası türetmek istiyorsak uygulanacak **patch** çıktısı görülüyor. Öyleyse bunu bir patch dosyası olarak kaydedebiliriz.

```bash
eaydin@dixon ~/calisma $ diff -u test.txt test2.txt > test1-2.patch
```

Şimdi patch dosyasını kullanabiliriz. Dosyanın içeriğinde patch'ın uygulanacağı dosya isimleri de belirtildiği için, doğrudan patch dosyasını programa standart girdi olarak vermemiz yeterli olacaktır.

```bash
eaydin@dixon ~/calisma $ patch < test1-2.patch 
patching file test.txt
eaydin@dixon ~/calisma $ cat test.txt 
birinci satir
ikinci baska satir
eaydin@dixon ~/calisma $ cat test2.txt 
birinci satir
ikinci baska satir
```

Gördüğünüz gibi artık test.txt dosyasının içeriği, test2.txt dosyasının içeriği ile aynı oldu.
