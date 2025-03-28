# Bir sayfanın İnternetteki Serüveni

Vermiş olduğumuz eğitimlerde, çoğunlukla İnternet'in çalışma mantığının çok iyi anlatılmadığı ya da kavranmadığı konusunda deneyimlerimiz tecrübe ile sabittir.

Günümüzde internetin temel taşlarından biri Alan Adı Sistemi'dir (DNS - Domain Name System). Bu sistem, insanların kolayca hatırlayabileceği alan adlarını (örn. `www.veriteknik.com.tr`) makinelerin anladığı IP adreslerine (örn. `94.103.33.130`) çevirir. Bu çeviri işleminin en üst seviyesinde **kök DNS sunucuları (root name servers)** bulunur. Bu sunucuların listesi, internetin düzgün çalışması için kritik öneme sahiptir ve genellikle işletim sistemleri veya DNS çözümleyiciler tarafından bilinir. Bu sistemin koordinasyonu ICANN (Internet Corporation for Assigned Names and Numbers) gibi kuruluşlar tarafından yapılır.

Linux sistemlerde, sistemin bildiği kök sunucuları `dig` komutu ile sorgulayabiliriz:

```bash
dig +nocomments
```

```bash
; <<>> DiG 9.8.2rc1-RedHat-9.8.2-0.37.rc1.el6_7.4 <<>> +nocomments
;; global options: +cmd
;.                IN    NS
.            277813    IN    NS    a.root-servers.net.
.            277813    IN    NS    g.root-servers.net.
.            277813    IN    NS    c.root-servers.net.
.            277813    IN    NS    l.root-servers.net.
.            277813    IN    NS    m.root-servers.net.
.            277813    IN    NS    f.root-servers.net.
.            277813    IN    NS    i.root-servers.net.
.            277813    IN    NS    b.root-servers.net.
.            277813    IN    NS    j.root-servers.net.
.            277813    IN    NS    d.root-servers.net.
.            277813    IN    NS    h.root-servers.net.
.            277813    IN    NS    e.root-servers.net.
.            277813    IN    NS    k.root-servers.net.
a.root-servers.net.    364471    IN    A    198.41.0.4
a.root-servers.net.    364471    IN    AAAA    2001:503:ba3e::2:30
b.root-servers.net.    364471    IN    A    192.228.79.201
b.root-servers.net.    364471    IN    AAAA    2001:500:84::b
c.root-servers.net.    364471    IN    A    192.33.4.12
c.root-servers.net.    364471    IN    AAAA    2001:500:2::c
d.root-servers.net.    364471    IN    A    199.7.91.13
d.root-servers.net.    364471    IN    AAAA    2001:500:2d::d
e.root-servers.net.    364471    IN    A    192.203.230.10
f.root-servers.net.    364471    IN    A    192.5.5.241
f.root-servers.net.    364471    IN    AAAA    2001:500:2f::f
g.root-servers.net.    364471    IN    A    192.112.36.4
h.root-servers.net.    364471    IN    A    198.97.190.53
;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Sat Dec 19 18:22:40 2015
;; MSG SIZE  rcvd: 496
```

Aynı kök sunucu bilgileri genellikle `/etc/named.root` veya benzeri bir dosyada da bulunabilir (BIND gibi DNS sunucu yazılımları tarafından kullanılır). Teorik olarak bu kök sunucuları değiştirmek mümkün olsa da, bu internetin genel işleyişinden kopmak anlamına gelir.

**DNS Çözümleme Adımları:**

Bir web tarayıcısına `https://www.example.com` gibi bir adres yazdığınızda, IP adresini bulmak için genellikle şu adımlar izlenir (bu süreç genellikle `dns_query.png` benzeri bir diagramda gösterilir):

