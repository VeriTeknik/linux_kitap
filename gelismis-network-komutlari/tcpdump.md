# tcpdump

`tcpdump`, ağ arayüzleri üzerinden geçen paketleri yakalamak, görüntülemek ve analiz etmek için kullanılan güçlü bir komut satırı paket çözümleyicisidir (packet sniffer/analyzer). Ağ sorunlarını giderme, protokol davranışlarını anlama ve güvenlik analizi gibi birçok amaçla kullanılır.

`tcpdump`, yakaladığı paketleri genellikle libpcap kütüphanesi formatında işler ve bu format, Wireshark gibi diğer birçok ağ analiz aracı tarafından da okunabilir.

**Yetki:** Ağ arayüzlerinden ham paketleri yakalamak genellikle root ayrıcalıkları gerektirir, bu nedenle `tcpdump` komutları sıklıkla `sudo` ile çalıştırılır.

## Temel Kullanım

*   **Arayüzleri Listeleme:** Sistemdeki kullanılabilir ağ arayüzlerini listelemek için:
    ```bash
    sudo tcpdump -D
    ```
*   **Belirli Bir Arayüzü Dinleme:** Belirtilen arayüzdeki tüm trafiği yakalar ve özetini ekrana basar. `Ctrl+C` ile durdurulur.
    ```bash
    sudo tcpdump -i eth0 
    ```
    *   `-i <arayüz>`: Dinlenecek ağ arayüzünü belirtir (örn. `eth0`, `wlan0`). `any` değeri tüm arayüzleri dinler (promiscuous mod destekleniyorsa).

*   **Temel Çıktı Formatı:**
    Varsayılan çıktı genellikle şöyledir:
    `[ZamanDamgası] [Protokol] ([Kaynak IP].[Kaynak Port] > [Hedef IP].[Hedef Port]): [Bayraklar], [Seq/Ack Numaraları], [Diğer Bilgiler], [Veri Uzunluğu]`
    ```
    14:55:01.123456 IP 192.168.1.10.54321 > 8.8.8.8.53: Flags [S], seq 1000, win 64240, options [mss 1460,sackOK,TS val 100 ecr 0,nop,wscale 7], length 0
    ```

## Yaygın Seçenekler

*   **`-n`**: IP adresleri için ana bilgisayar adı çözümlemesi yapma. Çıktıyı daha hızlı ve okunabilir hale getirebilir.
*   **`-nn`**: Hem IP adresleri hem de port numaraları için isim çözümlemesi yapma (örn. port 80 yerine 'http' yazmaz).
*   **`-v`, `-vv`, `-vvv`**: Çıktının detay seviyesini artırır (daha fazla protokol bilgisi gösterir).
*   **`-c <sayı>`**: Belirtilen sayıda paketi yakaladıktan sonra durur.
*   **`-s <snaplen>` (Snapshot Length):** Her paketin ne kadarının yakalanacağını belirtir (bayt cinsinden). `-s 0` genellikle paketin tamamını yakalamak için kullanılır (bazı eski sürümlerde farklı olabilir). Varsayılan değer genellikle paketin tamamını yakalamak için yeterlidir, ancak bazen özellikle sadece başlık bilgileriyle ilgileniliyorsa daha küçük bir değer kullanılabilir.
*   **`-A`**: Paket içeriğini ASCII olarak yazdırır.
*   **`-X`**: Paket içeriğini hem Hex hem de ASCII olarak yazdırır.
*   **`-w <dosya.pcap>`**: Yakalanan ham paketleri belirtilen dosyaya pcap formatında yazar. Bu dosya daha sonra `tcpdump -r` veya Wireshark gibi araçlarla analiz edilebilir.
    ```bash
    sudo tcpdump -i eth0 -w capture.pcap 
    ```
*   **`-r <dosya.pcap>`**: Canlı yakalama yapmak yerine, daha önce `-w` ile kaydedilmiş bir pcap dosyasını okur ve analiz eder.
    ```bash
    tcpdump -r capture.pcap host 192.168.1.10
    ```

## Filtreleme (BPF - Berkeley Packet Filter)

`tcpdump`'ın en güçlü özelliklerinden biri, yakalanacak paketleri belirli kriterlere göre filtreleyebilmesidir. Bu filtreler, komutun sonuna eklenen BPF ifadeleri ile tanımlanır.

**Filtre Primitifleri:**
*   **`host <ip_veya_hostname>`**: Belirtilen IP veya ana bilgisayar adını kaynak veya hedef olarak içeren paketler.
*   **`net <ağ/maske>`**: Belirtilen ağa ait paketler (örn. `net 192.168.1.0/24`).
*   **`port <port_no>`**: Belirtilen port numarasını kaynak veya hedef olarak içeren TCP veya UDP paketleri.
*   **`portrange <port1>-<port2>`**: Belirtilen port aralığındaki paketler.
*   **`proto <protokol>`**: Belirtilen protokolü kullanan paketler. Yaygın protokoller doğrudan yazılabilir: `tcp`, `udp`, `icmp`, `icmp6`, `arp`, `rarp`.

**Yön Belirteçleri:**
*   **`src`**: Sadece kaynak adres/port ile eşleşir (örn. `src host 10.0.0.5`, `src port 80`).
*   **`dst`**: Sadece hedef adres/port ile eşleşir (örn. `dst net 192.168.0.0/16`, `dst port 443`).

**Birleştirme Operatörleri:**
*   **`and` veya `&&`**: İki koşul da doğru olmalı.
*   **`or` veya `||`**: Koşullardan en az biri doğru olmalı.
*   **`not` veya `!`**: Koşul yanlış olmalı.
*   **`()`**: Operatör önceliğini değiştirmek için parantezler kullanılır (kabuk tarafından yorumlanmaması için genellikle tırnak içine alınır).

**Filtre Örnekleri:**

```bash
# Sadece 192.168.1.50 IP adresini içeren trafik
sudo tcpdump -i eth0 host 192.168.1.50

# Sadece 10.0.0.0/24 ağından gelen trafik
sudo tcpdump -i eth0 src net 10.0.0.0/24

# Sadece 80 numaralı portu içeren TCP veya UDP trafiği
sudo tcpdump -i eth0 port 80

# Sadece 443 numaralı TCP portunu içeren trafik
sudo tcpdump -i eth0 tcp port 443

# Sadece ICMP trafiği
sudo tcpdump -i eth0 icmp

# 192.168.1.100 host'u ile ilgili olmayan tüm trafik
sudo tcpdump -i eth0 not host 192.168.1.100

# Kaynağı 10.0.0.5 olan VE hedef portu 80 VEYA 443 olan trafik
sudo tcpdump -i eth0 'src host 10.0.0.5 and (dst port 80 or dst port 443)'

# Belirli bir host ile ilgili olmayan ve ARP olmayan trafik
sudo tcpdump -i eth0 'not host 192.168.1.1 and not arp' 
```

## Wireshark ile İlişkisi

`tcpdump`, komut satırı için güçlü bir yakalama ve temel analiz aracıdır. Yakalanan paketlerin daha detaylı ve görsel olarak incelenmesi için genellikle **Wireshark** kullanılır. `tcpdump` ile `-w` seçeneği kullanılarak kaydedilen `.pcap` dosyaları doğrudan Wireshark ile açılabilir ve analiz edilebilir.

`tcpdump`, ağ trafiğini anlamak ve sorunları gidermek için vazgeçilmez bir araçtır. Filtreleme yetenekleri sayesinde sadece ilgilenilen trafiği yakalamak mümkündür.
