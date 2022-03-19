# Olmazsa Olmaz: stdio.h

Herhangi bir C programı yazdıysanız, hemen hemen her zaman `stdio.h` başlık dosyasını programın başında çağırdığınızı fark etmişsinizdir. Aslında bu dosya, programın standart girdi ve standart çıktı ile etkileşimini sağlayan bileşenleri barındırır. İsmi bu yüzden _Standard Input/Output_'un kısaltmasıdır.

Brian Kernighan ve Dennis Ritchie'nin meşhur **The C Programming Language** kitabındaki basit bir örneği uygulayacak olursak, tanıdık sonuçlar elde ettiğimizi görebilirsiniz.

```
#include <stdio.h>
#include <ctype.h>

main()
{
    int c;
    while ((c = getchar()) != EOF)
        putchar(tolower(c));
    return 0;
}
```

Yukarıdaki kodu derlediğimizde, standart girdiden okuduğu kelimelerin, küçük harfe çevrilerek standart çıktıya yazdığı görülebilir. Derlediğimiz programa `lower` ismini verecek olursak, örneğin aşağıdaki şekilde kullanabiliriz.

```
eaydin@eaydin-vt ~/devel/lower $ echo AbCdE | ./lower
abcde
```

Gördüğünüz gibi, pipe işareti ile doğrudan standart girdiden gelen veriyi okuyabildik. Eğer bu verileri bir metin dosyasına yazsaydık, standart girdi olarak metin dosyasından da yönlendirebilirdik.

Önce metin dosyamızı oluşturalım.

```
eaydin@eaydin-vt ~/devel/lower $ echo AbCdE > karakterler
eaydin@eaydin-vt ~/devel/lower $ cat karakterler 
AbCdE
```

Şimdi programımıza standart girdi olarak sunalım.

```
eaydin@eaydin-vt ~/devel/lower $ ./lower < karakterler
abcde
```

Kısacası programımız için bilginin pipe ile veya **<** işareti ile gelmesinin bir önemi yok. İkisi de standart girdi çünkü. Benzer şekilde `ls` çıktısını da programımıza yönlendirebilirdik.

```
eaydin@eaydin-vt ~/devel/lower $ ls
Buyuk_HARFLI_diZin  karakterler  lower  lower.c
eaydin@eaydin-vt ~/devel/lower $ ls | ./lower
buyuk_harfli_dizin
karakterler
lower
lower.c
```

Halbuki bölümün başında McIlroy'un belirttiği gibi, ne `ls` programının bizim `lower` programımızdan haberi var, ne de `lower` programı yazılırken `ls` ile ilgili bir noktayı dikkate aldık.

Programımızdaki `getchar` fonksiyonu, aslında standart girdiden veri okuyan kısımdır. `puthcar` fonksiyonu ise standart çıktıya veri yazmaktan sorumludur. Bu fonksiyonlar `stdio.h` ile sağlanır. Bu yüzden kullanmak için _include_ etmemiz gerekir.

Eğer programı tek başına çalıştırırsak, biraz daha anlaşılabilir durum.

```
eaydin@eaydin-vt ~/devel/lower $ ./lower 
Bu bir Cümle
bu bir cümle
^C
```

Tanıdık geldi mi? Bu bölümün başlarında `cat` programını tek başına çalıştırdığımızda da benzer manzarayla karşılaşmıştık. Yine standart girdiden veri bekledi. Biz `Bu bir Cümle` yazdıktan sonra, kendisi yine standart çıktıya verinin aynısını yazdı, tıpkı `cat` gibi. Ama bu sefer karakterleri küçülttü. Yine Ctrl+c ile programımızı sonlandırarak çıktık.

Burada dikkat edilmesi gereken bir diğer nokta, programımızın parametrik şekilde girdi almaması. Örneğin karakterler dosyasını argüman olarak kendisine veremezdik, çünkü programımızda gelen argümanlarla neler yapılması gerektiğini belirtmedik. Böyle olunca argümanları görmezden gelecektir, ve yukarıdaki durumun aynısı gerçekleşecektir.

```
eaydin@eaydin-vt ~/devel/lower $ ./lower karakterler 
Argümanları Dikkate ALMADI
argümanları dikkate almadi
^C
```
