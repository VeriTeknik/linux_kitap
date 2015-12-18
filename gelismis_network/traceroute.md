# traceroute

traceroute komutu bir sunucuya erişene kadar gidilen yolu tespit etmeye yarar. Bunun için bu yol boyunca uğradığı bütün sunuculara ICMP paketleri gönderir. Nasıl çalıştığını anlamak için ping komutunda karşımıza çıkan (ancak üstünde durmadığımız) TTL (Time To Live) mekanizmasını anlamak gerekir.

## TTL

TTL basitçe, gönderdiğimiz paketin kaç noktadan geçmesine izin verdiğimizi ifade eder. Ping programını incelerken kullandığımız örneklerde genellikle bu değerin 54 olduğunu gördük. Bu şu anlama geliyor, "bu paket en fazla 54 noktadan geçebilir, daha fazlasına iletilmemeli."

Ağ'daki (internet veya yerel ağ fark etmez) her nokta (sunucu, router vb.) kendisine gelen paketin TTL'ine bakar, eğer bu değer 1'den büyükse, değeri 1 azaltıp bir sonraki  noktaya iletir, eğer değer 1'se, o zaman paketi göz ardı, eder, ICMP hata pakedi gönderir ve bu bilgi içerisinde kendi IP adresi de yer alır.

Öyleyse uzaktaki bir sunucuya ping atarken, eğer TTL değerimizi aradaki router sayısından daha küçük tutarsak, bu paket hedef sunucuya asla ulaşmaz, ancak arada bir noktadan cevap alırız.

Örneğin google.com sunucusuna ping atmak istersek, ancak TTL değerini 1 tutarsak,

```bash
eaydin@dixon ~ $ ping -t 1 google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
From 192.168.100.1 icmp_seq=1 Time to live exceeded
```

TTL değerimizi 2'ye çıkardığımızda,

```bash
eaydin@dixon ~ $ ping -t 2 google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
From 81.212.171.130.static.turktelekom.com.tr (81.212.171.130) icmp_seq=1 Time to live exceeded
```

Gidere arttırırsak, google'e ulaşana kadar geçtiğimiz yolu deşifre edebiliriz...

```bash
eaydin@dixon ~ $ ping -t 3 google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
From 93.155.0.184 icmp_seq=1 Time to live exceeded
```

```bash
eaydin@dixon ~ $ ping -t 4 google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
From 81.212.106.225.static.turktelekom.com.tr (81.212.106.225) icmp_seq=4 Time to live exceeded
```

```bash
eaydin@dixon ~ $ ping -t 5 google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
From 195.175.173.172.06-ulus-xrs-t2-2.06-ulus-t3-7.statik.turktelekom.com.tr (195.175.173.172) icmp_seq=1 Time to live exceeded
```

Traceroute programı da aslında arka planda bu mantığı uygular. Sırasıyla ICMP paketlerimizin TTL'ini artırarak ilgili noktaya ulaşana kadar bu işi tekrar eder.

## Kullanımı

Kullanımı gayet basittir ve çıktısı aşağıdaki gibidir.

```bash
eaydin@dixon ~ $ traceroute google.com
traceroute to google.com (216.58.209.14), 30 hops max, 60 byte packets
 1  192.168.100.1 (192.168.100.1)  8.191 ms  8.391 ms  8.403 ms
 2  81.212.171.130.static.turktelekom.com.tr (81.212.171.130)  42.977 ms  45.323 ms  45.343 ms
 3  93.155.0.184 (93.155.0.184)  45.342 ms  45.360 ms  45.368 ms
 4  81.212.106.225.static.turktelekom.com.tr (81.212.106.225)  45.373 ms  45.377 ms  45.401 ms
 5  195.175.174.42.06-ulus-xrs-t2-1.06-ulus-t3-7.statik.turktelekom.com.tr (195.175.174.42)  45.404 ms  45.414 ms  45.420 ms
 6  195.175.166.204.00-gayrettepe-xrs-t2-1.06-ulus-xrs-t2-1.statik.turktelekom.com.tr (195.175.166.204)  5123.341 ms * *
 7  * * *
 8  * * *
 9  * 209.85.250.69 (209.85.250.69)  74.476 ms  76.287 ms
10  209.85.142.189 (209.85.142.189)  76.328 ms  76.329 ms  76.330 ms
11  sof01s12-in-f14.1e100.net (216.58.209.14)  76.291 ms  52.555 ms  52.308 ms
```

Yukarıdaki çıktıyı incelediğimizde, 11 noktadan sonra Google'a erişebildiğimiz görüyoruz. Gerçekten de TTL 11 ile ping atarsak, Google'un cevap vereceğini görebiliriz.

