# awk ve sed

GNU/Linux üzerinde **awk** ve **sed** ikilisi, standart girdi/çıktı işlemlerinde sıkça kullanılan, metin değişikliğine yarayan programlardır.

Her ne kadar her ikisi için de kompleks programlar yazılabiliyor olsa da, bu bölümde ana hatlarıyla nasıl kullanıldıklarını ve en sık işinize yarayacak yönlerini inceleyeceğiz.

## awk

awk çoğunlukla tablarla ayrılmış verilerin belirli sütununu almakta veya sırasını değiştirmekte kullanılır. Örneğin `ls -l` çıktısını ayıklamak için aşağıdaki yöntem kullanılabilir.

```bash
eaydin@dixon ~/calisma $ ls -l *.txt
-rw-r--r-- 1 eaydin eaydin 33 Nov 28 14:56 test2.txt
-rw-r--r-- 1 eaydin eaydin 55 Nov 28 13:49 test3.txt
-rw-r--r-- 1 eaydin eaydin 69 Nov 28 13:49 test4.txt
-rw-r--r-- 1 eaydin eaydin 33 Nov 28 15:01 test.txt
-rw-r--r-- 1 eaydin eaydin 33 Nov 28 15:00 yedek-test2.txt
-rw-r--r-- 1 eaydin eaydin 40 Nov 28 15:00 yedek-test.txt
eaydin@dixon ~/calisma $ ls -l *.txt | awk '{print "Dosya Adı: "$9" Boyut: "$5" kB"}'
Dosya Adı: test2.txt Boyut: 33 kB
Dosya Adı: test3.txt Boyut: 55 kB
Dosya Adı: test4.txt Boyut: 69 kB
Dosya Adı: test.txt Boyut: 33 kB
Dosya Adı: yedek-test2.txt Boyut: 33 kB
Dosya Adı: yedek-test.txt Boyut: 40 kB
```

Ayracı boşluk karakteri haricinde bir karakter olarak tanımlamak için **-F** argümanı kullanılır.

```bash
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd
saned:x:113:123::/home/saned:/bin/false
speech-dispatcher:x:114:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/sh
eaydin:x:1000:1000:Emre,,,:/home/eaydin:/bin/bash
nvidia-persistenced:x:115:124:NVIDIA Persistence Daemon,,,:/:/sbin/nologin
sshd:x:116:65534::/var/run/sshd:/usr/sbin/nologin
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd | awk -F":" '{print "Kullanıcı: "$1}'
Kullanıcı: saned
Kullanıcı: speech-dispatcher
Kullanıcı: eaydin
Kullanıcı: nvidia-persistenced
Kullanıcı: sshd
```

Sitem üzerinde tanımlanmış değişkenleri alıp işleyebiliriz de.

```bash
eaydin@dixon ~/calisma $ X=10
eaydin@dixon ~/calisma $ echo $X
10
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd | \
awk -F":" -v val=$X '{print val " Kullanıcı: "$1}'
10 Kullanıcı: saned
10 Kullanıcı: speech-dispatcher
10 Kullanıcı: eaydin
10 Kullanıcı: nvidia-persistenced
10 Kullanıcı: sshd
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd | \
awk -F":" -v val=$X '{print val+1 " Kullanıcı: "$1;val=val+1}'
11 Kullanıcı: saned
12 Kullanıcı: speech-dispatcher
13 Kullanıcı: eaydin
14 Kullanıcı: nvidia-persistenced
15 Kullanıcı: sshd
```

Dosya içeriğindeki satırları etrafında tırnakla gösterebilmek için, şöyle teknikler kullanılabilir.

```bash
eaydin@dixon ~/calisma $ cat liste
PHP
Python
Ruby
Go
Perl
eaydin@dixon ~/calisma $ awk '{print q $0 q}' q="'" liste
'PHP'
'Python'
'Ruby'
'Go'
'Perl'
eaydin@dixon ~/calisma $ awk '{print q $0 q}' q='"' liste
"PHP"
"Python"
"Ruby"
"Go"
"Perl"
```

Aşağıdaki örnek, dosyayı okuyup ilk karaktere bakıyor, ilk karaker **P** ise bütün satırı yazdırıyor, değilse sadece ilk karakteri yazdırıyor.

