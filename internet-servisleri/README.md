# İnternet Servisleri

GNU/Linux işletim sistemleri, esnek ve güçlü yapısı sayesinde çeşitli internet servislerini (ağ hizmetlerini) sunmak için yaygın olarak kullanılır. Bu servisler, basit bir Raspberry Pi'den güçlü sunucu kümelerine kadar farklı donanımlar üzerinde çalışabilir. Bu bölümde, en sık kullanılan internet servislerinden bazıları ve temel kavramları ele alınacaktır.

En yaygın servisler arasında Web (HTTP/HTTPS), E-posta (SMTP/POP3/IMAP), Alan Adı Sistemi (DNS) ve Zaman Senkronizasyonu (NTP) bulunur. Güvenli iletişim için **HTTPS** kullanımı artık standart hale gelmiştir ve şifrelenmemiş HTTP'den **kaçınılmalıdır**.

Aşağıdaki tabloda sıkça kullanılan bazı servisler, varsayılan portları ve temel işlevleri özetlenmiştir:

| Servis        | Protokol/Port        | Açıklama                                                              | Yaygın Yazılımlar        |
| :------------ | :------------------ | :-------------------------------------------------------------------- | :----------------------- |
| HTTP          | TCP(80)             | Web içeriği sunma (şifrelenmemiş)                                     | Apache, Nginx            |
| HTTPS         | TCP(443)            | Web içeriği sunma (TLS/SSL ile şifrelenmiş)                           | Apache, Nginx            |
| DNS           | UDP(53), TCP(53)    | Alan adı - IP adresi çözümlemesi                                      | BIND, Unbound, Knot, PowerDNS |
| NTP           | UDP(123)            | Ağ Zaman Protokolü (Zaman senkronizasyonu)                            | ntpd, chronyd            |
| SMTP          | TCP(25, 587, 465)   | E-posta Gönderme (Simple Mail Transfer Protocol)                      | Postfix, Exim, Sendmail  |
| POP3          | TCP(110, 995)       | E-posta Alma (Post Office Protocol v3)                                | Dovecot, Courier         |
| IMAP          | TCP(143, 993)       | E-posta Alma/Yönetme (Internet Message Access Protocol)               | Dovecot, Courier         |
| FTP           | TCP(21, 20)         | Dosya Transfer Protokolü (şifrelenmemiş)                              | vsftpd, ProFTPD          |
| FTPS          | TCP(21, 990, 989)   | TLS/SSL üzerinden FTP (şifrelenmiş)                                   | vsftpd, ProFTPD          |
| SFTP (SSH)    | TCP(22)             | SSH Dosya Transfer Protokolü (SSH üzerinden, FTP ile ilgisi yoktur)   | OpenSSH sshd             |

Bir web sitesi sunmak için genellikle bir web sunucusuna (Apache veya Nginx gibi) ve dinamik içerik için PHP-FPM gibi bir uygulama sunucusuna ihtiyaç duyulur. DNS ve E-posta gibi hizmetler harici servis sağlayıcılardan alınabilir veya kendi sunucunuzda kurulabilir. Web sitenizden e-posta gönderimi gerekiyorsa, basit bir Mail Transfer Agent (MTA) kurmak (örn. Postfix'in sadece gönderme yapacak şekilde yapılandırılması) veya doğrudan harici bir SMTP sunucusu üzerinden göndermek (daha yaygın ve önerilen) gerekebilir.

Kendi DNS sunucunuzu çalıştırmak, alan adınız için nameserver kayıtlarını (NS) alan adı kayıt firmanız (registrar) üzerinden doğru şekilde yapılandırmanızı ve sunucunuzun ilgili alan adı için yetkili (authoritative) olmasını gerektirir.

**Bu Bölümdeki Konular:**

*   [Bir Sayfanın İnternetteki Serüveni](bir-sayfanin-internetteki-serueveni.md): Bir web sayfasına erişim sürecinin (DNS, HTTP vb.) genel akışı.
*   [Nameserver (DNS) Servisleri](nameserver-servisleri.md): DNS'in çalışma mantığı ve temel kavramlar.
*   [Apache Web Sunucusu](apache.md): Apache'nin kurulumu ve temel yapılandırması.
*   [PHP-FPM](php-fpm.md): PHP betiklerini Apache veya Nginx ile çalıştırmak için PHP-FPM kullanımı.
*   [FTP Servisleri](ftp/README.md): FTP, FTPS ve SFTP protokolleri ve sunucuları (vsftpd, ProFTPD).
