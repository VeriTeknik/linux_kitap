# ip

ip komutu, uzun süredir geliştirilmesi durdurulmuş bir takım komutların modern network notasyonlarını destekler ve bu komutların birleştirilmiş halini sunar.

Sistem üzerindeki NIC cihazlarının IP adreslerini öğrenmek için:

```bash
ip addr show
```

```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
    link/ether a0:d3:c1:5e:68:ec brd ff:ff:ff:ff:ff:ff
    inet6 fe80::a2d3:c1ff:fe5e:68ec/64 scope link 
       valid_lft forever preferred_lft forever
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 80:56:f2:5b:ad:ab brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.20/24 brd 192.168.100.255 scope global wlan0
       valid_lft forever preferred_lft forever
    inet6 fe80::8256:f2ff:fe5b:adab/64 scope link 
       valid_lft forever preferred_lft forever
```

Örneğin ```eth0``` cihazına ```192.168.16.5``` IP'sini eklemek için:

```bash
ip addr add 192.168.16.5 dev eth0
```

Veya aynı IP'yi bu cihazdan silmek için:

```bash
ip addr del 192.168.16.5 dev eth0
```

Aşağıdaki komutlarla bir cihazın çalışmasını veya durmasını sağlayabilirsiniz.

```bash
ip link set eth0 up
```

```bash
ip link set eth0 down
```

Aşağıdaki komut, route tablomuzu gösterir.

```bash
eaydin@dixon ~ $ ip route show
default via 192.168.100.1 dev wlan0  proto static 
```

Eğer belirli bir cihaz için route eklemek, silmek istiyorsak, veya default route'u belirlemek istiyorsak aşağıdaki komutları kullanabiliriz.


```bash
ip route add 10.2.19.0/24 via 192.168.16.1 dev eth0
```

```bash
ip route del 10.2.19.0/24
```


```bash
ip route add default via 192.168.16.1
```