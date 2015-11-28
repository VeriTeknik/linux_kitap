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

diff çıktısına baktığımızda, dosyalar arasında fark olduğunu görebiliyoruz. Farkın belirtildiği satırın üstündeyse **3d2** gibi bir ifade var. Bu ifade **patch** programının yorumlaması için gerekiyor. Bir patch dosyasını oluşturmak için diff programının **-u** parametresi kullanılabilir.

