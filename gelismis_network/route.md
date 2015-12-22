# route

```route``` komutu da ```ifconfig``` gibi artık desteklenmeyen komutlar arasındadır. Yerin ```ip``` komutunun route parametresi kullanılmalıdır.

## Mevcut Route Tablosunu Görmek

Komutu doğrudan çalıştırdığınızda route tablosunu gösterir.

```bash
eaydin@dixon ~ $ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         192.168.99.1    0.0.0.0         UG    0      0        0 wlan0
172.16.77.0     *               255.255.255.0   U     0      0        0 vmnet1
172.16.148.0    *               255.255.255.0   U     0      0        0 vmnet8
192.168.99.0    *               255.255.255.0   U     9      0        0 wlan0
```

```-n``` parametresiyle adreslerin nümerik değerleri gösterilir.

```bash
eaydin@dixon ~ $ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.99.1    0.0.0.0         UG    0      0        0 wlan0
172.16.77.0     0.0.0.0         255.255.255.0   U     0      0        0 vmnet1
172.16.148.0    0.0.0.0         255.255.255.0   U     0      0        0 vmnet8
192.168.99.0    0.0.0.0         255.255.255.0   U     9      0        0 wlan0
```

Linux çekirdeği daha hızlı routing (yönlendirme) yapabilmek için bir routing cache tutar. Cache değerlerini görmek için ```route -Cn``` komutunu kullanabilirsiniz.

