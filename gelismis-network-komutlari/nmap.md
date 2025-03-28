# nmap (Network Mapper)

Nmap ("Network Mapper"), ağları keşfetmek ve güvenlik denetimleri yapmak için kullanılan son derece güçlü ve popüler bir açık kaynaklı araçtır. Sistem yöneticileri ve güvenlik uzmanları tarafından ağdaki cihazları bulmak, açık portları tespit etmek, çalışan servisleri ve sürümlerini belirlemek, işletim sistemlerini tahmin etmek gibi birçok amaçla kullanılır.

**UYARI:** Nmap, ağ tarama ve keşif aracıdır. Bu aracı **sadece sahip olduğunuz veya tarama izninizin olduğu ağlarda ve sistemlerde kullanmalısınız**. İzinsiz olarak başka ağları veya sistemleri taramak yasa dışı olabilir ve etik değildir.

## Kurulum

Nmap genellikle dağıtımların standart depolarında bulunur.

*   **RHEL Tabanlı (dnf):**
    ```bash
    sudo dnf install nmap -y
    ```
*   **Debian Tabanlı (apt):**
    ```bash
    sudo apt update
    sudo apt install nmap -y
    ```
*   **Arch Linux (pacman):**
    ```bash
    sudo pacman -Syu nmap
    ```

## Temel Kullanım ve Tarama Türleri

Nmap'in temel sözdizimi `nmap [Tarama Tipi...] [Seçenekler] {Hedef Belirtimi}` şeklindedir.

**1. Host Keşfi (Ping Scan)**

