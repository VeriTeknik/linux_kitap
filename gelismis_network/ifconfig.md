# ifconfig

Bugün GNU/Linux kullanan sistem yöneticilerinin çoğu eski alışkanlıklarından dolayı ağ cihazlarını (NIC) ayarlamak için ```ifconfig``` komutunu kullanırlar, ancak ifconfig uzun süredir geliştirilmeyen ve artık terk edilen (deprecated) bir yazılımdır. Dolayısıyla kullanmamak daha doğrudur.

Programın geliştirilmesinin durdurulduğu ilk olarak Debian mail listinde paylaşıldı. Bugün CentOS 7 gibi sistemlerde bu komut bulunmamaktadır.

Duyuruyu şuradan görebilirsiniz: https://lists.debian.org/debian-devel/2009/03/msg00780.html

ifconfig'in artık geliştirilmemesinin pek çok sebebi vardır, ve bunun yerine ```ip``` komutunun kullanımı tavsiye edilir. Örneğin netmask tanımlanmasına CIDR notasyonu desteklenmez, ```255.255.255.248``` yerine ```/29``` yazamazsınız.

Öte yandan, bazı eski sistemlerde veya gömülü sistemlerde ```ip``` komutu bulunmayabilir. Bunun için nasıl çalıştığı hakkında fikir sahibi olmakta fayda var.

## Mevcut Cihazları Listelemek

```ifconfig``` programı parametresiz çağırıldığında mevcut ağ cihazlarını (NIC, Network Interface Controller) listeler.

```bash
eaydin@dixon ~ $ ifconfig
eth0      Link encap:Ethernet  HWaddr a0:d3:c1:5e:68:ec  
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:7869 errors:0 dropped:0 overruns:0 frame:0
          TX packets:7869 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:3719917 (3.7 MB)  TX bytes:3719917 (3.7 MB)

wlan0     Link encap:Ethernet  HWaddr 80:56:f2:5b:ad:ab  
          inet addr:192.168.99.20  Bcast:192.168.99.255  Mask:255.255.255.0
          inet6 addr: fe80::8256:f2ff:fe5b:adab/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:416423 errors:0 dropped:0 overruns:0 frame:0
          TX packets:281587 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:531301670 (531.3 MB)  TX bytes:38844271 (38.8 MB)
```

