# vsftpd (Very Secure FTP Daemon)

`vsftpd`, güvenlik ve performansa odaklanan, yaygın olarak kullanılan bir FTP ve FTPS sunucusudur. Yapılandırması genellikle diğer FTP sunucularına göre daha basit kabul edilir.

**Uyarı:** Standart FTP güvensizdir. Mümkünse, dosya transferi için `vsftpd`'yi FTPS modunda yapılandırın veya daha modern ve genellikle daha kolay yapılandırılan SFTP (OpenSSH `sshd` tarafından sağlanır) kullanın.

## Kurulum

`vsftpd` genellikle dağıtımların standart depolarında bulunur.

*   **RHEL Tabanlı (dnf):**
    ```bash
    sudo dnf install vsftpd -y
    ```
*   **Debian Tabanlı (apt):**
    ```bash
    sudo apt update
    sudo apt install vsftpd -y
    ```

## Temel Yapılandırma (`/etc/vsftpd.conf`)

Ana yapılandırma dosyası genellikle `/etc/vsftpd.conf`'tur. Aşağıda bazı temel ve güvenlik açısından önemli ayarlar bulunmaktadır (dosyayı `sudo` ile düzenleyin):

```bash
# Anonim girişi engelle (ÖNEMLİ!)
anonymous_enable=NO

# Yerel sistem kullanıcılarının giriş yapmasına izin ver
local_enable=YES

# Kullanıcıların dosya yüklemesine/değiştirmesine izin ver (gerekliyse)
write_enable=YES

# Yerel kullanıcıları kendi ev dizinlerine hapset (chroot) (ÖNEMLİ!)
chroot_local_user=YES
# Eğer chroot edilen ev dizini kullanıcı tarafından yazılabilir ise, 
# güvenlik nedeniyle vsftpd hata verebilir. Bunu aşmak için (dikkatli olun):
allow_writeable_chroot=YES 
# Alternatif olarak, chroot için yazma izni olmayan ayrı bir dizin kullanılabilir.

# Standalone modda çalıştır (xinetd yerine)
listen=YES
# IPv6 dinlemesi için (gerekliyse)
listen_ipv6=YES 

# Yüklemeler için varsayılan umask (022 -> dosyalar 644, dizinler 755)
local_umask=022

# ASCII modunda transferi etkinleştir (bazı eski sistemlerle uyumluluk için gerekebilir)
# ascii_upload_enable=YES
# ascii_download_enable=YES

# Zaman damgalarını GMT olarak göster
use_localtime=NO 

# Bağlantı ve transfer loglarını etkinleştir
xferlog_enable=YES
xferlog_std_format=YES # Standart log formatını kullan
xferlog_file=/var/log/vsftpd.log # Log dosyasının yeri

# Boşta kalma süresi (saniye)
idle_session_timeout=600

# Veri bağlantısı zaman aşımı (saniye)
data_connection_timeout=120

# Banner göster (isteğe bağlı)
ftpd_banner=FTP Sunucusuna Hos Geldiniz.

# Pasif mod için port aralığı (güvenlik duvarında bu portlara izin verilmeli)
# pasv_min_port=40000
# pasv_max_port=40100

# Kullanıcı listesi dosyaları (gerekirse belirli kullanıcılara izin vermek/engellemek için)
# userlist_enable=YES
# userlist_file=/etc/vsftpd.userlist
# userlist_deny=NO # userlist_file'daki kullanıcılara izin ver (YES ise engelle)
```

## FTPS Yapılandırması (TLS/SSL)

FTPS'i etkinleştirmek için `/etc/vsftpd.conf` dosyasına aşağıdaki gibi direktifler eklenir:

```bash
# SSL/TLS'i etkinleştir
ssl_enable=YES

# Anonim kullanıcılar için SSL'i zorunlu kılma (zaten anonim kapalı)
allow_anon_ssl=NO
# Yerel kullanıcılar için hem login hem de veri transferinde SSL'i zorunlu kıl
force_local_data_ssl=YES
force_local_logins_ssl=YES

# Kullanılacak TLS/SSL protokol sürümleri (Sadece modern TLS önerilir)
ssl_tlsv1_2=YES
ssl_sslv2=NO
ssl_sslv3=NO
ssl_tlsv1=NO
ssl_tlsv1_1=NO

# SSL sertifikası ve özel anahtar dosyalarının yolu
# (Genellikle bir SSL sertifikası oluşturmanız veya almanız gerekir, örn. Let's Encrypt)
rsa_cert_file=/etc/ssl/certs/vsftpd.pem # Kendi sertifika dosyanızın yolu
rsa_private_key_file=/etc/ssl/private/vsftpd.pem # Kendi özel anahtar dosyanızın yolu

# Güçlü şifreleme algoritmalarını tercih et
ssl_ciphers=HIGH

# SSL oturumlarının yeniden kullanımıyla ilgili uyumluluk ayarı
require_ssl_reuse=NO 
```
**Not:** FTPS için bir SSL sertifikası ve özel anahtar gereklidir. `openssl` komutu ile kendinden imzalı (self-signed) bir sertifika oluşturabilir veya Let's Encrypt gibi bir otoriteden ücretsiz sertifika alabilirsiniz. Dosya yollarını kendi sertifika konumlarınıza göre ayarlayın.

## Servis Yönetimi (`systemctl`)

Yapılandırma dosyasında değişiklik yaptıktan sonra `vsftpd` servisini yeniden başlatmanız gerekir:

```bash
# Yapılandırmayı kontrol et (bazı dağıtımlarda bu komut olmayabilir)
# vsftpd -olisten=NO /etc/vsftpd.conf 

# Servisi yeniden başlat
sudo systemctl restart vsftpd.service

# Sistem başlangıcında otomatik çalışmasını sağla
sudo systemctl enable vsftpd.service

# Servisin durumunu kontrol et
sudo systemctl status vsftpd.service
```

## Güvenlik Duvarı (Firewall)

`vsftpd`'nin çalışması için güvenlik duvarınızda ilgili portlara izin vermeniz gerekir:
*   **Port 21 (TCP):** FTP kontrol bağlantısı.
*   **Port 20 (TCP):** FTP veri bağlantısı (Aktif mod - nadiren kullanılır).
*   **Pasif Port Aralığı (TCP):** `/etc/vsftpd.conf` içinde `pasv_min_port` ve `pasv_max_port` ile tanımlanan aralık (Pasif mod - yaygın olarak kullanılır).
*   **Port 990 (TCP):** Implicit FTPS (eğer yapılandırıldıysa).

Örnek (`firewalld` kullanarak):
```bash
sudo firewall-cmd --permanent --add-service=ftp
# veya belirli portlar:
# sudo firewall-cmd --permanent --add-port=21/tcp
# sudo firewall-cmd --permanent --add-port=40000-40100/tcp # Pasif port aralığı
sudo firewall-cmd --reload
```

`vsftpd`, doğru yapılandırıldığında güvenli bir FTP/FTPS sunucusu olabilir, ancak SFTP'nin genellikle daha basit ve standart bir güvenli dosya transfer çözümü sunduğunu unutmayın.
