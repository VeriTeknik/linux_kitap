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

## Route Tablosuna Müdahale Etmek
Route tablosuna yeni bir gateway eklenebilir, silinebilir veya bazı yollar engellenebilir. Bu bölümde örneklerini inceleyeceğiz.

### Default Gateway Eklemek

Belirli bir ağ aralığında (network range) bulunmayan paketler default gateway'e yönlenir. Örneğin yukarıdaki routing tablosunda, 172.16.77.0/24, 172.16.148.0/24 ve 192.168.99.0/24 dışındaki adreslere yönlendirilecek bütün paketler, 192.168.99.1 adresine gönderilir. Burası bulunduğumuz ağdaki router'dır, kendisi de gelen paketleri Internet Servis Sağlayıcıya iletir.

```bash
route add default gw 192.168.99.5
```

### Default Gateway Silmek

Default Gateway'in, "nereye gideceğini bilmediğimiz paketlerin gönderildiği yer" olduğunu öğrendik. Öyleyse sistemimizde sadece bir tane default gateway olabilir. Bu durumda default gateway'i silmek için aşağıdaki komutu kullanabiliriz.

```bash
route del default
```

**NOT:** Bazı durumlarda sistemde birden fazla default gateway tanımlanabilir. Teorik olarak gerekmiyor olsa da pratikte nadiren rastlanılan bir durumdur. Bu özel durumlar kapsamımız dışında olduğundan incelemiyoruz. Sadece böyle senaryoların karşılaşılabildiğini belirtme ihtiyacı hissettik.
