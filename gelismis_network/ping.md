# ping


Network üzerinden yapılan işlemlerin vazgeçilmezi ping-pong mekanizması muhtemelen yakından tanıdığınız bir yapıya sahiptir. Burada ping komutunun pratik birkaç kullanımını göstereceğiz.

## Tekrar Sayısı Belirtme

ping tek başına kullanıldığında sonsuza kadar işlemi sürdürür, ancak ```Ctrl+c``` ile programı durdurursanız, veya ```kill``` ile PID'yi öldürürseniz durur.

Ancak program daha çalıştırılırken ```-c``` parametresiyle kaç defa ping atıp sonlanacağını belirtebilirsiniz.

```bash
eaydin@dixon ~ $ ping -c 5 google.com
PING google.com (216.58.209.14) 56(84) bytes of data.
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=1 ttl=54 time=50.9 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=2 ttl=54 time=50.4 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=3 ttl=54 time=50.1 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=4 ttl=54 time=50.1 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=5 ttl=54 time=51.1 ms

--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 50.154/50.575/51.153/0.475 ms
```

## Sadece İstatistiği Gösterme

Yukarıdaki örneğin sonunda, ping istatistiğimizin yansıtıldığını görebilirsiniz. ```-q``` komutu (quiet output) sadece ping işleminin başını ve sonundaki istatistiği gösterir. ```-c``` ile birleştirilmezse, programı durdurmadan istatistiği göremeyiz.

```bash
eaydin@dixon ~ $ ping -c 5 -q google.com 
PING google.com (216.58.209.14) 56(84) bytes of data.

--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 51.350/52.214/54.528/1.232 ms
```

Eğer program sonlanmadan mevcut istatistiği görmek isterseniz, ```Ctrl+|``` sinyalini gönderebilirsiniz (Türkçe klavyelerde bunu gerçekleştirmek için ```Ctrl+Shift+<``` tuşlarına basmak gerekir).


```bash
eaydin@dixon ~ $ ping google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=1 ttl=55 time=85.4 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=2 ttl=55 time=84.5 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=3 ttl=55 time=85.0 ms
3/3 packets, 0% loss, min/avg/ewma/max = 84.541/85.016/85.302/85.451 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=4 ttl=55 time=85.0 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=5 ttl=55 time=85.2 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=6 ttl=55 time=86.5 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=7 ttl=55 time=86.0 ms
7/7 packets, 0% loss, min/avg/ewma/max = 84.541/85.429/85.512/86.557 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=8 ttl=55 time=85.3 ms
64 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=9 ttl=55 time=85.0 ms
^C
--- google.com ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8008ms
rtt min/avg/max/mdev = 84.541/85.382/86.557/0.647 ms
```

Yukarıdaki çıktıda ```3/3``` ve ```7/7``` ile başlayan satırlardan hemen önce ```Ctrl+|``` sinyali gönderilmiştir.

## Toplam Süre Tanımlama

Dilerseniz programa "x saniye boyunca ping gönder ve sonlandır" diyebilirsiniz. Böylece gelen paket cevabından ve sayısından bağımsız olarak işlemi sonlandırır. ```-w``` ile belirtilen değer, saniye cinsindendir.

Eğer hem ```-w``` hem de ```-c``` değerlerini tanımlarsanız, hangisi önce sonlanırsa o dikkate alınır.

Örneğin 15 paketi 20 saniyede göndermeye çalışırsanız aşağıdaki yapıyı uygulayabilirsiniz.

```bash
ping -c 15 -w 20 google.com
```

Bu durumda eğer 20 saniyeden kısa sürede 15 paket gönderilirse program sona erecektir. Veya 20 saniye dolarsa ve hala 15 paket gönderilmemişse bile program sona erecektir.


## ICMP Paketlerinin Boyutu ve Yapısı

Yukarıdaki örneklerimizde, ping mesajımızın gönderilmesini ifade eden satırda kaç Byte veri gönderdiğimizi görebilirsiniz. 56 Byte veri gönderiyoruz, aslında bu kısım "payload" olan kısım, yani ilettiğimiz anlamsız veri. Bu verinin IPv4 üzerinden iletilebilmesi için 28 byte veri daha iletmemiz gerekiyor. 20 Byte IP adresi, 8 Byte ICMP başlığı. Bu durumda payload + başlık bilgileri toplam 56+28=84 Byte veri iletiyoruz. ping komutu çıktısında ```56(84) bytes of data``` ile ifade edilen değer bunu gösteriyor.

