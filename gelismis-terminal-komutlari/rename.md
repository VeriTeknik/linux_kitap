# rename

Aslında GNU/Linux sistemlerde dosyayı yeniden adlandırmak için bir komut bulunmaz. Bir dosyayı yeniden adlandırmak, onu taşımak demektir, dolayısıyla **mv** komutu bu iş için kullanılır.

`rename` ise genellikle Larry Wall tarafından yazılan bir Perl betiğidir (script) ve Perl'in güçlü düzenli ifade (regular expression) yeteneklerini kullanarak karmaşık toplu yeniden isimlendirme işlemleri için kullanılır. `mv` komutu ile ancak döngüler veya karmaşık betikler yazılarak yapılabilecek işlemler, `rename` ile tek satırda çözülebilir.

**Önemli Not: İki Farklı `rename` Komutu**

Linux sistemlerinde yaygın olarak iki farklı `rename` komutu bulunabilir:
1.  **Perl `rename` (prename):** Bu bölümde anlatılan, Perl ifadesi kullanan güçlü versiyon. Paket yöneticilerinde adı `rename`, `prename` veya `perl-rename` olabilir.
2.  **`util-linux` `rename`:** Daha basit bir komuttur ve sadece metin dizgisi değiştirme işlemi yapar (`rename 'eski_metin' 'yeni_metin' dosya...`).

Hangi versiyonun sisteminizde `rename` olarak çalıştığını anlamak için `man rename` komutuna bakabilirsiniz. Eğer man sayfası Perl ifadelerinden bahsediyorsa, bu bölümde anlatılan versiyondur. Bu kitapta Perl `rename` komutunu ele alacağız. Sisteminizde kurulu değilse, paket yöneticinizle (`sudo apt install rename` veya `sudo dnf install prename` gibi) kurabilirsiniz.

Örneğin, elimizde rakamlardan oluşan jpg dosyaları olsun, ve bu dosyaların uzantılarına dokunmadan, başına sonuna kelime eklemek isteyelim.

```bash
eaydin@dixon ~/calisma/jpg $ ls
0145.jpg  0146.jpg  3.jpg  897653.jpg
eaydin@dixon ~/calisma/jpg $ rename -v 's/(\d*)\.jpg$/goruntu_$1_dosyasi\.jpg/' *.jpg
0145.jpg renamed as goruntu_0145_dosyasi.jpg
0146.jpg renamed as goruntu_0146_dosyasi.jpg
3.jpg renamed as goruntu_3_dosyasi.jpg
897653.jpg renamed as goruntu_897653_dosyasi.jpg
eaydin@dixon ~/calisma/jpg $ ls
goruntu_0145_dosyasi.jpg  goruntu_0146_dosyasi.jpg  goruntu_3_dosyasi.jpg  goruntu_897653_dosyasi.jpg
```

Eğer işlemi geri almak isteseydik, yani başında **kelime\_** ve sonunda **\_başkakelime** olup ortasında rakam bulunan jpg dosyalarını **rakam.jpg** şeklinde değiştirmek isteseydik,

```bash
eaydin@dixon ~/calisma/jpg $ rename -v 's/.*_(\d*)_.*(\.jpg)$/$1$2/' *.jpg
goruntu_0145_dosyasi.jpg renamed as 0145.jpg
goruntu_0146_dosyasi.jpg renamed as 0146.jpg
goruntu_3_dosyasi.jpg renamed as 3.jpg
goruntu_897653_dosyasi.jpg renamed as 897653.jpg
eaydin@dixon ~/calisma/jpg $ ls
0145.jpg  0146.jpg  3.jpg  897653.jpg
```

Sonuçlarınızı test etmek için, öncesinde **-n** parametresini kullanabilirsiniz. Örneğin dosya adlarını büyük/küçük harf ile değiştirmek isteseydik,

```bash
eaydin@dixon ~/calisma/jpg $ ls
0145.jpg  0146.jpg  3.jpg  897653.jpg
eaydin@dixon ~/calisma/jpg $ rename -n 'y/a-z/A-Z/' *
0145.jpg renamed as 0145.JPG
0146.jpg renamed as 0146.JPG
3.jpg renamed as 3.JPG
897653.jpg renamed as 897653.JPG
eaydin@dixon ~/calisma/jpg $ ls
0145.jpg  0146.jpg  3.jpg  897653.jpg
eaydin@dixon ~/calisma/jpg $ rename -v 'y/a-z/A-Z/' *
0145.jpg renamed as 0145.JPG
0146.jpg renamed as 0146.JPG
3.jpg renamed as 3.JPG
897653.jpg renamed as 897653.JPG
eaydin@dixon ~/calisma/jpg $ ls
0145.JPG  0146.JPG  3.JPG  897653.JPG
```
