# `route` (Eski Yönlendirme Tablosu Aracı)

`route` komutu, `ifconfig` gibi eski `net-tools` paketinin bir parçasıdır ve Linux çekirdeğinin IP yönlendirme (routing) tablosunu görüntülemek ve yönetmek için kullanılırdı. Bu komut da artık **geliştirilmemektedir** ve modern sistemlerde yerini `iproute2` paketindeki **`ip route`** komutuna bırakmıştır.

`ip route` komutu daha esnek, daha güçlüdür ve modern ağ kavramlarını (policy routing, multiple routing tables vb.) daha iyi destekler. Bu bölümde `route` komutu tarihsel bilgi amacıyla anlatılacaktır. **Yeni yapılandırmalar ve betikler için `ip route` kullanmanız şiddetle tavsiye edilir.**

### Mevcut Route Tablosunu Görmek

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

`-n` parametresiyle adreslerin nümerik değerleri gösterilir.

```bash
eaydin@dixon ~ $ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.99.1    0.0.0.0         UG    0      0        0 wlan0
172.16.77.0     0.0.0.0         255.255.255.0   U     0      0        0 vmnet1
172.16.148.0    0.0.0.0         255.255.255.0   U     0      0        0 vmnet8
192.168.99.0    0.0.0.0         255.255.255.0   U     9      0        0 wlan0
```

### Route Tablosuna Müdahale Etmek (Geçici Ayarlar)

`route` komutu ile yapılan değişiklikler, tıpkı `ifconfig` gibi, geçicidir ve sistem yeniden başlatıldığında kaybolur. Kalıcı ayarlar için NetworkManager, systemd-networkd veya `/etc/network/interfaces` gibi araçlar kullanılmalıdır.

#### Default Gateway Eklemek

Belirli bir ağ aralığında (network range) bulunmayan paketler default gateway'e yönlenir. Örneğin yukarıdaki routing tablosunda, 172.16.77.0/24, 172.16.148.0/24 ve 192.168.99.0/24 dışındaki adreslere yönlendirilecek bütün paketler, 192.168.99.1 adresine gönderilir. Burası bulunduğumuz ağdaki router'dır, kendisi de gelen paketleri Internet Servis Sağlayıcıya iletir. Default gateway'in kendisi olduğunu, **Flags** kolonundaki **G** ifadesi belirtmektedir.

```bash
sudo route add default gw 192.168.99.5
```
*   **Modern Karşılığı:** `sudo ip route add default via 192.168.99.5`

#### Default Gateway Silmek

Varsayılan ağ geçidini silmek için:
```bash
sudo route del default
```
*   **Modern Karşılığı:** `sudo ip route del default`

**NOT:** Bazı durumlarda sistemde birden fazla default gateway tanımlanabilir. Teorik olarak gerekmiyor olsa da pratikte nadiren rastlanılan bir durumdur. Bu özel durumlar kapsamımız dışında olduğundan incelemiyoruz. Sadece böyle senaryoların karşılaşılabildiğini belirtme ihtiyacı hissettik.

#### Belirli Bir Ağa Route Eklemek

Belirli bir ağa (örn. 192.168.59.0/25) giden trafiği belirli bir arayüz (örn. eth0) veya ağ geçidi (gateway) üzerinden yönlendirmek için:

*   **Arayüz Belirterek:**
    ```bash
    sudo route add -net 192.168.59.0 netmask 255.255.255.0 dev eth0 
    ```
    *   **Modern Karşılığı:** `sudo ip route add 192.168.59.0/24 dev eth0`

*   **Ağ Geçidi Belirterek:**
    ```bash
    sudo route add -net 192.168.55.0 netmask 255.255.255.0 gw 192.168.55.1
    ```
    *   **Modern Karşılığı:** `sudo ip route add 192.168.55.0/24 via 192.168.55.1`

Bu satırı yazdıktan sonra önceki route tablomuz şöyle oldu:

```bash
eaydin@dixon ~ $ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.99.1    0.0.0.0         UG    0      0        0 wlan0
172.16.77.0     0.0.0.0         255.255.255.0   U     0      0        0 vmnet1
172.16.148.0    0.0.0.0         255.255.255.0   U     0      0        0 vmnet8
192.168.59.128  0.0.0.0         255.255.255.128 U     0      0        0 eth0
192.168.99.0    0.0.0.0         255.255.255.0   U     9      0        0 wlan0
```

#### Route Silmek

Eklenmiş bir route'u silmek için `add` yerine `del` kullanılır:
```bash
sudo route del -net 192.168.59.0 netmask 255.255.255.0 dev eth0
```
*   **Modern Karşılığı:** `sudo ip route del 192.168.59.0/24 dev eth0`

```bash
sudo route del -net 192.168.55.0 netmask 255.255.255.0 gw 192.168.55.1
```
*   **Modern Karşılığı:** `sudo ip route del 192.168.55.0/24 via 192.168.55.1`

#### Bir IP veya Ağı Engellemek (`reject`)

Belirli bir hedefe giden paketleri yönlendirmek yerine doğrudan engellemek (ICMP "unreachable" mesajı ile) için `reject` kullanılır:
```bash
sudo route add -host 192.168.59.5 reject
```
*   **Modern Karşılığı:** `sudo ip route add prohibit 192.168.59.5` (veya `blackhole` - sessizce yok sayar)

