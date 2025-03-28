# Dosya Transfer Protokolleri (FTP, FTPS, SFTP)

Dosya Transfer Protokolü (FTP - File Transfer Protocol), ağ üzerindeki bilgisayarlar arasında dosya aktarımı yapmak için kullanılan eski ve temel bir protokoldür. Bir istemci-sunucu modeli kullanır; kullanıcı bir FTP istemcisi ile bir FTP sunucusuna bağlanarak dosya yükleyebilir (upload) veya indirebilir (download).

## FTP'nin Güvenlik Zafiyetleri

**Standart FTP protokolü (genellikle TCP port 21 ve 20 kullanılır) doğası gereği güvensizdir.** Hem kullanıcı adı/şifre gibi kimlik bilgileri hem de aktarılan verinin kendisi ağ üzerinde **şifrelenmeden**, düz metin olarak gönderilir. Bu durum, ağ trafiğini dinleyen kötü niyetli kişilerin bu bilgilere kolayca erişmesine olanak tanır. Bu nedenle, **güvenilmeyen ağlarda (özellikle internet üzerinde) standart FTP kullanımından kesinlikle kaçınılmalıdır.**

## Güvenli Alternatifler: FTPS ve SFTP

FTP'nin güvenlik zafiyetlerini gidermek için iki ana alternatif geliştirilmiştir:

1.  **FTPS (FTP over TLS/SSL):** Standart FTP protokolünü, web sitelerinde HTTPS için kullanılan TLS/SSL şifreleme katmanı ile sarmalar. Hem kontrol bağlantısı (komutlar, kullanıcı adı/şifre) hem de veri bağlantısı (dosya içeriği) şifrelenebilir. İki modu vardır:
    *   **Explicit FTPS:** Bağlantı normal FTP portundan (21) başlar, istemci `AUTH TLS` veya `AUTH SSL` komutuyla şifrelemeyi talep eder.
    *   **Implicit FTPS:** Bağlantı doğrudan güvenli bir porttan (genellikle 990) başlar ve tüm iletişim baştan itibaren şifrelenir.
    FTPS, FTP'nin altyapısını kullandığı için NAT ve güvenlik duvarı yapılandırmalarında bazen zorluklar çıkarabilir (özellikle veri bağlantısı için farklı portlar kullanıldığında).
    *   [FTPS](ftps.md)

2.  **SFTP (SSH File Transfer Protocol):** FTP ile **karıştırılmamalıdır**. SFTP, FTP'den tamamen farklı, modern bir protokoldür ve dosya transferi işlemlerini güvenli bir **SSH (Secure Shell)** bağlantısı üzerinden gerçekleştirir.
    *   Tüm iletişim (kimlik bilgileri, komutlar, veri) SSH tüneli içinde şifrelenir.
    *   Genellikle standart SSH portunu (22) kullanır, bu da güvenlik duvarı yapılandırmasını basitleştirir.
    *   Dosya transferi yanında dizin listeleme, oluşturma, silme, izin değiştirme gibi daha gelişmiş dosya sistemi operasyonlarını destekler.
    *   Kimlik doğrulama için SSH anahtarları kullanılabilir.
    *   Günümüzde **güvenli dosya transferi için genellikle SFTP tercih edilen yöntemdir.** SFTP sunucu işlevselliği, çoğu Linux sisteminde zaten kurulu olan OpenSSH sunucusu (`sshd`) tarafından sağlanır.
    *   [SFTP](sftp.md)

## Yaygın Sunucu Yazılımları

*   **vsftpd (Very Secure FTP Daemon):** Güvenlik odaklı, hafif ve yaygın olarak kullanılan bir FTP/FTPS sunucusudur.
    *   [vsftpd](vsftpd.md)
*   **ProFTPD:** Çok esnek yapılandırma seçenekleri sunan, modüler yapıda bir FTP/FTPS sunucusudur.
    *   [ProFTPD](proftpd.md)
*   **OpenSSH sshd:** SFTP hizmetini sağlar.

## İstemci Kullanımı

Dosya transferi için çeşitli komut satırı istemcileri (`ftp`, `sftp`, `lftp`, `scp`, `rsync`) ve grafiksel istemciler (FileZilla, WinSCP vb.) mevcuttur.
*   [Komut Satırından ftp Kullanımı](komut-satirindan-ftp-kullanimi.md) (Not: Bu bölümdeki `ftp` komutu güvensiz protokolü kullanır, `sftp` istemcisi daha güvenlidir.)

Bu bölümdeki diğer dosyalar, farklı FTP sunucularının yapılandırılması ve güvenli alternatifleri hakkında daha detaylı bilgi verecektir.
