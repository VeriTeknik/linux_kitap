# `ip` Komutu (iproute2)

`ip` komutu, modern Linux sistemlerinde ağ yapılandırmasını yönetmek için kullanılan temel araçtır. `iproute2` paketinin bir parçasıdır ve `ifconfig`, `route`, `arp`, `netstat -r` gibi eski `net-tools` paketindeki komutların yerini almıştır. `ip` komutu daha tutarlı bir sözdizimine sahiptir ve IPv6, policy routing, network namespaces gibi modern ağ özelliklerini daha iyi destekler.

`ip` komutu, yönetilecek nesneye (object) göre alt komutlar alır. En sık kullanılan nesneler şunlardır:
*   `link`: Ağ arayüzlerini (fiziksel veya sanal) yönetir.
*   `address` (veya `addr`): Arayüzlere atanan IP adreslerini (IPv4/IPv6) yönetir.
*   `route`: Yönlendirme tablosunu yönetir.
*   `neighbour` (veya `neigh`): ARP (IPv4) ve NDP (IPv6) komşu tablolarını yönetir.
*   `rule`: Policy routing kurallarını yönetir.
*   `tunnel`: IP tünellerini yönetir.
*   `tuntap`: TUN/TAP sanal arayüzlerini yönetir.
*   `netns`: Ağ isim alanlarını (network namespaces) yönetir.

## Temel Kullanım Örnekleri

**Arayüzleri Listeleme (`ip link`)**

Sistemdeki tüm ağ arayüzlerini ve durumlarını listeler:
```bash
ip link show
# veya kısaca:
ip link
```
Renkli çıktı için `-c` seçeneği eklenebilir: `ip -c link show`.

**IP Adreslerini Gösterme (`ip addr`)**

Tüm arayüzlere atanmış IP adreslerini (IPv4 ve IPv6) gösterir:
```bash
ip addr show
# veya kısaca:
ip addr
# veya sadece belirli bir arayüz için:
ip addr show eth0 
```

Sistem üzerindeki NIC cihazlarının IP adreslerini öğrenmek için:

```bash
ip addr show
```

Örnek Çıktı:
```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:12:34:56 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic enp0s3
       valid_lft 86300sec preferred_lft 86300sec
    inet6 fe80::a00:27ff:fe12:3456/64 scope link 
       valid_lft forever preferred_lft forever
```

**IP Adresi Ekleme/Silme (`ip addr add/del`)**

Bir arayüze geçici olarak IP adresi eklemek veya silmek için kullanılır (kalıcı ayarlar için NetworkManager, systemd-networkd veya `/etc/network/interfaces` gibi araçlar kullanılmalıdır). IP adresi CIDR notasyonu ile (örn. `/24`) belirtilmelidir.

```bash
# enp0s3 cihazına 192.168.1.150/24 IP'sini ekle
sudo ip addr add 192.168.1.150/24 dev enp0s3

# Aynı IP'yi sil
sudo ip addr del 192.168.1.150/24 dev enp0s3
```
Bir arayüzdeki tüm IP adreslerini temizlemek için `flush` kullanılabilir: `sudo ip addr flush dev enp0s3`.

**Arayüzü Açma/Kapatma (`ip link set up/down`)**

Bir ağ arayüzünü etkinleştirmek veya devre dışı bırakmak için:
```bash
sudo ip link set enp0s3 up
```

```bash
sudo ip link set enp0s3 down
```

**Yönlendirme Tablosunu Gösterme/Yönetme (`ip route`)**

Çekirdeğin IP yönlendirme tablosunu göstermek için:

```bash
ip route show
# veya kısaca:
ip route
```
Örnek Çıktı:
```bash
default via 192.168.1.1 dev enp0s3 proto dhcp metric 100 
192.168.1.0/24 dev enp0s3 proto kernel scope link src 192.168.1.100 metric 100 
```

Geçici olarak yönlendirme kuralı eklemek veya silmek için (kalıcı ayarlar için ağ yönetim aracı kullanılmalıdır):
```bash
# 10.0.0.0/8 ağına 192.168.1.254 ağ geçidi üzerinden git
sudo ip route add 10.0.0.0/8 via 192.168.1.254

# Belirli bir route'u sil
sudo ip route del 10.0.0.0/8

# Varsayılan ağ geçidini (default route) ekle/değiştir
sudo ip route add default via 192.168.1.1 dev enp0s3 
# veya (mevcutu değiştirmek için):
# sudo ip route replace default via 192.168.1.1 dev enp0s3
```

**Komşu Tablosunu Gösterme (`ip neigh`)**

ARP (IPv4) veya NDP (IPv6) tablosunu (IP-MAC adresi eşleşmeleri) göstermek için:
```bash
ip neigh show
# veya kısaca:
ip neigh
```

`ip` komutu, `iproute2` paketinin sunduğu birçok alt komut ve seçenekle ağ yönetiminin hemen her yönünü kontrol etme imkanı sunar. Daha fazla bilgi için `man ip` veya `ip <nesne> help` (örn. `ip route help`) komutlarına bakılabilir.
