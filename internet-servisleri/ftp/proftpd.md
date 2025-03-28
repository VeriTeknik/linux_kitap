# ProFTPD

ProFTPD, yüksek düzeyde yapılandırılabilir, modüler ve yaygın olarak kullanılan bir açık kaynaklı FTP ve FTPS sunucusudur. Yapılandırma sözdizimi Apache web sunucusuna oldukça benzer, bu da Apache'ye aşina olan yöneticiler için öğrenmeyi kolaylaştırır.

**Uyarı:** Standart FTP güvensizdir. Mümkünse, dosya transferi için ProFTPD'yi FTPS modunda (`mod_tls` ile) yapılandırın veya daha modern ve genellikle daha kolay yapılandırılan SFTP (OpenSSH `sshd` tarafından sağlanır) kullanın.

## Kurulum

ProFTPD genellikle dağıtımların standart depolarında bulunur.

*   **RHEL Tabanlı (dnf):** (EPEL deposu gerekebilir)
    ```bash
    sudo dnf install proftpd proftpd-utils -y 
    # FTPS için openssl gerekebilir (genellikle kuruludur)
    ```
*   **Debian Tabanlı (apt):**
    ```bash
    sudo apt update
    sudo apt install proftpd-basic -y 
    # FTPS için: sudo apt install proftpd-mod-crypto -y (veya proftpd-basic içinde olabilir)
    ```

## Temel Yapılandırma (`/etc/proftpd/proftpd.conf`)

Ana yapılandırma dosyası genellikle `/etc/proftpd/proftpd.conf` veya `/etc/proftpd.conf`'tur. Ek yapılandırma dosyaları `/etc/proftpd/conf.d/` veya `/etc/proftpd/modules.conf` gibi yerlerde bulunabilir.

Aşağıda bazı temel direktifler ve güvenlik ayarları bulunmaktadır:

```apache
# Sunucu Adı ve Tipi
ServerName                      "ProFTPD Sunucusu"
ServerType                      standalone # xinetd yerine kendi başına çalışır
DefaultServer                   on

# Port
Port                            21

# Çalıştırılacak Kullanıcı ve Grup
User                            proftpd
Group                           nogroup # veya proftpd

# Maksimum Eşzamanlı Bağlantı
MaxInstances                    30

# Kullanıcıları Ev Dizinlerine Hapsetme (Chroot)
DefaultRoot                     ~   # Kullanıcıyı kendi ev dizinine kilitler
# Belirli bir grup dışındakileri kilitlemek için:
# DefaultRoot ~ !admin_group 

# Geçerli bir kabuğu olmayan kullanıcıların girişine izin ver (örn. /sbin/nologin)
RequireValidShell               off

# Anonim girişi engelle (ÖNEMLİ!)
<Anonymous ~ftp>
  User                          ftp
  Group                         nogroup
  UserAlias                     anonymous ftp
  # Anonim girişi tamamen kapatmak için bu bloğu silin veya:
  <Limit LOGIN>
    DenyAll
  </Limit>
</Anonymous>

# Yükleme/Değiştirme İzinleri
<Directory />
  AllowOverwrite                on
</Directory>
# Daha kısıtlı izinler için <Directory> veya <Limit> blokları kullanılabilir

# Loglama
TransferLog /var/log/proftpd/xferlog
SystemLog   /var/log/proftpd/proftpd.log

# Pasif mod için port aralığı (güvenlik duvarında izin verilmeli)
# PassivePorts                  49152 65534
```

## FTPS Yapılandırması (`mod_tls`)

ProFTPD'de FTPS desteği `mod_tls` modülü ile sağlanır. Bu modülün yüklenmesi ve yapılandırılması gerekir.

1.  **Modülü Yükleme/Etkinleştirme:**
    *   Debian/Ubuntu'da genellikle `proftpd-mod-crypto` paketi ile gelir.
    *   Yapılandırma dosyasında (`modules.conf` veya `proftpd.conf`) `LoadModule mod_tls.c` satırının aktif olduğundan emin olun.

2.  **TLS Yapılandırması (`proftpd.conf` veya ayrı bir `tls.conf`):**
    ```apache
    <IfModule mod_tls.c>
        # TLS'i etkinleştir
        TLSEngine               on
        # Loglama
        TLSLog                  /var/log/proftpd/tls.log
        # Sadece güvenli protokolleri kullan (TLSv1.2 ve sonrası)
        TLSProtocol             TLSv1.2 TLSv1.3 
        # Sertifika ve anahtar dosyaları
        TLSRSACertificateFile     /etc/ssl/certs/proftpd.crt # Kendi sertifika dosyanız
        TLSRSACertificateKeyFile  /etc/ssl/private/proftpd.key # Kendi özel anahtarınız
        # TLSCACertificateFile    /etc/ssl/certs/myca.crt # İstemci sertifikası doğrulaması için (isteğe bağlı)
        # TLSVerifyClient off # İstemci sertifikası isteme (genellikle off)
        
        # Bağlantı gereksinimleri
        TLSRequired             on # Hem kontrol hem veri bağlantısı için TLS zorunlu
        # veya TLSRequired ctrl+data
        
        # Diğer seçenekler
        TLSOptions              NoCertRequest NoSessionReuseRequired # İstemci sertifikası isteme, oturum yeniden kullanımı zorunlu değil
        TLSRenegotiate          none # Yeniden anlaşmayı kapat
    </IfModule>
    ```
    **Not:** FTPS için bir SSL sertifikası ve özel anahtar gereklidir. `openssl` komutu ile kendinden imzalı (self-signed) bir sertifika oluşturabilir veya Let's Encrypt gibi bir otoriteden ücretsiz sertifika alabilirsiniz. Dosya yollarını kendi sertifika konumlarınıza göre ayarlayın.

## Servis Yönetimi (`systemctl`)

Yapılandırma dosyasında değişiklik yaptıktan sonra `proftpd` servisini yeniden başlatmanız gerekir:

```bash
# Yapılandırmayı kontrol et
sudo proftpd -t

# Servisi yeniden başlat
sudo systemctl restart proftpd.service

# Sistem başlangıcında otomatik çalışmasını sağla
sudo systemctl enable proftpd.service

# Servisin durumunu kontrol et
sudo systemctl status proftpd.service
```
Servis adı dağıtıma göre `proftpd` veya `proftpd-basic` olabilir.

## Güvenlik Duvarı (Firewall)

ProFTPD'nin çalışması için güvenlik duvarınızda ilgili portlara izin vermeniz gerekir:
*   **Port 21 (TCP):** FTP/FTPS kontrol bağlantısı.
*   **Pasif Port Aralığı (TCP):** `/etc/proftpd/proftpd.conf` içinde `PassivePorts` ile tanımlanan aralık (Pasif mod - FTPS için de gereklidir).
*   **Port 990 (TCP):** Implicit FTPS (eğer yapılandırıldıysa).

Örnek (`firewalld` kullanarak):
```bash
sudo firewall-cmd --permanent --add-service=ftp
# veya belirli portlar:
# sudo firewall-cmd --permanent --add-port=21/tcp
# sudo firewall-cmd --permanent --add-port=49152-65534/tcp # Pasif port aralığı
sudo firewall-cmd --reload
```

ProFTPD, esnek yapılandırmasıyla güçlü bir FTP/FTPS sunucusudur, ancak yapılandırması `vsftpd`'ye göre biraz daha karmaşık olabilir. Güvenlik için FTPS'i etkinleştirmek veya SFTP kullanmak önemlidir.