Bir ağdaki hangi ana bilgisayarların (host) aktif olduğunu (ping'e yanıt verdiğini) hızlıca kontrol eder, port taraması yapmaz.
```bash
# Belirtilen ağdaki (CIDR notasyonu) aktif hostları bul
nmap -sn 192.168.1.0/24 
```
*   `-sn`: Ping Scan - Port taramasını devre dışı bırakır.

**2. Port Tarama**

Hedef sistemlerde hangi TCP veya UDP portlarının açık (bir servis tarafından dinleniyor), kapalı veya filtrelenmiş (güvenlik duvarı tarafından engellenmiş) olduğunu belirler.

*   **TCP SYN Scan (Varsayılan - Stealth Scan):**
    En yaygın kullanılan tarama türüdür. Tam bir TCP bağlantısı kurmadan sadece SYN paketi gönderir. Hedef port açıksa SYN/ACK, kapalıysa RST yanıtı alınır. Genellikle root yetkisi gerektirir. Daha az log bırakma eğilimindedir.
    ```bash
    sudo nmap <hedef_ip_veya_hostname>
    ```
*   **TCP Connect Scan (`-sT`):**
    Tam bir TCP üçlü el sıkışması (SYN -> SYN/ACK -> ACK) gerçekleştirmeye çalışır. Root yetkisi gerektirmez ancak daha yavaş ve daha kolay tespit edilebilir (loglanır).
    ```bash
    nmap -sT <hedef_ip_veya_hostname>
    ```
*   **UDP Scan (`-sU`):**
    Belirtilen UDP portlarını tarar. UDP'nin doğası gereği (bağlantısız), bu tarama genellikle daha yavaş ve daha az güvenilirdir. Portun açık olup olmadığını anlamak için genellikle ICMP "Port Unreachable" yanıtının *gelmemesine* bakılır. Root yetkisi gerektirir.
    ```bash
    sudo nmap -sU <hedef_ip_veya_hostname>
    ```
*   **Belirli Portları Tarama (`-p`):**
    Varsayılan olarak Nmap en yaygın 1000 TCP portunu tarar. `-p` seçeneği ile belirli portları veya aralıkları tarayabilirsiniz.
    ```bash
    # Sadece 80 ve 443 portlarını tara
    nmap -p 80,443 <hedef>

    # 1 ile 1024 arasındaki tüm portları tara
    nmap -p 1-1024 <hedef>

    # Hem TCP 80 hem de UDP 53 portunu tara
    nmap -p T:80,U:53 <hedef> 
    ```
*   **Hızlı Tarama (`-F`):**
    Varsayılan 1000 port yerine en yaygın 100 portu tarar. Daha hızlıdır ancak bazı açık portları kaçırabilir.
    ```bash
    nmap -F <hedef>
    ```

**Tarama Sonuçları:**
*   **open:** Port aktif olarak bağlantı kabul ediyor.
*   **closed:** Port erişilebilir ancak üzerinde bir uygulama dinlemiyor.
*   **filtered:** Nmap, portun durumunu belirleyemedi (genellikle güvenlik duvarı tarafından engelleniyor).
*   **unfiltered:** Port erişilebilir ancak Nmap açık mı kapalı mı olduğunu belirleyemedi (genellikle ACK scan ile görülür).
*   **open|filtered:** Portun açık veya filtrelenmiş olabileceği durumlar (özellikle UDP taramasında).
*   **closed|filtered:** Portun kapalı veya filtrelenmiş olabileceği durumlar.

## Servis/Versiyon ve İşletim Sistemi Tespiti

*   **Servis ve Versiyon Tespiti (`-sV`):**
    Açık portlarda çalışan servislerin adını ve sürüm numarasını belirlemeye çalışır. Port taramasından sonra ek bir sorgulama yapar.
    ```bash
    sudo nmap -sV <hedef>
    ```
*   **İşletim Sistemi Tespiti (`-O`):**
    Hedef sistemin işletim sistemini tahmin etmeye çalışır. TCP/IP yığınındaki farklılıklara dayalı çeşitli testler gönderir. Root yetkisi gerektirir ve hedefin en az bir açık ve bir kapalı TCP portuna sahip olması genellikle daha iyi sonuç verir.
    ```bash
    sudo nmap -O <hedef>
    ```

## Agresif Tarama (`-A`)

Bu seçenek, birkaç popüler tarama türünü birleştirir:
*   İşletim sistemi tespiti (`-O`)
*   Versiyon tespiti (`-sV`)
*   Varsayılan betik taraması (`-sC`)
*   Traceroute (`--traceroute`)

Kullanışlıdır ancak oldukça "gürültülü" bir taramadır ve hedef sistemde kolayca fark edilebilir.
```bash
sudo nmap -A <hedef>
```

## Diğer Kullanışlı Seçenekler

*   **`-T<0-5>` (Timing Template):** Tarama hızını ve agresifliğini ayarlar.
    *   `T0` (paranoid), `T1` (sneaky): Çok yavaş, IDS'lerden kaçınmaya çalışır.
    *   `T2` (polite): Yavaş, daha az bant genişliği kullanır.
    *   `T3` (normal): Varsayılan.
    *   `T4` (aggressive): Hızlı, ağın yeterince hızlı olduğunu varsayar.
    *   `T5` (insane): Çok hızlı, ağ veya hedef yavaşsa sonuçlar güvenilir olmayabilir.
*   **`-v` / `-vv`**: Çıktı detay seviyesini artırır (verbose).
*   **`-oN <dosya>`**: Normal formatta çıktı dosyası kaydeder.
*   **`-oX <dosya>`**: XML formatında çıktı dosyası kaydeder.
*   **`-oG <dosya>`**: Grep ile kolayca işlenebilen formatta çıktı dosyası kaydeder.
*   **`-iL <liste_dosyası>`**: Hedefleri komut satırı yerine bir dosyadan okur.
*   **`--script <betik_adı>`**: Nmap Scripting Engine (NSE) kullanarak belirli betikleri çalıştırır (örn. güvenlik açığı taraması, daha fazla bilgi toplama).

Nmap, çok sayıda seçeneğe sahip kapsamlı bir araçtır. `man nmap` komutu veya Nmap'in resmi web sitesi ([https://nmap.org](https://nmap.org)) daha fazla bilgi için başvurulacak en iyi kaynaklardır. Unutmayın, bu aracı yasal ve etik sınırlar içinde kullanmak sizin sorumluluğunuzdadır.
