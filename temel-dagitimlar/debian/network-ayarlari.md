# Network Ayarları

Debian tabanlı sistemlerde ağ yapılandırması için tarihsel olarak `/etc/network/interfaces` dosyası ve `ifup`/`ifdown` komutları (ve bunları yöneten `networking` servisi) kullanılmıştır. Ancak modern Debian sürümleri, özellikle masaüstü kurulumları, **NetworkManager**'ı da yaygın olarak kullanır. Sunucu ortamlarında ise `/etc/network/interfaces` hala sıkça tercih edilmekle birlikte, **systemd-networkd** de giderek popülerleşen bir alternatiftir.

**Önemli:** Bir ağ arayüzü (örn. `eth0`) aynı anda sadece *bir* yönetim sistemi tarafından kontrol edilmelidir (ya `/etc/network/interfaces`, ya NetworkManager, ya da systemd-networkd). Farklı sistemlerle aynı arayüzü yapılandırmaya çalışmak çakışmalara ve beklenmedik davranışlara yol açabilir.

## Geleneksel Yöntem: `/etc/network/interfaces`

Bu yöntemde, tüm ağ arayüzlerinin yapılandırması `/etc/network/interfaces` dosyasında tanımlanır. Değişikliklerin uygulanması için genellikle ilgili arayüz `ifdown <arayüz>` ve `ifup <arayüz>` komutlarıyla yeniden başlatılır veya `networking` servisi (`sudo systemctl restart networking` veya eski sistemlerde `sudo service networking restart`) yeniden başlatılır.

**Örnek `/etc/network/interfaces` Dosyası:**

```
# Loopback arayüzü
auto lo
iface lo inet loopback

# eth0 arayüzü - Statik IP
auto eth0
iface eth0 inet static
    address 94.103.47.78
    netmask 255.255.255.128 # veya address 94.103.47.78/25
    gateway 94.103.47.1
    # İsteğe bağlı: DNS sunucuları (resolvconf paketi kuruluysa)
    # dns-nameservers 8.8.8.8 8.8.4.4
    # dns-search example.com

# eth1 arayüzü - DHCP
auto eth1
iface eth1 inet dhcp
```

*   `auto <arayüz>`: Sistem başlangıcında arayüzün otomatik olarak etkinleştirilmesini sağlar.
*   `iface <arayüz> inet <metod>`: Arayüz için IPv4 yapılandırmasını tanımlar.
    *   `loopback`: Geri döngü arayüzü (127.0.0.1).
    *   `static`: Statik IP adresi yapılandırması. `address`, `netmask` (veya CIDR prefix), `gateway` gibi parametreler kullanılır.
    *   `dhcp`: IP adresini DHCP sunucusundan otomatik olarak alır.
*   `address`: IP adresi.
*   `netmask`: Alt ağ maskesi. Alternatif olarak `address` satırında CIDR notasyonu (örn. `192.168.1.100/24`) kullanılabilir.
*   `gateway`: Varsayılan ağ geçidi.
*   `dns-nameservers`: Kullanılacak DNS sunucuları (boşlukla ayrılmış). Bu satırın çalışması için genellikle `resolvconf` paketinin kurulu olması gerekir. DNS sunucuları ayrıca `/etc/resolv.conf` dosyasında da tanımlanabilir.

## Modern Yöntem 1: NetworkManager

Özellikle masaüstü ve dizüstü bilgisayarlarda veya sık sık ağ değiştiren sistemlerde NetworkManager varsayılan olabilir. NetworkManager, ağları dinamik olarak yönetir ve `nmcli` (komut satırı) veya `nmtui` (metin arayüzü) gibi araçlarla yapılandırılır.

NetworkManager'ın kurulu ve aktif olup olmadığını kontrol etmek için:
```bash
sudo systemctl status NetworkManager
```

Eğer NetworkManager aktifse, `/etc/network/interfaces` dosyasındaki arayüz tanımları genellikle NetworkManager tarafından *yönetilmez* olarak işaretlenir veya dosya içeriği daha basit tutulur. NetworkManager yapılandırması için RHEL/CentOS bölümündeki [Network Ayarları (NetworkManager)](../centos/network-ayarlari.md) kısmına bakabilirsiniz. `nmcli` ve `nmtui` komutları Debian üzerinde de benzer şekilde çalışır.

## Modern Yöntem 2: systemd-networkd

`systemd-networkd`, özellikle sunucular ve gömülü sistemler için tasarlanmış, systemd ile entegre çalışan modern bir ağ yapılandırma servisidir. Yapılandırma `/etc/systemd/network/` dizini altındaki `.network` uzantılı dosyalarla yapılır.

Örnek bir statik IP için `/etc/systemd/network/10-static-eth0.network` dosyası:
```ini
[Match]
Name=eth0

[Network]
Address=192.168.1.100/24
Gateway=192.168.1.1
DNS=8.8.8.8
DNS=8.8.4.4
```
Yapılandırma sonrası servis etkinleştirilir ve başlatılır:
```bash
sudo systemctl enable systemd-networkd
sudo systemctl start systemd-networkd
# DNS için systemd-resolved servisi de gerekebilir
# sudo systemctl enable systemd-resolved
# sudo systemctl start systemd-resolved 
```

Hangi yöntemin kullanılacağı sistemin kurulumuna ve yöneticinin tercihine bağlıdır. Sunucu kurulumlarında `/etc/network/interfaces` hala yaygın olsa da, NetworkManager ve systemd-networkd daha modern ve esnek alternatifler sunmaktadır.
