# `ifconfig` (Eski Ağ Yapılandırma Aracı)

`ifconfig` komutu, geleneksel Unix ve eski Linux sistemlerinde ağ arayüzlerini (NIC - Network Interface Controller) yapılandırmak ve bilgilerini görüntülemek için kullanılan temel araçtı. Ancak, `ifconfig`'in dahil olduğu `net-tools` paketi artık **geliştirilmemektedir** ve modern Linux dağıtımlarında yerini büyük ölçüde `iproute2` paketindeki **`ip` komutuna** bırakmıştır.

**Neden `ip` Kullanılmalı?**
*   `ip` komutu daha yetenekli ve esnektir (IPv6, policy routing, tüneller vb. için daha iyi destek).
*   Daha tutarlı bir sözdizimine sahiptir (`ip link ...`, `ip addr ...`, `ip route ...`).
*   Aktif olarak geliştirilmektedir.
*   `ifconfig` bazı modern dağıtımlarda varsayılan olarak kurulu gelmeyebilir.

Bu bölümde `ifconfig` komutunun temel kullanımı, özellikle eski sistemlerle karşılaşıldığında veya tarihsel bilgi amacıyla anlatılacaktır. **Yeni yapılandırmalar ve betikler için `ip` komutunu kullanmanız şiddetle tavsiye edilir.**

## Mevcut Cihazları Listelemek

`ifconfig` programı parametresiz çağırıldığında mevcut ağ cihazlarını \(NIC, Network Interface Controller\) listeler.

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

Yukarıdaki çıktıda 3 cihaz görülüyor.

`eth0`: Ethernet kartı cihazı. Açık konumda \(UP\) ancak bir IP adresi yok. **HWaddr** ile gösterilen kısım MAC adresi.

`lo`: Lookback interface. Yerel ağ'ın oluşması, 127.0.0.1 IP adresinin bir yere işaret edebilmesi için oluşan sanal cihaz.

`wlan0`: Başka bir ethernet kartı. Adından anlaşılacağı üzere kablosuz \(wireless\) cihaz. Hem IPv4 hem de IPv6 adresleri görülüyor. Ayrıca **RX** ve **TX** ile giden, gelen paket sayısı ve miktarı gösteriliyor. Cihaz açıldığından beri 531.3MB indirme \(download, receive\) yapmış, 38.8MB gönderme \(upload, transmisson\) yapmış.

Tek cihazı görüntülemek için parametre olarak ismi verilebilir.

```bash
eaydin@dixon ~ $ ifconfig eth0
eth0      Link encap:Ethernet  HWaddr a0:d3:c1:5e:68:ec  
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

Bazı durumlarda cihazların tamamı listelenmeyebilir. Özellikle DOWN durumda olan cihazları da görüntülemek için `-a` parametresi kullanılmalıdır.

`ifconfig -a`

### `iwconfig` (Eski Kablosuz Yapılandırma Aracı)

`ifconfig`'in kablosuz ağlara özel versiyonu gibidir. Kablosuz arayüzlerin ESSID, mod, frekans gibi özel ayarlarını görüntülemek ve (geçici olarak) değiştirmek için kullanılır.

```bash
eth0      no wireless extensions.

wlan0     IEEE 802.11bgn  ESSID:"aydin"  
          Mode:Managed  Frequency:2.427 GHz  Access Point: 18:28:61:D3:B2:ED   
          Bit Rate=58.5 Mb/s   Tx-Power=20 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:off
          Link Quality=46/70  Signal level=-64 dBm  
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:1716  Invalid misc:1703   Missed beacon:0

lo        no wireless extensions.
```

iwconfig komutu, bu bilgileri `/proc/net/wireless` dosyasını okuyarak derler.

```bash
eaydin@dixon ~ $ cat /proc/net/wireless
Inter-| sta-|   Quality        |   Discarded packets               | Missed | WE
 face | tus | link level noise |  nwid  crypt   frag  retry   misc | beacon | 22
 wlan0: 0000   44.  -66.  -256        0      0      0   1716   1712        0
```

```bash
iwconfig wlan0
```
`iwconfig` de `net-tools` paketinin bir parçasıdır ve modern sistemlerde yerini `iw` komutuna bırakmıştır. Kablosuz ağ yönetimi için genellikle NetworkManager gibi üst düzey araçlar kullanılır.

## Temel Komutlar

Aşağıda ifconfig ile temel cihaz ayarlamalarının nasıl yapıldığını göreceğiz.

**ÖNEMLİ NOT**: Bu ayarlar sistemi doğrudan etkiler, reboot gerektirmez. Dolayısıyla cihazın IP adresini veya benzer bilgileri değiştirirseniz internet erişimini etkileyebilirsiniz. Uzaktan bağlı olduğunuz cihazlarda bu komutları kullanırken dikkatli olmanızı tavsiye ederiz. Bu ayarlar sistem reboot olduğunda kaybolur, yani `/etc/network/interfaces` veya `/etc/sysconfig/network-scripts/ifcfg-eth0` gibi dosyaları düzenlediğinizde olduğu gibi kalıcı değildir.

### Cihaz Açıp Kapatmak (`up`/`down`)

Bir arayüzü etkinleştirmek veya devre dışı bırakmak için:
```bash
# Etkinleştirme
sudo ifconfig eth0 up 
# Modern Karşılığı: sudo ip link set eth0 up

# Devre Dışı Bırakma
sudo ifconfig eth0 down
# Modern Karşılığı: sudo ip link set eth0 down
```
`ifup` ve `ifdown` komutları ise genellikle `/etc/network/interfaces` (Debian) gibi yapılandırma dosyalarını okuyarak arayüzleri yöneten daha üst seviye betiklerdir.

### IP Adresi, Netmask, Broadcast Atamak

Bir arayüze IP adresi, alt ağ maskesi ve yayın (broadcast) adresi atamak için:
```bash
sudo ifconfig eth0 192.168.42.5 netmask 255.255.255.0 broadcast 192.168.42.255
```
*   **Modern Karşılığı:** `sudo ip addr add 192.168.42.5/24 broadcast 192.168.42.255 dev eth0`
    (Broadcast adresi genellikle otomatik hesaplanır ve belirtilmesi gerekmez).
*   `ifconfig` CIDR notasyonunu (`/24`) **desteklemez**.

### MTU Ayarlamak

Arayüzün Maksimum İletim Birimi'ni (MTU) ayarlamak için:
```bash
sudo ifconfig eth0 mtu 1492
```
*   **Modern Karşılığı:** `sudo ip link set eth0 mtu 1492`

### Promiscuous Mod

Arayüzü, sadece kendine ait olmayan paketleri de alacak şekilde karışık (promiscuous) moda almak için (paket analizi vb. için):
```bash
# Etkinleştirme
sudo ifconfig eth0 promisc
# Modern Karşılığı: sudo ip link set eth0 promisc on

# Devre Dışı Bırakma
sudo ifconfig eth0 -promisc
# Modern Karşılığı: sudo ip link set eth0 promisc off
```

### MAC Adresini Değiştirmek

Arayüzün donanım (MAC) adresini değiştirmek için (dikkatli kullanılmalıdır):
```bash
sudo ifconfig eth0 hw ether AA:BB:CC:DD:EE:FF
```
*   **Modern Karşılığı:** `sudo ip link set dev eth0 address AA:BB:CC:DD:EE:FF`

Özetle, `ifconfig` eski sistemlerde temel ağ bilgilerini görmek veya geçici ayarlar yapmak için hala işe yarayabilir, ancak modern Linux yönetimi için `ip` komutunu öğrenmek ve kullanmak daha doğrudur.
