# Wireshark (Giriş)

Wireshark, dünyanın en popüler ve yaygın olarak kullanılan ağ protokolü çözümleyicisidir (network protocol analyzer). Açık kaynaklıdır ve grafiksel bir kullanıcı arayüzü (GUI) sunar. Ağ trafiğini canlı olarak yakalamak veya daha önce yakalanmış (`tcpdump` gibi araçlarla oluşturulmuş `.pcap` dosyaları dahil) trafik dosyalarını açıp detaylı bir şekilde incelemek için kullanılır.

## Wireshark vs `tcpdump`

*   **`tcpdump`**: Komut satırı tabanlıdır. Paket yakalama ve temel filtreleme için güçlüdür. Özellikle sunucularda, uzak sistemlerde veya betiklerde kullanışlıdır. Çıktısı metin tabanlıdır veya `.pcap` dosyasına kaydedilebilir.
*   **Wireshark**: Grafiksel arayüze sahiptir. Paketleri interaktif olarak incelemek, protokolleri katman katman ayrıştırmak, filtrelemek, istatistik çıkarmak ve grafikler oluşturmak için çok daha kullanıcı dostudur. Genellikle masaüstü sistemlerde kullanılır. `tcpdump` tarafından oluşturulan dosyaları okuyabilir.

Her iki araç da paket yakalama için genellikle aynı `libpcap` (veya Windows'ta `Npcap`) kütüphanesini kullanır.

## Kurulum

Wireshark çoğu Linux dağıtımının depolarında bulunur.

*   **RHEL Tabanlı (dnf):**
    ```bash
    sudo dnf install wireshark-qt # veya wireshark-gtk
    ```
*   **Debian Tabanlı (apt):**
    ```bash
    sudo apt update
    sudo apt install wireshark -y
    ```
    Kurulum sırasında genellikle, normal kullanıcıların da paket yakalayıp yakalayamayacağı sorulur. Güvenlik nedeniyle, genellikle sadece `wireshark` grubuna üye olan kullanıcıların yakalama yapmasına izin verilir. Kullanıcınızı bu gruba eklemeniz gerekebilir:
    ```bash
    sudo usermod -aG wireshark $USER 
    # Değişikliğin etkili olması için oturumu kapatıp açmanız gerekebilir.
    ```
*   **Arch Linux (pacman):**
    ```bash
    sudo pacman -Syu wireshark-qt # veya wireshark-gtk
    ```

## Temel Kullanım

1.  **Başlatma:** Wireshark'ı grafiksel menüden veya terminalden `wireshark` komutuyla başlatın. Paket yakalamak için genellikle yönetici (root) veya özel grup (örn. `wireshark`) yetkisi gerekir.
2.  **Arayüz Seçme:** Başlangıç ekranında, paket yakalamak istediğiniz ağ arayüzünü (örn. `eth0`, `wlan0`) çift tıklayarak veya seçip başlat düğmesine basarak seçin.
3.  **Paket Yakalama:** Wireshark seçilen arayüzdeki trafiği yakalamaya başlar ve paketleri gerçek zamanlı olarak listeler.
    *   **Durdurma/Başlatma:** Araç çubuğundaki kırmızı kare (Durdur) ve köpekbalığı yüzgeci (Başlat) ikonları ile yakalama işlemi kontrol edilir.
4.  **Arayüz Panelleri:**
    *   **Paket Listesi (Packet List):** Yakalanan paketlerin özet bilgilerini (zaman, kaynak/hedef IP, protokol, bilgi) gösteren ana panel.
    *   **Paket Detayları (Packet Details):** Seçilen paketin protokol katmanlarını (Ethernet, IP, TCP/UDP, Uygulama Katmanı vb.) hiyerarşik olarak gösterir. Her katman genişletilerek o katmana ait başlık bilgileri incelenebilir.
    *   **Paket Baytları (Packet Bytes):** Seçilen paketin ham verisini hexadecimal ve ASCII formatında gösterir.
5.  **Ekran Filtreleri (Display Filters):** Wireshark'ın en güçlü özelliklerinden biridir. Yakalanmış olan paketler arasından sadece belirli kriterlere uyanları göstermek için kullanılır. **Bunlar yakalama filtreleri (capture filters) değildir; tüm trafik yakalanır, sadece gösterimi filtrelenir.** Filtreleme çubuğuna (genellikle üstte bulunur) filtre ifadeleri yazılır.
    *   Örnekler:
        *   `ip.addr == 192.168.1.10`: Kaynak veya hedef IP adresi 192.168.1.10 olan paketler.
        *   `tcp.port == 80`: Kaynak veya hedef TCP portu 80 olan paketler.
        *   `http`: HTTP protokolünü içeren paketler.
        *   `dns`: DNS protokolünü içeren paketler.
        *   `tcp.flags.syn == 1 and tcp.flags.ack == 1`: TCP SYN/ACK paketleri.
        *   `!(arp or icmp or dns)`: ARP, ICMP ve DNS dışındaki tüm paketler.
    Filtreleme sözdizimi oldukça geniştir ve protokol alanlarına göre detaylı filtreleme yapılabilir.
6.  **Kaydetme/Açma:** Yakalanan trafiği `.pcapng` (varsayılan) veya `.pcap` formatında kaydetmek (`File -> Save As...`) veya daha önce kaydedilmiş bir dosyayı açmak (`File -> Open...`) mümkündür.

Wireshark, ağ protokollerini öğrenmek, ağ sorunlarını teşhis etmek ve güvenlik analizi yapmak için son derece değerli bir araçtır. Bu sadece çok temel bir giriştir; Wireshark'ın yetenekleri çok daha geniştir (TCP akışlarını takip etme, istatistikler, grafikler, VoIP analizi vb.). Detaylı bilgi için resmi Wireshark belgelerine ([https://www.wireshark.org/docs/](https://www.wireshark.org/docs/)) başvurulabilir.
