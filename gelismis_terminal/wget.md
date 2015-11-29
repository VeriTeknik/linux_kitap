# wget

Bu kitapçıkta daha önce de karşılaştığımız komutlardan **wget**, internetten dosya çekmeye yarayan, **Web GET** sözcüklerinin kısaltması olan bir program.

İnternetten herhangi bir dosyayı indirmek için dosya adresini wget'e parametre olarak vermek yeterlidir.

```bash
wget http://www.plugged.in/downloads/plugged.sh
```

Yukarıdaki komutla bulunduğumuz dizine ```plugged.sh``` dosyası inecektir.

Bazen linkler istediğimiz isim yapısında olmayabilir. İndirilen dosyayı farklı isimle kaydetmek için aşağıdaki yöntem izlenebilir.

```bash
wget wget http://www.plugged.in/downloads/plugged.sh -O install.sh
```

Artık dosya ```plugged.sh``` ismiyle değil, ```install.sh``` ismiyle kaydedilecektir.

## Yarım Kalan İndirmeler

Normalde wget programı, indirilen dosya hali hazırda dizinde mevcutsa, yeni indirilen dosayı sonuna **.1 .2** gibi rakamlar koyarak yazar. Örneğin yukarıdaki örnekte **plugged.sh** dosyası zaten dizinde mevcutsa, yeniden indirdiğimizde wget dosyayı **plugged.sh.1** olarak kaydeder.

Oysa indirme sırasında dosya yarım kalmışsa, kaldığı yerden devam ettirmek için **-c** parametresini vermemiz yeterlidir.

```bash
wget -c http://www.plugged.in/downloads/plugged.sh
```

## Hız Limitleme

İndirme hızını limitlemek için ```--limit-rate``` parametresi kullanılır.

```bash
wget --limit-rate=400k http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
```

## Deneme Sayısı

Bazı durumlarda indirme işlemini kaç kez deneyeceğinizi belirtmek isteyebilirsiniz.

```bash
wget --tries=10 http://plugged.in/downloads/plugged.sh
```

## İndirme İşlemini Arka Planda Gerçekleştirme

Büyük dosyaları indirme işlemini arka planda yürütebilirsiniz. Böyle bir durumda wget size programın PID numarasını verecek, ve logları yazdığı dosyayı bildirecekir. Bu sayede isterseniz indirme işlemini log dosyasından takip edebilir, dilediğiniz zaman PID numarasını kullanarak programı durdurabilirsiniz.

```bash
# wget -b http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
Continuing in background, pid 25965.
Output will be written to ‘wget-log’.
```

## Liste Kullanımı

İndirilecek dosyaların bir listesini bir program ile oluşturabilirsiniz, veya yükleme yazılımınızda kullanmak üzere hazırlayabilirsiniz. Bu listedeki linkleri sırayla indirmesi için **wget** programına parametre olarak verebilirsiniz.

```bash
eaydin@dixon ~/calisma/wget $ cat indir.txt 
http://plugged.in/downloads/plugged.sh
http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
eaydin@dixon ~/calisma/wget $ wget -i indir.txt 
--2015-11-30 00:42:03--  http://plugged.in/downloads/plugged.sh
Resolving plugged.in (plugged.in)... 94.103.33.130, 2a00:7300:1::fff5
Connecting to plugged.in (plugged.in)|94.103.33.130|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8990 (8,8K) [application/x-sh]
Saving to: ‘plugged.sh’

100%[===============================================================================================>] 8.990       --.-K/s   in 0,02s   

2015-11-30 00:42:05 (422 KB/s) - ‘plugged.sh’ saved [8990/8990]

--2015-11-30 00:42:05--  http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
Resolving mirror.rackdc.com (mirror.rackdc.com)... 94.103.32.100, 2a00:7300:1::100
Connecting to mirror.rackdc.com (mirror.rackdc.com)|94.103.32.100|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 666894336 (636M) [application/x-iso9660-image]
Saving to: ‘CentOS-7-x86_64-Minimal-1503-01.iso’

 0% [                                                                                                ] 531.035      296KB/s
 ```


## İndirme Kontrolü

Bazı durumlarda indirme işleminden önce indirmenin mümkün olup olmadığını kontrol edebilirsiniz. Bunun için ```--spider``` seçeneği kullanılabilir. Bu seçeneğin en yaygın kullanımı, sisteminizin internet erişiminin olup olmadığı, internet erişimi varsa da DNS çözümlemelerini doğru yapıp yapmadığınız konusundadır. Aşağıdaki örnekleri inceleyelim.

```bash
eaydin@dixon ~/calisma/wget $ wget --spider google.com
Spider mode enabled. Check if remote file exists.
--2015-11-30 00:26:28--  http://google.com/
Resolving google.com (google.com)... 216.58.209.14, 2a00:1450:4017:803::200e
Connecting to google.com (google.com)|216.58.209.14|:80... connected.
HTTP request sent, awaiting response... 302 Found
Location: http://www.google.com.tr/?gfe_rd=cr&ei=lHtbVtjnGcuz8wfWmI6IAw [following]
Spider mode enabled. Check if remote file exists.
--2015-11-30 00:26:28--  http://www.google.com.tr/?gfe_rd=cr&ei=lHtbVtjnGcuz8wfWmI6IAw
Resolving www.google.com.tr (www.google.com.tr)... 216.58.208.99, 2a00:1450:4017:803::2003
Connecting to www.google.com.tr (www.google.com.tr)|216.58.208.99|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Remote file exists and could contain further links,
but recursion is disabled -- not retrieving.
eaydin@dixon ~/calisma/wget $ echo $?
0
eaydin@dixon ~/calisma/wget $ wget --spider google.c
Spider mode enabled. Check if remote file exists.
--2015-11-30 00:26:52--  http://google.c/
Resolving google.c (google.c)... failed: Name or service not known.
wget: unable to resolve host address ‘google.c’
eaydin@dixon ~/calisma/wget $ echo $?
4
```

Gördüğünüz gibi **google.com** için exit status 0 aldık, oysa **google.c** için exit status 4 döndü. Bu sayede scriptlerimize bir internet kontrol mekanizması koyabiliriz.

En yaygın kullanım, bu seçeneği ```-q``` (quiet) seçeneği ile kullanmaktır.

```bash
eaydin@dixon ~/calisma/wget $ wget -q --spider google.com
eaydin@dixon ~/calisma/wget $ echo $?
0
```

**NOT:** Yukarıdaki ifadelerde **$?** gösteriminin ne anlama geldiğini bilmiyorsanız, kitabın Bash Programlama bölümüne bakabilirsiniz.

## Tarayıcı Gibi Davranmak

İndirmek istediğiniz bazı linkler kaynağın bir tarayıcı gibi davranmasını isterler. 
Aşağıda bu tip senaryolarda kullanabileceğiniz iki yol gösterici örnek görebilrsiniz.

```bash
# wget --user-agent="Mozilla/5.0 (X11; U; Linux i686; \ 
en-US; rv:1.9.0.3) Gecko/2008092416 \
Firefox/3.0.3" http://indirilecek-link
```

Veya cookie kullanılarak login yapılabilir.

```bash
# wget --save-cookies cookies.txt \
--post-data 'user=kullanici&password=sifre' \
http://sayfa.com/login.php

# wget --load-cookies cookies.txt http://sayfa.com/indir.tar.gz
```

