# Network Ayarları (NetworkManager)

Modern RHEL tabanlı dağıtımlarda (RHEL 7+, CentOS 7+, Fedora, CentOS Stream, Rocky Linux, AlmaLinux) ağ yapılandırması varsayılan olarak **NetworkManager** servisi tarafından yönetilir. NetworkManager, ağ bağlantılarını ve cihazlarını yönetmek için dinamik ve esnek bir sistem sunar.

Yapılandırma için çeşitli araçlar mevcuttur:
*   **`nmcli`**: Kapsamlı bir komut satırı aracı. Betiklerde (scripting) ve otomasyonda kullanışlıdır.
*   **`nmtui`**: Terminal üzerinde çalışan, menü tabanlı, kullanımı kolay bir metin arayüzü (Text User Interface).
*   **Grafiksel Arayüzler:** GNOME veya KDE gibi masaüstü ortamlarının kendi ağ ayarları arayüzleri (genellikle arka planda NetworkManager kullanır).

Bu bölümde `nmcli` ve `nmtui` üzerine odaklanacağız.

## NetworkManager ile Temel İşlemler (`nmcli`)

`nmcli`, NetworkManager'ı komut satırından yönetmek için ana araçtır.

**Ağ Cihazlarının Durumunu Görme:**
Sistemdeki ağ cihazlarını ve durumlarını listeler:
```bash
nmcli device status
# veya kısaca:
nmcli dev status
```
Örnek Çıktı:
```
DEVICE  TYPE      STATE      CONNECTION 
enp0s3  ethernet  connected  Kablolu bağlantı 1 
lo      loopback  unmanaged  --                 
```

**Aktif Bağlantıları Görme:**
Mevcut aktif ağ bağlantılarını listeler:
```bash
nmcli connection show --active
# veya kısaca:
nmcli con show --active
```

**Tüm Tanımlı Bağlantıları Görme:**
Aktif olsun veya olmasın, sistemde tanımlı tüm bağlantı profillerini listeler:
```bash
nmcli connection show
# veya kısaca:
nmcli con show
```

**Belirli Bir Bağlantının Detaylarını Görme:**
Bağlantı adını veya UUID'sini kullanarak detayları gösterir:
```bash
nmcli connection show "Kablolu bağlantı 1" 
# veya UUID ile:
# nmcli con show <uuid>
```
Bu komut, IP adresi, ağ maskesi, ağ geçidi, DNS sunucuları gibi birçok detayı gösterir.

## Ağ Ayarlarını Değiştirme (`nmcli`)

**Önemli:** Ağ ayarlarını değiştirirken dikkatli olun. Uzak bir sunucuya bağlıysanız, yanlış bir ayar bağlantınızın kopmasına neden olabilir. Değişiklikler genellikle `sudo` yetkisi gerektirir.

**Statik IP Adresi Ayarlama:**
Mevcut bir bağlantıyı DHCP'den statik IP'ye geçirmek için:
```bash
# Bağlantı adını öğrenin (örneğin "Kablolu bağlantı 1")
nmcli con show

# IPv4 metodunu 'manual' yap, IP adresini/maskesini, ağ geçidini ve DNS'i ayarla
sudo nmcli con modify "Kablolu bağlantı 1" ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns "8.8.8.8,8.8.4.4"

# Bağlantıyı yeniden etkinleştirerek ayarları uygula
sudo nmcli con down "Kablolu bağlantı 1" && sudo nmcli con up "Kablolu bağlantı 1" 
```
*   `ipv4.addresses`: IP adresi ve CIDR formatında alt ağ maskesi (örn. `/24` = `255.255.255.0`).
*   `ipv4.gateway`: Varsayılan ağ geçidi.
*   `ipv4.dns`: Virgülle ayrılmış DNS sunucu adresleri.

**DHCP'ye Geri Dönme:**
```bash
sudo nmcli con modify "Kablolu bağlantı 1" ipv4.method auto ipv4.gateway "" ipv4.dns ""
sudo nmcli con down "Kablolu bağlantı 1" && sudo nmcli con up "Kablolu bağlantı 1" 
```

**Yeni Bağlantı Ekleme:**
Örneğin, `enp0s8` cihazı için statik IP ile yeni bir Ethernet bağlantısı eklemek:
```bash
sudo nmcli con add type ethernet con-name "Statik-Eth1" ifname enp0s8 ipv4.method manual ipv4.addresses 10.0.0.50/24 ipv4.gateway 10.0.0.1 ipv4.dns "10.0.0.1"
```

## Metin Arayüzü (`nmtui`)

Komut satırı seçenekleri karmaşık geliyorsa, `nmtui` daha kolay bir alternatif sunar. Terminalde `nmtui` komutunu çalıştırarak menü tabanlı arayüzü başlatabilirsiniz.

```bash
sudo nmtui
```

`nmtui` içinde ok tuşları ve Enter ile gezinebilirsiniz:
*   **Edit a connection:** Mevcut bağlantıları düzenlemek veya yeni bağlantı eklemek için kullanılır. Statik IP, DHCP, DNS gibi ayarları kolayca yapabilirsiniz.
*   **Activate a connection:** Bağlantıları etkinleştirmek veya devre dışı bırakmak için kullanılır.
*   **Set system hostname:** Sistem ana bilgisayar adını değiştirmek için kullanılır.

Değişiklik yaptıktan sonra "OK" veya "Back" seçenekleriyle menülerden çıkın ve en son ana menüden "Quit" seçeneğini seçin. `nmtui` ile yapılan değişiklikler de NetworkManager tarafından yönetilir ve kalıcı olur.

## Eski Yöntem (`ifcfg` Dosyaları)

RHEL/CentOS 6 ve öncesinde ağ yapılandırması `/etc/sysconfig/network-scripts/` dizini altındaki `ifcfg-<arayüz_adı>` (örn. `ifcfg-eth0`, `ifcfg-enp0s3`) dosyaları düzenlenerek ve `network` servisi (`service network restart` veya `systemctl restart network`) yeniden başlatılarak yapılırdı.

Örnek bir statik IP için `ifcfg-enp0s3` dosyası:
```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME=enp0s3
UUID=...
DEVICE=enp0s3
ONBOOT=yes
IPADDR=192.168.1.100
PREFIX=24  # veya NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4
```

Modern sistemlerde NetworkManager genellikle bu dosyaları okuyabilir ancak yeni yapılandırmalar için `nmcli` veya `nmtui` kullanmak daha standart ve önerilen yöntemdir. Eğer NetworkManager kurulu ve aktifse, `ifcfg` dosyalarını doğrudan düzenlemek yerine NetworkManager araçlarını kullanmak çakışmaları önler. Bazı minimal kurulumlarda veya özel durumlarda NetworkManager yerine hala `network` servisi kullanılabilir, ancak bu durum giderek azalmaktadır.