Dilersek bu değeri ```-s``` parametresiyle değiştirebiliriz. Örneğin 20 Byte payload göndermek için,

```bash
eaydin@dixon ~ $ ping -s 20 google.com
PING google.com (216.58.209.14) 20(48) bytes of data.
28 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=1 ttl=54 time=51.0 ms
28 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=2 ttl=54 time=50.8 ms
```

Parametre belirtmediğimizde google.com'a 56(+28) Byte gönderip 64 Byte cevap almıştık. Son örneğimizdeyse 20(+28) Byte veri gönderip, 28 Byte cevap aldık. Gördüğünüz üzere cevaplar her zaman 8 Byte ekstra bilgi içermeli, çünkü bu bilgi gönderdiğimiz ICMP başlığının karşılığını içermektedir. Örneğin paket numarasını içermektedir. Ping mesajlarını gönderirken bir paket numarası veririz, cevap aldığımızda da hangi paketin cevabı olduğunu karşı taraftan öğreniriz. Bu sayede hem hangi paketin ne kadar sürede gidip geldiğini hesaplayabiliriz, hem de yolda kaybolan paketlerin sayısını anlarız. Ping gönderirken gördüğünüz ```icmp_seq``` paket sayısını (sequence) göstermektedir.

Bu durumda tahmin edeceğiniz üzere, gönderebileceğimiz en küçük paket 0 Byte payload içerip sadece IP adresi ve ICMP başlığından oluşan paket olacaktır. 0+28 Byte'lık bu pakete karşılık alacağımız cevap 8 Byte'lık ICMP başlığı karşılığı olmalıdır.

```bash
eaydin@dixon ~ $ ping -s 0 google.com
PING google.com (216.58.209.14) 0(28) bytes of data.
8 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=1 ttl=54
8 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=2 ttl=54
8 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=3 ttl=54
```

Burada atlanmaması gereken bir nokta var. Google'dan aldığımız cevapta tabii ki bizim IP adresmiz de vardı (yoksa paket bize ulaşmazdı) dolayısıyla gelen cevabın boyutu aslında program tarafından 20 Byte eksik gösterilmektedir.

Aşağıdaki ```tcpdump``` çıktısı bunu göstermektedir.

```bash
eaydin@dixon ~ $ sudo tcpdump -XX -n -vv -i wlan0 src 216.58.209.14
tcpdump: listening on wlan0, link-type EN10MB (Ethernet), capture size 65535 bytes
12:39:38.788969 IP (tos 0x0, ttl 54, id 0, offset 0, flags [none], proto ICMP (1), length 28)
    216.58.209.14 > 192.168.100.20: ICMP echo reply, id 7563, seq 9, length 8
	0x0000:  8056 f25b adab 8841 fc06 09c8 0800 4500  .V.[...A......E.
	0x0010:  001c 0000 0000 3601 b6db d83a d10e c0a8  ......6....:....
	0x0020:  6414 0000 e26b 1d8b 0009                 d....k....
```

Yukarıda gördüğünüz tcpdumo çıktısı bir ping işleminin (ICMP paketinin) yapısını göstermektedir. tcpdump kullanımını bilmiyorsanız şimdilik bunu önemsemenize gerek yok, ilerleyen bölümlerde göreceğiz, ancak ```proto ICMP (1), length 28``` yazan satır, gelen verinin aslında 8 byte değil, 28 byte olduğunu göstermektedir. Hemen altındaki satır, artık IPv4 başlık bilgilerinden paketi ayıklamıştır, burada ```length 8``` yazdığını görebilirsiniz.
 
Fark ettiyseniz 0 byte payload ile veri gönderdiğimizde, icmp paketlerinin sırasını ve cevap aldığımızı gördük, ancak kaç ms içinde cevap aldığımız bilgisi gelmedi. Bunun sebebi, bu bilginin payload'a yazılmasıdır. Karşı tarafa paket gönderdiğimizde, cevap vereceği zaman paketin payload kısmına zaman bilgisini de yerleştirir. Payload uzunluğunu 0 byte yaptığımızda timestamp göndermedik, dolayısıyla cevap olarak almadık. Bu durumda da zaman bilgisi edinmemiş olduk.

