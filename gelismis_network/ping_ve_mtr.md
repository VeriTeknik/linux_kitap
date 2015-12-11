# ping ve mtr

## ping

Network üzerinden yapılan işlemlerin vazgeçilmezi ping-pong mekanizması muhtemelen yakından tanıdığınız bir yapıya sahiptir. Burada ping komutunun pratik birkaç kullanımını göstereceğiz.

### Tekrar Sayısı Belirtme

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

### Sadece İstatistiği Gösterme

Yukarıdaki örneğin sonunda, ping istatistiğimizin yansıtıldığını görebilirsiniz. ```-q``` komutu (quiet output) sadece ping işleminin başını ve sonundaki istatistiği gösterir. ```-c``` ile birleştirilmezse, programı durdurmadan istatistiği göremeyiz.

```bash
eaydin@dixon ~ $ ping -c 5 -q google.com 
PING google.com (216.58.209.14) 56(84) bytes of data.

--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 51.350/52.214/54.528/1.232 ms
```

### Paket Boyutunu Değiştirme

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
PING google.com (216.58.208.110) 0(28) bytes of data.
8 bytes from sof01s11-in-f110.1e100.net (216.58.208.110): icmp_seq=1 ttl=55
8 bytes from sof01s11-in-f110.1e100.net (216.58.208.110): icmp_seq=2 ttl=55
8 bytes from sof01s11-in-f110.1e100.net (216.58.208.110): icmp_seq=3 ttl=55
8 bytes from sof01s11-in-f110.1e100.net (216.58.208.110): icmp_seq=4 ttl=55
```

Öte yandan dilediğimiz kadar büyük paketler de gönderebiliriz.






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



### ICMP Paketlerini Gözardı Etmek

Linux üzerinde ICMP paketlerini gözardı etmenin pek çok yolu var, ancak bu pek tavsiye edilmez. Yine de IPTABLES gibi kompleks yöntemler yerine basitçe paketleri gözardı etmek isterseniz aşağıdaki yöntemleri kullanabilirsiniz.

```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
```
```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts
```
