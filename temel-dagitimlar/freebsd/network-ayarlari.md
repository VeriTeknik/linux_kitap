# Network Ayarları

FreeBSD'de ağ yapılandırması genellikle `/etc/rc.conf` dosyası üzerinden yapılır. Bu dosya, sistem başlangıcında çalıştırılacak servisleri ve ağ arayüzleri gibi temel sistem parametrelerini tanımlar. DNS sunucu adresleri ise `/etc/resolv.conf` dosyasında belirtilir.

## `/etc/rc.conf` ile Yapılandırma

`/etc/rc.conf` dosyasına eklenen veya değiştirilen satırlar, sistem başlangıcında veya `netif` servisi yeniden başlatıldığında uygulanır.

**Ağ Arayüzlerini Listeleme:**
Sistemdeki ağ arayüzlerini görmek için `ifconfig` komutu kullanılır:
```bash
ifconfig -a
```

**DHCP ile Otomatik Yapılandırma:**
Bir ağ arayüzünün (örneğin `em0`) DHCP kullanarak otomatik olarak yapılandırılması için `/etc/rc.conf` dosyasına şu satır eklenir:
```bash
ifconfig_em0="DHCP" 
```
Birden fazla arayüz varsa, her biri için ayrı bir satır eklenir.

**Statik IP Adresi ile Yapılandırma:**
`em0` arayüzüne statik bir IP adresi, alt ağ maskesi ve varsayılan ağ geçidi atamak için `/etc/rc.conf` dosyasına aşağıdaki gibi satırlar eklenir:
```bash
# em0 arayüzü için statik IP ayarları
ifconfig_em0="inet 192.168.1.100 netmask 255.255.255.0" 
# Varsayılan ağ geçidi
defaultrouter="192.168.1.1" 
```
*   `ifconfig_<arayüz_adı>`: Belirtilen arayüz için yapılandırma satırını başlatır.
*   `inet <ip_adresi>`: IPv4 adresini belirtir.
*   `netmask <alt_ağ_maskesi>`: Alt ağ maskesini belirtir. Alternatif olarak CIDR notasyonu da kullanılabilir: `inet 192.168.1.100/24`.
*   `defaultrouter`: Sistemin varsayılan ağ geçidini (router) tanımlar.

**Birden Fazla IP Adresi (Alias):**
Bir arayüze birden fazla IP adresi atamak için `alias` kullanılır:
```bash
ifconfig_em0="inet 192.168.1.100 netmask 255.255.255.0"
ifconfig_em0_alias0="inet 192.168.1.101 netmask 255.255.255.0"
ifconfig_em0_alias1="inet 10.0.0.5 netmask 255.0.0.0"
defaultrouter="192.168.1.1"
```

**IPv6 Yapılandırması:**
Statik IPv6 adresi ve SLAAC (StateLess Address AutoConfiguration) için:
```bash
# IPv6'yı etkinleştir
ipv6_enable="YES" 
# Statik IPv6 adresi (em0 için)
ifconfig_em0_ipv6="inet6 2001:db8:cafe::100 prefixlen 64" 
# SLAAC'ı etkinleştir (em0 için)
ifconfig_em0_ipv6="inet6 accept_rtadv" 
# Varsayılan IPv6 ağ geçidi
ipv6_defaultrouter="2001:db8:cafe::1" 
```

## DNS Ayarları (`/etc/resolv.conf`)

DNS sunucu adresleri `/etc/resolv.conf` dosyasında tanımlanır. Bu dosya genellikle DHCP istemcisi tarafından otomatik olarak güncellenir, ancak statik yapılandırmada manuel olarak düzenlenmesi gerekir.
```
search example.com # İsteğe bağlı: Alan adı arama listesi
nameserver 8.8.8.8   # Birincil DNS sunucusu
nameserver 8.8.4.4   # İkincil DNS sunucusu
```

## Değişiklikleri Uygulama

`/etc/rc.conf` dosyasında yapılan değişikliklerin geçerli olması için genellikle `netif` servisini yeniden başlatmak gerekir:
```bash
sudo service netif restart
```
Veya sadece belirli bir arayüzü yeniden başlatmak için:
```bash
sudo service netif restart em0
```
Sistemi yeniden başlatmak da (`sudo shutdown -r now`) ayarların uygulanmasını sağlar.

`/etc/resolv.conf` dosyasında yapılan değişiklikler genellikle anında geçerli olur.

## Geçici Ayarlar (`ifconfig`)

`ifconfig` komutu, çalışan sistem üzerinde ağ arayüzlerini geçici olarak yapılandırmak için de kullanılabilir. Bu ayarlar sistem yeniden başlatıldığında kaybolur.
```bash
# em0'a geçici IP ve maske ata
sudo ifconfig em0 inet 192.168.1.150 netmask 255.255.255.0

# Geçici varsayılan ağ geçidi ekle (route komutu ile)
sudo route add default 192.168.1.1

# Arayüzü kapat
sudo ifconfig em0 down

# Arayüzü aç
sudo ifconfig em0 up 
```

FreeBSD'nin ağ yapılandırması `/etc/rc.conf` merkezli yapısıyla oldukça basit ve güçlüdür. Detaylı bilgi ve diğer seçenekler için `rc.conf(5)` ve `ifconfig(8)` man sayfalarına bakılabilir.