ping programının man sayfalarında ```-s``` ile belirttiğimiz Byte boyutu en az program içindeki ```timeval``` değeri kadarsa, timestamp dahil edileceğini belirtir. Bu değeri test ederek bulabilirsiniz.

```bash
eaydin@dixon ~ $ ping -s 15 -c 1 google.com
PING google.com (216.58.208.110) 15(43) bytes of data.
23 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=1 ttl=55
```

```bash
eaydin@dixon ~ $ ping -s 16 -c 1 google.com
PING google.com (216.58.208.110) 16(44) bytes of data.
24 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=1 ttl=55 time=86.5 ms
```

Yukarıdaki örneklerden gördüğünüz gibi, en az 16 Byte'lık alan bıraktığımızda paketin seyahat süresini öğrenebiliyoruz.

Öte yandan dilediğimiz kadar büyük paketler de gönderebiliriz. Örneğin

```bash
eaydin@dixon ~ $ ping -s 290 192.168.100.123
PING 192.168.100.123 (192.168.100.123) 290(318) bytes of data.
298 bytes from 192.168.100.123: icmp_seq=2 ttl=64 time=1.77 ms
298 bytes from 192.168.100.123: icmp_seq=3 ttl=64 time=1.62 ms
298 bytes from 192.168.100.123: icmp_seq=4 ttl=64 time=1.76 ms
```

Ping programı timestamp dışındaki bilgileri ASCII karakterlerle doldurur. Aşağıda iki tcpdump çıktısı görüyorsunuz, paketlerin sonunda ```ABCDE```... şeklinde devam eden kısım, söz ettiğimiz ASCII karakterlerdir.

```bash
eaydin@dixon ~ $ sudo tcpdump -XX -n -vv -i wlan0 dst 192.168.100.123
```
```
11:54:17.964372 IP (tos 0x0, ttl 64, id 54338, offset 0, flags [DF], proto ICMP (1), length 108)
    192.168.100.20 > 192.168.100.123: ICMP echo request, id 6485, seq 27, length 88
	0x0000:  fcaa 1456 6c5d 8056 f25b adab 0800 4500  ...Vl].V.[....E.
	0x0010:  006c d442 4000 4001 1c6e c0a8 6414 c0a8  .l.B@.@..n..d...
	0x0020:  647b 0800 39df 1955 001b 499d 6a56 0000  d{..9..U..I.jV..
	0x0030:  0000 fcb6 0e00 0000 0000 1011 1213 1415  ................
	0x0040:  1617 1819 1a1b 1c1d 1e1f 2021 2223 2425  ...........!"#$%
	0x0050:  2627 2829 2a2b 2c2d 2e2f 3031 3233 3435  &'()*+,-./012345
	0x0060:  3637 3839 3a3b 3c3d 3e3f 4041 4243 4445  6789:;<=>?@ABCDE
	0x0070:  4647 4849 4a4b 4c4d 4e4f                 FGHIJKLMNO
11:54:22.711764 IP (tos 0x0, ttl 64, id 55033, offset 0, flags [DF], proto ICMP (1), length 103)
    192.168.100.20 > 192.168.100.123: ICMP echo request, id 6489, seq 1, length 83
	0x0000:  fcaa 1456 6c5d 8056 f25b adab 0800 4500  ...Vl].V.[....E.
	0x0010:  0067 d6f9 4000 4001 19bc c0a8 6414 c0a8  .g..@.@.....d...
	0x0020:  647b 0800 99b7 1959 0001 4e9d 6a56 0000  d{.....Y..N.jV..
	0x0030:  0000 36dc 0a00 0000 0000 1011 1213 1415  ..6.............
	0x0040:  1617 1819 1a1b 1c1d 1e1f 2021 2223 2425  ...........!"#$%
	0x0050:  2627 2829 2a2b 2c2d 2e2f 3031 3233 3435  &'()*+,-./012345
	0x0060:  3637 3839 3a3b 3c3d 3e3f 4041 4243 4445  6789:;<=>?@ABCDE
	0x0070:  4647 4849 4a                             FGHIJ
```