```bash
eaydin@dixon ~/calisma $ awk '{ if(substr($0,0,1)=="P") {print $0} \
else { print substr($0,0,1)}}' liste
PHP
Python
R
G
Perl
```

**`awk` Dahili Değişkenler ve Bloklar:**

`awk` ayrıca birçok dahili değişkene sahiptir:
*   `NR`: O an işlenen kaydın (genellikle satırın) numarası.
*   `NF`: O an işlenen kayıttaki alan (field) sayısı.
*   `$0`: Tüm satırın içeriği.

Ayrıca özel `BEGIN` ve `END` blokları bulunur:
*   `BEGIN { ... }`: `awk` herhangi bir girdi satırını okumadan *önce* çalıştırılır. Başlık yazdırmak veya değişkenleri başlatmak için kullanılır.
*   `END { ... }`: `awk` tüm girdi satırlarını okuduktan *sonra* çalıştırılır. Toplamları yazdırmak veya özet bilgi vermek için kullanılır.

Örnek:
```bash
# Dosyadaki her satırın numarasını ve alan sayısını yazdır, sonunda toplam satır sayısını belirt
awk '{ print "Satır No:", NR, "Alan Sayısı:", NF, $0 } END { print "Toplam Satır:", NR }' liste
```

## sed

sed dosyalar veya standart girdi üzerinde değişiklik yapmaya yarayan bir araçtır. Regular expression desteklediği gibi basit işlemler için de sıklıkla kullanılır. Örneğin yukarıda kullandığımız **liste** dosyasında içinde PHP geçen satırı **\#** ile açıklama satırı haline getirmek isteseydik

```bash
eaydin@dixon ~/calisma $ sed '/PHP/s/^/#/' liste
#PHP
Python
Ruby
Go
Perl
```

Yukarıdaki komutun yaptığı işlem basittir. **liste** dosyası içinde **PHP** ifadesini saradı, **s** ile değişiklik yapılacağı bildirildi, **^** ile satırın başına **\#** karakterinin ekleneceği ifade edildi.

Eğer bu satırı silmek isteseydik;

```bash
eaydin@dixon ~/calisma $ sed '/PHP/d' liste
Python
Ruby
Go
Perl
```

Eğer başında **P** geçen bütün satırları silmek isteseydik;

```bash
eaydin@dixon ~/calisma $ sed '/^P/d' liste
Ruby
Go
```

Çoğunlukla sed kullanımında **s** komutunu başta görürsünüz. Örneğin satırlarımızda **h** harflerini bulsak ve bu harften önce ve sonra **&lt;** ile **&gt;** işaretleri koymak istesek;

```bash
eaydin@dixon ~/calisma $ sed 's/[hH]/<&>/' liste 
P<H>P
Pyt<h>on
Ruby
Go
Perl
```

Öte yandan, sadece **h** harflerinin değil, içinde **h** harfi geçen satırın tamamını etiketlemek isteseydik,

```bash
eaydin@dixon ~/calisma $ sed 's/.*[hH].*/<&>/' liste 
<PHP>
<Python>
Ruby
Go
Perl
```

Gördüğünüz gibi bu örneklerde, bulduğumuz satırı ifade eden karakter **&** ile gösteriliyor.

**Global Değişiklik (`g` flagi):**

Varsayılan olarak, `sed`'in `s` komutu bir satırda bulduğu *ilk* eşleşmeyi değiştirir. Eğer satırdaki *tüm* eşleşmeleri değiştirmek istiyorsanız, komutun sonuna `g` (global) bayrağını eklemelisiniz:

```bash
# 'liste' dosyasındaki tüm 'P' harflerini 'X' ile değiştir
sed 's/P/X/g' liste 
```

**Yerinde Düzenleme (`-i` seçeneği):**

`sed` normalde sonucu standart çıktıya yazar, orijinal dosyayı değiştirmez. Değişiklikleri doğrudan orijinal dosyaya uygulamak için `-i` seçeneği kullanılır.

**DİKKAT:** `-i` seçeneği tehlikeli olabilir, çünkü orijinal dosyanın üzerine yazar. Yanlış bir komutla dosyanızı bozabilirsiniz. Kullanmadan önce komutunuzu test etmeniz veya bir yedek oluşturmanız şiddetle tavsiye edilir.

