# Temel Network Yapılandırması ve Kavramları

Bu bölüm, Linux sistemlerinde temel ağ yapılandırması, OSI modelinin ilgili katmanları (L2, L3) ve VLAN (Virtual Local Area Network) gibi temel ağ segmentasyonu kavramlarına giriş yapmaktadır.

**İncelenecek Konular:**

*   OSI Modeli ve TCP/IP Modeli (Kısaca)
*   Katman 2 (Data Link Layer): MAC Adresleri, Ethernet Çerçeveleri (Frames), Switch'ler, ARP.
*   Katman 3 (Network Layer): IP Adresleri (IPv4/IPv6), Alt Ağ Maskeleri (Subnet Masks), Yönlendirme (Routing) Temelleri, Router'lar.
*   Temel Ağ Yapılandırma Dosyaları (Dağıtıma göre farklılıklar - örn. `/etc/network/interfaces`, `/etc/sysconfig/network-scripts/`, NetworkManager, systemd-networkd).
*   `ip` Komutu ile Yapılandırma (Tekrar) - (Arayüz, IP, Route).
*   VLAN (Virtual Local Area Network) Nedir? Neden Kullanılır? (802.1Q Etiketleme).
*   Linux'ta VLAN Arayüzü Oluşturma ve Yapılandırma (`ip link add link <fiziksel_arayüz> name <vlan_arayüz> type vlan id <vlan_id>`).
*   Basit Ağ Sorun Giderme Adımları (Bağlantı kontrolü, IP/Gateway/DNS ayarları).