```bash
eaydin@dixon ~ $ ping -t 11 google.com
PING google.com (216.58.209.14) 56(84) bytes of data.
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=1 ttl=54 time=51.5 ms
```

Ancak arada bazı noktalarda "*" işaretleri mevcut. Bu işaretler, ilgili sunucuya giderken pinglenen noktaların cevap vermediği, veya DNS çözümlemesinde hata yaşandığı gibi pek çok şeyi ifade edebilir.

Farkındaysanız 9. satırda başta bir "*" işareti var, sonra satır normal devam ediyor. Bunun sebebi, traceroute her noktayı pinglerken, 3 defa deniyor. Belli ki denemelerinden birinde başarısız olmuş. Her satır için 3 farklı ms değeri olması, ancak 9. satırda sadece 2 farklı ms değeri olması da bundan kaynaklanmaktadır.

Her nokta (hop) için yapılacak pingleme (query) miktarını ```-q``` parametresiyle değiştirebilirsiniz. Ayrıca hostname çözümlemesi yapmayıp, doğrudan çıktıda IP adreslerinin görünmesini de ```-n``` ile sağlayabilirsiniz.

```bash
eaydin@dixon ~ $ traceroute google.com -n -q 1
traceroute to google.com (216.58.211.14), 30 hops max, 60 byte packets
 1  192.168.100.1  5.363 ms
 2  81.212.171.130  33.504 ms
 3  93.155.0.184  35.415 ms
 4  81.212.106.225  35.994 ms
 5  195.175.174.42  37.083 ms
 6  *
 7  195.175.174.59  45.340 ms
 8  72.14.197.192  53.157 ms
 9  209.85.248.54  60.998 ms
10  64.233.175.34  76.644 ms
11  216.239.47.189  82.119 ms
12  216.58.211.14  77.069 ms
```

Traceroute algoritmasında TTL değerinin 1'den başladığını ifade etmiştik. Dilerseniz bu değeri 1'den başlatmak yerine farklı değerle taramaya başlayabilirsiniz.

```bash
eaydin@dixon ~ $ traceroute google.com -n -f 5
traceroute to google.com (216.58.211.14), 30 hops max, 60 byte packets
 5  195.175.174.42  34.707 ms  35.731 ms  37.701 ms
 6  195.175.166.204  44.291 ms  44.878 ms  44.895 ms
 7  * * *
 8  72.14.197.194  55.306 ms 72.14.197.192  55.963 ms  55.967 ms
 9  209.85.248.54  65.153 ms  67.279 ms  69.913 ms
10  64.233.175.34  78.079 ms  68.314 ms  76.712 ms
11  216.239.47.189  79.059 ms  88.210 ms  89.261 ms
12  216.58.211.14  69.281 ms  78.807 ms  78.197 ms
```

## ICMP, UDP ve TCP Kullanımı

traceroute'un çalışma prensibini anlamak için ping komutunu farklı TTL'ler kullanarak denedik. Aslında traceroute komutu, parametre belirtilmediğinde ping atarak bu işlemi gerçekleştirmez, ancak mekanizmanın rahat anlaşılması için bu yöntemi tercih ettik.

traceroute programı, parametre kullanılmadığında UDP ile yol çıkarmaya çalışır. Bunu nasıl yaptığını ve sebebini aşağıda inceleyeceğiz, ancak traceroute ile istediğimiz metodu kullanarak yol çıkarmak mümkün, yeter ki ağ yapısı buna müsaade etsin.

Bağlantı tipini belirlemek için ```-M``` (method) parametresi kullanılır. Örneğin TCP yöntemini seçmek için

```bash
traceroute google.com -M tcp
```

**NOT:** MS Windows sistemlerde traceroute programı ```tracert``` ismiyle bulunur. Bunun sebebi, eski DOS sistemlerinde dosya isimlerine getirilen kısıtlamadır. Eski DOS sistemlerinde dosya adları en fazla 8 karakter olabilir, dosya uzantıları ise 3 karakter olabilirdi. Bunun için programı ```tracert.exe``` olarak isimlendirmişlerdir. Bu programın bir diğer farklılığı, standart tarama mekanizması olarak UDP değil, ICMP paketleri kullanmasıdır.

### UDP ile Kullanımı

Daha önce belirttiğimiz gibi, traceroute programı GNU/Linux üzerindeki dağıtımında parametre belirtilmeyince bu tekniği kullanır. UDP taramasıyla program, karşı tarafın UDP üzerinde bir servis çalıştığı "düşünülmeyen" portlarına datagram gönderir. Eğer karşı tarafta gerçekten bu portlarda bir servis çalışmıyorsa, cevap olarak "Destination Port Unreachable" içerikli bir ICMP paketi gönderir.

traceroute UDP üzerinden tarama yaparken, 33434 portundan başlar, her hop'ta değeri bir artırır.