Bazı `sed` sürümleri, `-i` ile birlikte bir uzantı belirterek otomatik yedekleme yapmanıza olanak tanır:

```bash
# Değişiklikleri doğrudan 'liste' dosyasına uygula, orijinalini 'liste.bak' olarak yedekle
sed -i.bak 's/PHP/Hypertext Preprocessor/' liste 
```
Eğer yedek istemiyorsanız (ve dikkatliyseniz) sadece `-i` kullanabilirsiniz:
```bash
# Değişiklikleri doğrudan 'liste' dosyasına uygula (YEDEK YOK!)
# sed -i 's/PHP/Hypertext Preprocessor/' liste 
```

### Yaygın Sed Kullanımları

#### Boş Satırları Silme

Dosyamıza boş satırlar ekleyelim.

```bash
eaydin@dixon ~/calisma $ cat liste 


PHP
Python

Ruby
Go
Perl
```

Bu satırları silmek için aşağıdaki yöntem kullanılabilir,

```bash
eaydin@dixon ~/calisma $ sed '/^\s*$/d' liste
PHP
Python
Ruby
Go
Perl
```

#### Satır Öncesi Boşlukları Silme

```bash
eaydin@dixon ~/calisma $ cat liste
PHP
   Python
Ruby
Go
Perl
eaydin@dixon ~/calisma $ sed 's/^[ ]*//' liste
PHP
Python
Ruby
Go
Perl
```

#### Satır Sonrası Boşlukları Silme

```bash
eaydin@dixon ~/calisma $ sed 's/[ ]*$//' liste
```

#### Birden Fazla Boşluğu Tek Boşluk ile Değiştirme

```bash
eaydin@dixon ~/calisma $ cat liste
PHP
   Python
Ruby     .  
Go  Kolay    Öğrenilir
Perl
eaydin@dixon ~/calisma $ sed 's/  */ /g' liste
PHP
 Python
Ruby . 
Go Kolay Öğrenilir
Perl
```

#### Açıklama Satırı Ekleme

İçinde **o** veya **r** harfi geçen satırları **\#** ile açıklama satırı haline getirelim.

```bash
eaydin@dixon ~/calisma $ sed -e '/[o|r]/s/^#*/#/' liste
PHP
#Python
Ruby  
#Go
#Perl
```

#### Açıklama Satırlarını Kaldırma

Dosyanızda açıklama satırları çok çeşitli hallerde olabilir, aşağıdaki örneği inceleyelim.

```bash
eaydin@dixon ~/calisma $ cat aciklama
PHP
#Python
#Python2
Ruby  
###Go
###Go2
# Perl
# Per2
  #C
  #C2
```

Burada hem başında boşluk olan satırlar var, hem birden fazla **\#** ile başlayan satırlar var, hem de **\#**den sonra boşluk içeren satırlar var. Tamamını _uncomment_ edebilmek için aşağıdaki yöntem kullanılabilir.

```bash
eaydin@dixon ~/calisma $ sed 's/^[ ]*#*//' aciklama
PHP
Python
Python2
Ruby  
Go
Go2
 Perl
 Per2
C
C2
```

İçinde sadece **2** geçen satırların açıklamasını kaldırmak istersek,

```bash
eaydin@dixon ~/calisma $ sed 's/^[ ]*#*\(.*2\)/\1/' aciklama
PHP
#Python
Python2
Ruby  
###Go
Go2
# Perl
 Per2
  #C
C2
```

#### Satır Numaralandırma

`sed = dosyaismi` kullanımı, her satırı tek tek yazdırıp, öncesine satır numarasını yazdırır. Örneğin

```bash
eaydin@dixon ~/calisma $ sed = liste
1
PHP
2
Python
3
Ruby
4
Go
5
Perl
```

Öyleyse sed ile bu satırlarda **\n** karakterlerini **\t** ile değiştirip tablarla satırlarımızı gösterebiliriz.

```bash
eaydin@dixon ~/calisma $ sed = liste | sed 'N;s/\n/\t/'
1    PHP
2    Python
3    Ruby
4    Go
5    Perl
```
**Not:** Basit satır numaralandırma için genellikle `nl` veya `cat -n` komutları daha pratik olabilir:
```bash
nl liste
cat -n liste
```