Gördüğünüz gibi aldığımız cevabın boyutu, gönderdiğimiz paketin boyutuna bağlı. Bu durum çok rağbet gören sistemlerin gereksiz yere çok paket transfer etmesine sebep olabilir. Örneğin Google muhtemelen gün içerisinde çok ciddi sayıda ping isteği alıyordur. Gelen bütün ping paketlerine, paketin isteği kadar boyutta cevap vermek gereksiz trafik yüküne sebep olabilir. Bunun önüne geçmek için paketi küçültürler.


```bash
eaydin@dixon ~ $ ping -s 65 google.com
PING google.com (216.58.208.110) 65(93) bytes of data.
72 bytes from sof01s11-in-f14.1e100.net (216.58.208.110): icmp_seq=1 ttl=55 (truncated)
```

Yukarıdaki örnekte gördüğünüz ```(truncated)``` ifadesi paketin istediğimiz değerden küçük geldiği anlamına gelir. Gerçekten de 65 byte veri gönderdik, 65+8=73 byte cevap beklerdik ancak 72 byte geldi. Google bu kitabı yazıldığı tarihte ICMP cevaplarını en fazla 72 byte olacak şekilde düzenlemiştir.


## ICMP Paketlerini Gözardı Etmek

Linux üzerinde ICMP paketlerini gözardı etmenin pek çok yolu var, ancak bu pek tavsiye edilmez. Yine de IPTABLES gibi kompleks yöntemler yerine basitçe paketleri gözardı etmek isterseniz aşağıdaki yöntemleri kullanabilirsiniz.

```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
```
```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts
```

## Ping Flood

İki ardışık ICMP paketi arasındaki süreyi ```-i``` parametresiyle saniye cinsinden düzenleyebilirsiniz.

```bash
eaydin@dixon ~ $ ping -i 3 google.com
PING google.com (216.58.209.14) 56(84) bytes of data.
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=1 ttl=54 time=51.1 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=2 ttl=54 time=53.2 ms
```

Kesirli değerler verebilirsiniz.

```bash
eaydin@dixon ~ $ ping -i 0.3 google.com
PING google.com (216.58.209.14) 56(84) bytes of data.
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=1 ttl=54 time=59.9 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=2 ttl=54 time=52.2 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=3 ttl=54 time=51.4 ms
```

Tahmin edeceğiniz üzere, çok küçük değerler birim zamanda çok sayıda paket gönderilmesine sebep olur. Bu durum bilgisayarınızı "saldırgan" pozisyonuna düşürebilir. Bunu engellemek için 0.2 saniyenin altındaki değerler için root yetkisi gerekmektedir.

```bash
eaydin@dixon ~ $ ping -i 0.19 google.com
PING google.com (216.58.209.14) 56(84) bytes of data.
ping: cannot flood; minimal interval allowed for user is 200ms
```

Herhangi bir sebeple birim zamanda çok sayıda paket göndermek isterseniz, ping komutunun flood özelliğini kullanabilirsiniz. Bu durumda çok sayıda paket gönderilip alınacağı için tamamı ekrana yazdırılmaz, ancak her paket gönderildiğinde ekrana bir **.** (nokta) işareti yazdırılır, her paket alındığındaysa bir **backspace** işareti yazdırılır, yani bir önceki nokta silinir.

```bash
eaydin@dixon ~ $ sudo ping -f 192.168.100.123
PING 192.168.100.123 (192.168.100.123) 56(84) bytes of data.
...............................^C     
--- 192.168.100.123 ping statistics ---
9461 packets transmitted, 9430 received, 0% packet loss, time 20653ms
rtt min/avg/max/mdev = 0.695/2.223/114.147/3.504 ms, pipe 10, ipg/ewma 2.183/2.072 ms
```

Yukarıdaki örnekte 20 saniye içerisinde 9461 paket gönderilmiş, 9430 cevap alınmıştır. Bu yüzden **^C** işaretinden önce (9461-9430=) 31 tane nokta işareti var, bunlar cevapsız paketleri gösterir.