```bash
eaydin@dixon ~ $ ping 192.168.59.5
connect: Network is unreachable
eaydin@dixon ~ $ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.99.1    0.0.0.0         UG    0      0        0 wlan0
172.16.77.0     0.0.0.0         255.255.255.0   U     0      0        0 vmnet1
172.16.148.0    0.0.0.0         255.255.255.0   U     0      0        0 vmnet8
192.168.59.5    -               255.255.255.255 !H    0      -        0 -
192.168.99.0    0.0.0.0         255.255.255.0   U     9      0        0 wlan0
```

Engellemeyi kaldırmak için:
```bash
sudo route del -host 192.168.59.5 reject
```
*   **Modern Karşılığı:** `sudo ip route del prohibit 192.168.59.5`

Bir ağı engellemek:
```bash
sudo route add -net 192.168.59.0 netmask 255.255.255.0 reject
```
*   **Modern Karşılığı:** `sudo ip route add prohibit 192.168.59.0/24`

```bash
eaydin@dixon ~ $ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.99.1    0.0.0.0         UG    0      0        0 wlan0
172.16.77.0     0.0.0.0         255.255.255.0   U     0      0        0 vmnet1
172.16.148.0    0.0.0.0         255.255.255.0   U     0      0        0 vmnet8
192.168.59.0    -               255.255.255.0   !     0      -        0 -
192.168.99.0    0.0.0.0         255.255.255.0   U     9      0        0 wlan0
```

Engellemeyi kaldırmak için:
```bash
sudo route del -net 192.168.59.0 netmask 255.255.255.0 reject
```
*   **Modern Karşılığı:** `sudo ip route del prohibit 192.168.59.0/24`

## Örnek Route Senaryosu (Modern `ip route` ile)

Aşağıdaki örneği dikkate alalım. **VT** isminde bir Linux sunucumuz (router) olsun. Üzerinde 3 ağ arayüzü var:
*   `eth0`: İnternet bağlantısı (IP: `94.103.32.80/28`, Gateway: `94.103.32.81`)
*   `eth1`: Teknik Ekip Ağı (IP: `192.168.59.1/24`)
*   `eth2`: Muhasebe Ekibi Ağı (IP: `10.0.42.1/24`)

Amacımız, hem teknik hem de muhasebe ekibinin birbirleriyle ve internetle konuşabilmesini sağlamak. Bunun için VT sunucusunda IP yönlendirmeyi (IP forwarding) etkinleştirmek ve istemcilerde doğru ağ geçidini ayarlamak gerekir.

**1. VT Sunucusunda IP Yönlendirmeyi Etkinleştirme:**
```bash
# Geçici olarak etkinleştirme
sudo sysctl -w net.ipv4.ip_forward=1

# Kalıcı hale getirmek için /etc/sysctl.conf veya /etc/sysctl.d/ içinde:
# net.ipv4.ip_forward = 1
# Ardından: sudo sysctl -p
```

**2. Uçbirimlerin Ağ Geçidi Ayarları:**
*   Teknik Ekip (`192.168.59.0/24`): Varsayılan ağ geçidi olarak VT sunucusunun bu ağdaki IP'sini (`192.168.59.1`) kullanmalıdırlar.
*   Muhasebe Ekibi (`10.0.42.0/24`): Varsayılan ağ geçidi olarak VT sunucusunun bu ağdaki IP'sini (`10.0.42.1`) kullanmalıdırlar.

**3. VT Sunucusunda Yönlendirme Tablosu:**
VT sunucusunun yönlendirme tablosu genellikle şöyle görünmelidir (`ip route` komutu ile):
```bash
# İnternete çıkış için varsayılan yol
default via 94.103.32.81 dev eth0 
# Muhasebe ağına doğrudan bağlı
10.0.42.0/24 dev eth2 proto kernel scope link src 10.0.42.1 
# İnternet bağlantısının olduğu ağ
94.103.32.70/28 dev eth0 proto kernel scope link src 94.103.32.80 
# Teknik ekip ağına doğrudan bağlı
192.168.59.0/24 dev eth1 proto kernel scope link src 192.168.59.1 
```
*   Doğrudan bağlı ağlar (`proto kernel scope link`) genellikle otomatik olarak eklenir.
*   Varsayılan ağ geçidi (`default via ...`) genellikle DHCP ile alınır veya manuel olarak eklenir:
    ```bash
    sudo ip route add default via 94.103.32.81 dev eth0
    ```

Bu yapılandırma ile:
*   Teknik ekipteki bir bilgisayar (`192.168.59.X`), muhasebedeki bir bilgisayara (`10.0.42.Y`) paket gönderdiğinde, paket önce VT'nin `192.168.59.1` adresine gider. VT, hedef IP'nin `10.0.42.0/24` ağına ait olduğunu tablosundan bilir ve paketi `eth2` arayüzünden dışarı yönlendirir.
*   Teknik ekipteki bir bilgisayar internete (örn. `8.8.8.8`) paket gönderdiğinde, paket VT'nin `192.168.59.1` adresine gider. VT, hedefin kendi bildiği ağlarda olmadığını görür ve paketi varsayılan ağ geçidi (`94.103.32.81`) üzerinden `eth0` arayüzünden dışarı yönlendirir. (NAT yapılandırması da gerekebilir).

`route` komutu eski sistemlerde yönlendirme tablosunu yönetmek için kullanılırdı, ancak `ip route` komutu modern Linux sistemlerinde daha yetenekli ve standart bir alternatiftir.
