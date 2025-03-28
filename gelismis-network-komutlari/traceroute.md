# traceroute

`traceroute` komutu (veya Windows'taki `tracert`), bir ağ paketinin yerel makinenizden hedef bir sunucuya ulaşana kadar geçtiği yönlendiricileri (routers veya hops) ve her bir atlama noktasındaki gecikme sürelerini keşfetmek için kullanılan bir ağ tanılama aracıdır. Ağ bağlantısındaki sorunların veya yavaşlıkların kaynağını belirlemede çok kullanışlıdır.

Van Jacobson tarafından 1987'de geliştirilen `traceroute`, temel olarak `ping` komutunun kullandığı ICMP protokolünü ve IP paketlerindeki TTL (Time To Live) alanını akıllıca kullanarak çalışır.

## Çalışma Prensibi: TTL Manipülasyonu

TTL (Time To Live) değeri, bir IP paketinin ağda ne kadar süre (daha doğrusu kaç yönlendirici/hop) boyunca dolaşabileceğini belirten bir sayaçtır. Paket bir yönlendiriciden geçtiğinde, yönlendirici genellikle TTL değerini bir azaltır. TTL değeri 0'a ulaştığında, yönlendirici paketi daha fazla iletmez ve paketin kaynağına bir ICMP "Time Exceeded" (Zaman Aşıldı) hata mesajı gönderir. Bu hata mesajı, yönlendiricinin kendi IP adresini içerir.

`traceroute`, bu mekanizmayı kullanarak hedefe giden yolu adım adım keşfeder:
1.  Hedefe TTL=1 olan bir paket (genellikle UDP veya ICMP) gönderir.
2.  İlk yönlendirici paketi alır, TTL'i 0 yapar, paketi atar ve kaynağa kendi IP adresini içeren bir ICMP "Time Exceeded" mesajı gönderir. `traceroute` bu yanıtı alır ve ilk hop'un adresini kaydeder.
3.  Hedefe TTL=2 olan bir paket gönderir.
4.  İlk yönlendirici TTL'i 1 yapar ve paketi ikinci yönlendiriciye iletir. İkinci yönlendirici TTL'i 0 yapar, paketi atar ve kaynağa kendi IP adresini içeren bir ICMP "Time Exceeded" mesajı gönderir. `traceroute` ikinci hop'un adresini kaydeder.
5.  Bu işlem, TTL değeri artırılarak devam eder. Paket hedefe ulaştığında, hedef TTL'i azaltır ancak süre dolmadığı için "Time Exceeded" göndermez. Bunun yerine, `traceroute`'un kullandığı protokole bağlı olarak farklı bir yanıt verir (örn. UDP için ICMP "Port Unreachable", ICMP Echo için "Echo Reply", TCP SYN için "SYN/ACK" veya "RST"). `traceroute` bu yanıtı aldığında hedefe ulaştığını anlar ve işlemi sonlandırır.

`traceroute`, her hop için genellikle 3 paket göndererek ortalama gecikme süresini (RTT - Round Trip Time) de ölçer.

## Temel Kullanım

Kullanımı oldukça basittir:
```bash
traceroute <hedef_ip_veya_hostname>
```
Örnek Çıktı:
```bash
traceroute to google.com (216.58.209.14), 30 hops max, 60 byte packets
 1  192.168.100.1 (192.168.100.1)  8.191 ms  8.391 ms  8.403 ms
 2  81.212.171.130.static.turktelekom.com.tr (81.212.171.130)  42.977 ms  45.323 ms  45.343 ms
 3  93.155.0.184 (93.155.0.184)  45.342 ms  45.360 ms  45.368 ms
 4  81.212.106.225.static.turktelekom.com.tr (81.212.106.225)  45.373 ms  45.377 ms  45.401 ms
 5  195.175.174.42.06-ulus-xrs-t2-1.06-ulus-t3-7.statik.turktelekom.com.tr (195.175.174.42)  45.404 ms  45.414 ms  45.420 ms
 6  195.175.166.204.00-gayrettepe-xrs-t2-1.06-ulus-xrs-t2-1.statik.turktelekom.com.tr (195.175.166.204)  5123.341 ms * *
 7  * * *
 8  * * *
 9  * 209.85.250.69 (209.85.250.69)  74.476 ms  76.287 ms
10  209.85.142.189 (209.85.142.189)  76.328 ms  76.329 ms  76.330 ms
11  sof01s12-in-f14.1e100.net (216.58.209.14)  76.291 ms  52.555 ms  52.308 ms
```

Yukarıdaki çıktıyı incelediğimizde, 11 atlama (hop) sonrasında `google.com`'a ulaşıldığı görülmektedir. Her satır, bir yönlendiriciyi temsil eder ve o yönlendiriciye gönderilen 3 paketin gidiş-dönüş süreleri (RTT - Round Trip Time) milisaniye cinsinden gösterilir.

Aradaki `* * *` işaretleri, o hop'taki yönlendiricinin belirli bir süre içinde yanıt vermediği anlamına gelir. Bu durum, yönlendiricinin ICMP Time Exceeded mesajı göndermeyecek şekilde yapılandırılmış olmasından veya ağdaki yoğunluktan kaynaklanabilir. Bir satırda üç yerine daha az zaman değeri olması, o hop için gönderilen bazı paketlerin kaybolduğu veya zaman aşımına uğradığı anlamına gelir.

## Yaygın Seçenekler

*   **`-n`**: IP adresleri için ters DNS çözümlemesi yapma, sadece numerik IP adreslerini göster. Genellikle işlemi hızlandırır.
*   **`-q <sayı>`**: Her hop için gönderilecek sorgu (probe) paketlerinin sayısını belirler (varsayılan 3).
*   **`-f <ilk_ttl>`**: Taramaya başlanacak ilk TTL değerini belirler (varsayılan 1).
*   **`-m <maks_ttl>`**: Maksimum denenecek hop sayısını belirler (varsayılan genellikle 30 veya 64).
*   **`-w <saniye>`**: Her bir sorgu için beklenecek maksimum yanıt süresini saniye cinsinden belirler (varsayılan genellikle 5).
*   **`-I` veya `-M icmp`**: ICMP Echo paketleri kullanarak tarama yapmaya zorlar (ping gibi).
*   **`-T` veya `-M tcp`**: TCP SYN paketleri kullanarak tarama yapmaya zorlar.
    *   **`-p <port>`**: TCP metodu kullanılırken hedef portu belirtir (varsayılan 80). Firewall'ları aşmak için sıkça kullanılır (örn. `-T -p 80` veya `-T -p 443`).
*   **`-U` veya `-M udp`**: UDP datagramları kullanarak tarama yapmaya zorlar (Linux'ta genellikle varsayılan).
    *   **`-p <port>`**: UDP metodu kullanılırken hedef portu belirtir (varsayılan 33434'ten başlar ve artar).
*   **`-6`**: IPv6 kullanarak tarama yapar (eğer sistemde IPv6 yapılandırılmışsa). `traceroute6` komutu da genellikle aynı işlevi görür.

## ICMP, UDP ve TCP Kullanım Metotları

`traceroute`, hedefe ulaşmaya çalışırken farklı protokoller kullanabilir. Hangi metodun çalışacağı, ağdaki güvenlik duvarı kurallarına bağlıdır.

*   **UDP (Varsayılan - Linux):** `traceroute` varsayılan olarak, hedefin kullanmadığı düşünülen yüksek numaralı UDP portlarına (33434'ten başlayarak artan) datagramlar gönderir. Hedefe ulaşıldığında, hedef makine bu portu dinlemediği için bir ICMP "Port Unreachable" mesajı gönderir ve `traceroute` hedefe ulaştığını anlar. Birçok firewall UDP trafiğini engelleyebileceği için bu yöntem her zaman çalışmayabilir.
*   **ICMP (`-I`):** Bu yöntem, `ping` gibi ICMP Echo Request paketleri gönderir. Hedefe ulaşıldığında, hedef ICMP Echo Reply ile yanıt verir. Birçok ağ yöneticisi güvenlik nedeniyle dışarıdan gelen ICMP Echo Request'leri engellediği için bu yöntem de her zaman başarılı olmayabilir. (Windows'taki `tracert` varsayılan olarak bu yöntemi kullanır).
*   **TCP (`-T`):** Bu yöntem, hedefe TCP SYN paketleri gönderir. Genellikle hedef port olarak web sunucularının kullandığı 80 (HTTP) veya 443 (HTTPS) gibi, güvenlik duvarlarında açık olma olasılığı yüksek portlar seçilir (`-p` ile).
    *   Aradaki yönlendiriciler TTL dolduğunda ICMP Time Exceeded gönderir.
    *   Hedefe ulaşıldığında, eğer belirtilen port açıksa, hedef SYN/ACK ile yanıt verir. `traceroute` bu yanıtı aldığında hedefe ulaştığını anlar ve bağlantıyı tamamlamak yerine bir RST (Reset) paketi göndererek bağlantıyı hemen kapatır ("half-open" tekniği).
    *   Eğer hedef port kapalıysa, hedef RST paketi gönderir ve `traceroute` yine hedefe ulaştığını anlar.
    TCP metodu, ICMP veya UDP'nin engellendiği durumlarda genellikle daha başarılı sonuç verir.

## Alternatif Araçlar

*   **`mtr` (My Traceroute):** `ping` ve `traceroute`'u birleştiren, sürekli güncellenen interaktif bir araçtır. Her hop için paket kaybı ve gecikme istatistiklerini dinamik olarak gösterir. Genellikle `mtr <hedef>` şeklinde kullanılır.
*   **`tracepath`:** `traceroute`'a benzer, ancak genellikle root yetkisi gerektirmez (ICMP yerine UDP kullanır) ve yol üzerindeki MTU (Maximum Transmission Unit) değerini keşfetmeye çalışır.

`traceroute` ve benzeri araçlar, ağ bağlantı sorunlarının yerini tespit etmek için vazgeçilmezdir.