1.  **Yerel Önbellek (Cache):** Tarayıcı ve işletim sistemi önce kendi önbelleklerine bakar, adres daha önce çözümlenmiş mi diye kontrol eder.
2.  **Hosts Dosyası:** İşletim sistemi `/etc/hosts` dosyasına bakar.
3.  **Yerel DNS Sunucusu (Recursive Resolver):** Eğer adres bulunamazsa, istek işletim sisteminin yapılandırıldığı DNS sunucusuna (genellikle internet servis sağlayıcınızın veya Google'ın `8.8.8.8` gibi genel bir sunucusu) iletilir. Bu sunucu "recursive resolver" olarak görev yapar.
4.  **Kök Sunucular:** Yerel DNS sunucusu adresi bilmiyorsa, kök DNS sunucularından birine sorar (".com" alan adından sorumlu sunucunun adresini öğrenir).
5.  **TLD Sunucuları:** Yerel DNS sunucusu, kök sunucudan aldığı bilgiyle ".com" TLD (Top-Level Domain) sunucusuna sorar ("example.com" alan adından sorumlu sunucunun adresini öğrenir).
6.  **Yetkili (Authoritative) Sunucu:** Yerel DNS sunucusu, TLD sunucusundan aldığı bilgiyle "example.com" alan adının yetkili nameserver'ına sorar ("www.example.com" adresinin IP'sini öğrenir).
7.  **Yanıt:** Yetkili sunucu IP adresini yerel DNS sunucusuna bildirir.
8.  **Önbelleğe Alma ve İletme:** Yerel DNS sunucusu aldığı IP adresini kendi önbelleğine kaydeder (belirli bir süre için - TTL) ve tarayıcıya/işletim sistemine iletir.

**Not:** DNS sorgularının gizliliğini ve güvenliğini artırmak için DNS over HTTPS (DoH) ve DNS over TLS (DoT) gibi modern protokoller geliştirilmiştir.

**HTTP/HTTPS İsteği:**

Tarayıcı, alan adının IP adresini öğrendikten sonra web sunucusuna bağlanır:

1.  **TCP Bağlantısı:** Tarayıcı, hedef IP adresine standart port üzerinden bir TCP bağlantısı başlatır.
    *   **HTTPS (Güvenli - Önerilen):** Port 443 kullanılır. Tarayıcı ve sunucu arasında bir **TLS/SSL el sıkışması (handshake)** gerçekleşir. Sunucu sertifikasını sunar, tarayıcı bunu doğrular ve güvenli, şifreli bir kanal oluşturulur.
    *   **HTTP (Güvensiz):** Port 80 kullanılır. Bağlantı şifrelenmez.
2.  **HTTP İsteği:** Tarayıcı, oluşturulan TCP bağlantısı (veya HTTPS için şifreli TLS kanalı) üzerinden bir HTTP isteği gönderir. Bu istek genellikle şunları içerir:
    *   Metot: `GET` (sayfayı isteme), `POST` (veri gönderme) vb.
    *   İstenen Kaynak: `/index.html`, `/images/logo.png` vb.
    *   HTTP Versiyonu: `HTTP/1.1`, `HTTP/2` vb.
    *   Başlıklar (Headers): Tarayıcı tipi (`User-Agent`), kabul edilen içerik tipleri (`Accept`), daha önceki ziyaretlerden kalan çerezler (`Cookie`) gibi ek bilgiler.
3.  **Sunucu İşlemi:** Web sunucusu (örn. Apache, Nginx) isteği alır. İstenen kaynağı (dosya, script vb.) bulur, gerekirse bir uygulama sunucusunu (örn. PHP-FPM) çalıştırır ve bir HTTP yanıtı oluşturur. (Bu süreç `apache_query.png` benzeri bir diagramda gösterilebilir).
4.  **HTTP Yanıtı:** Sunucu, tarayıcıya bir HTTP yanıtı gönderir. Bu yanıt şunları içerir:
    *   Durum Kodu: `200 OK` (başarılı), `404 Not Found` (bulunamadı), `301 Moved Permanently` (yönlendirme), `500 Internal Server Error` (sunucu hatası) vb.
    *   Başlıklar (Headers): İçerik tipi (`Content-Type`), içerik uzunluğu (`Content-Length`), sunucu bilgisi (`Server`), çerez ayarlama (`Set-Cookie`) vb.
    *   İçerik (Body): İstenen HTML sayfası, resim dosyası veya diğer veriler.
5.  **Sayfa Gösterimi:** Tarayıcı, aldığı yanıtı (genellikle HTML) işler, gerekli diğer kaynakları (CSS, JavaScript, resimler) istemek için ek HTTP istekleri yapar ve son olarak sayfayı kullanıcıya gösterir.

**Port Numaraları ve IANA:**
Standart port numaraları (0-1023 arası "well-known ports") IANA (Internet Assigned Numbers Authority) tarafından yönetilir ve belirli servisler için ayrılmıştır (örn. 80 HTTP, 443 HTTPS, 22 SSH, 25 SMTP). Tarayıcılar, protokol belirtildiğinde (http:// veya https://) bu varsayılan portları otomatik olarak kullanır, bu nedenle adres çubuğuna `:80` veya `:443` yazmak genellikle gerekmez.
