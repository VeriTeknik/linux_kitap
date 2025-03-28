# Network Ayarları

Arch Linux, ağ yapılandırması için kullanıcısına çeşitli seçenekler sunar. Kurulum sırasında veya sonrasında ihtiyaca göre farklı ağ yönetim araçları kurulup yapılandırılabilir. Yaygın kullanılan yöntemler şunlardır:

1.  **systemd-networkd:** systemd ile entegre, genellikle sunucular ve basit yapılandırmalar için tercih edilen modern bir ağ yöneticisi.
2.  **NetworkManager:** Hem masaüstü hem de sunucu ortamlarında popüler, tam özellikli bir ağ yöneticisi. Özellikle Wi-Fi ve mobil bağlantılar için kullanışlıdır.
3.  **dhcpcd:** Basit bir DHCP istemcisi. Statik IP yapılandırması için de kullanılabilir ancak genellikle diğer araçlar kadar esnek değildir.
4.  **netctl:** Arch Linux'a özgü, profil tabanlı bir ağ yönetim aracı (artık daha az yaygın).
5.  **Manuel Yapılandırma:** `ip` komutları ve diğer düşük seviyeli araçlarla manuel yapılandırma (genellikle önerilmez).

**Önemli:** Bir ağ arayüzü (örn. `eth0`, `enp0s3`, `wlan0`) aynı anda sadece *bir* yönetim aracı tarafından kontrol edilmelidir. Örneğin, hem NetworkManager hem de systemd-networkd'yi aynı anda aynı arayüz için etkinleştirmek sorunlara yol açar.

## Metod 1: systemd-networkd

`systemd-networkd`, systemd'nin bir parçasıdır ve yapılandırması `/etc/systemd/network/` dizini altındaki `.network` uzantılı dosyalarla yapılır. DNS yönetimi için genellikle `systemd-resolved` servisi ile birlikte kullanılır.

**Yapılandırma Dosyaları:**

*   **DHCP (Örnek: `/etc/systemd/network/20-wired.network`):**
    ```ini
    [Match]
    Name=en* # 'en' ile başlayan tüm kablolu arayüzlerle eşleşir

    [Network]
    DHCP=ipv4 
    ```
*   **Statik IP (Örnek: `/etc/systemd/network/20-wired-static.network`):**
    ```ini
    [Match]
    Name=enp0s3 # Belirli bir arayüzle eşleşir

    [Network]
    Address=192.168.1.100/24
    Gateway=192.168.1.1
    DNS=8.8.8.8
    DNS=8.8.4.4
    ```

**Servisleri Etkinleştirme ve Başlatma:**
```bash
# systemd-networkd'yi etkinleştir ve başlat
sudo systemctl enable systemd-networkd.service
sudo systemctl start systemd-networkd.service

# systemd-resolved'u etkinleştir ve başlat (DNS için)
sudo systemctl enable systemd-resolved.service
sudo systemctl start systemd-resolved.service

# /etc/resolv.conf'un systemd-resolved tarafından yönetilmesi için link oluştur
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf 
```

## Metod 2: NetworkManager

NetworkManager, özellikle dinamik ortamlar ve kablosuz ağlar için popüler bir seçenektir. `nmtui` (metin arayüzü) ve `nmcli` (komut satırı) araçlarını sunar.

**Kurulum (Gerekirse):**
```bash
sudo pacman -S networkmanager
```

**Servisi Etkinleştirme ve Başlatma:**
```bash
sudo systemctl enable NetworkManager.service
sudo systemctl start NetworkManager.service
```
**NOT:** Eğer `dhcpcd` veya `systemd-networkd` gibi başka bir ağ servisi çalışıyorsa, NetworkManager'ı etkinleştirmeden önce onu durdurup devre dışı bırakmanız gerekir (`sudo systemctl stop <servis>`, `sudo systemctl disable <servis>`).

**Yapılandırma (`nmtui`):**
Kullanımı en kolay yöntemlerden biridir. Terminalde çalıştırın:
```bash
sudo nmtui
```
Açılan menüden "Edit a connection" ile bağlantıları düzenleyebilir (statik IP, DHCP, DNS vb.), "Activate a connection" ile etkinleştirebilirsiniz.

**Yapılandırma (`nmcli`):**
Komut satırı üzerinden daha detaylı kontrol sağlar. Örnekler için RHEL/CentOS bölümündeki [Network Ayarları (NetworkManager)](../centos/network-ayarlari.md) kısmına bakabilirsiniz. Komutlar Arch Linux üzerinde de geçerlidir.

## Metod 3: dhcpcd

Basit bir DHCP istemcisidir. Statik IP atamak için de kullanılabilir ancak genellikle NetworkManager veya systemd-networkd daha esnektir.

**Kurulum (Gerekirse):**
`dhcpcd` genellikle temel kurulumda gelir.

**Servisi Etkinleştirme (Belirli Arayüz İçin):**
```bash
# Örnek: enp0s3 arayüzü için DHCP'yi etkinleştir ve başlat
sudo systemctl enable dhcpcd@enp0s3.service
sudo systemctl start dhcpcd@enp0s3.service 
```

**Statik IP Yapılandırması (`/etc/dhcpcd.conf`):**
`/etc/dhcpcd.conf` dosyasının sonuna aşağıdaki gibi satırlar eklenerek statik IP ayarlanabilir:
```
interface enp0s3
static ip_address=192.168.1.100/24
static routers=192.168.1.1
static domain_name_servers=8.8.8.8 8.8.4.4
```
Değişiklik sonrası `dhcpcd` servisini yeniden başlatmak gerekir:
```bash
sudo systemctl restart dhcpcd@enp0s3.service 
# veya tüm dhcpcd servisleri için:
# sudo systemctl restart dhcpcd.service
```

Arch Linux'ta ağ yönetimi için hangi yöntemin seçileceği kullanıcının tercihine ve ihtiyacına bağlıdır. Genellikle NetworkManager veya systemd-networkd modern ve esnek çözümler sunar.
