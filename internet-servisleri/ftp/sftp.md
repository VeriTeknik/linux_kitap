# SFTP (SSH File Transfer Protocol)

SFTP, dosya transferi ve yönetimi için SSH (Secure Shell) protokolü üzerine inşa edilmiş güvenli bir ağ protokolüdür. Standart FTP veya FTPS ile karıştırılmamalıdır; tamamen farklı bir protokoldür ancak benzer işlevsellik sunar.

SFTP'nin en büyük avantajı, mevcut SSH altyapısını kullanmasıdır. Çoğu Linux sunucusunda OpenSSH sunucusu (`sshd`) zaten kurulu ve çalışır durumdadır ve bu sunucu genellikle SFTP hizmetini de otomatik olarak sağlar. Bu, ayrı bir FTP sunucusu kurma ve yapılandırma ihtiyacını ortadan kaldırır.

## SFTP'nin Avantajları

*   **Güvenlik:** Tüm iletişim (kimlik doğrulama, komutlar, veri aktarımı) SSH tarafından şifrelenir.
*   **Basitlik:** Genellikle ek sunucu kurulumu gerektirmez (sshd yeterlidir).
*   **Tek Port:** Standart SSH portunu (genellikle 22) kullanır, bu da güvenlik duvarı yapılandırmasını basitleştirir.
*   **İşlevsellik:** Basit dosya yükleme/indirme işlemlerinin yanı sıra dizin listeleme, oluşturma, silme, yeniden adlandırma, sembolik link oluşturma ve izinleri/sahipliği değiştirme gibi gelişmiş dosya sistemi operasyonlarını destekler.
*   **Kimlik Doğrulama:** SSH ile aynı kimlik doğrulama yöntemlerini kullanır (şifre, SSH anahtarları vb.).

## SFTP Sunucu Yapılandırması (OpenSSH `sshd_config`)

SFTP hizmeti genellikle `/etc/ssh/sshd_config` dosyasındaki `Subsystem` direktifi ile etkinleştirilir. Çoğu dağıtımda bu satır varsayılan olarak bulunur ve aktiftir:

```
Subsystem sftp /usr/lib/openssh/sftp-server 
# veya /usr/libexec/openssh/sftp-server gibi farklı bir yol olabilir
```

Bu satır, `sshd`'nin SFTP bağlantı isteklerini nasıl ele alacağını belirtir.

**Kullanıcıları Kendi Ev Dizinlerine Hapsetme (Chroot):**

SFTP kullanıcılarının sadece kendi ev dizinlerini veya belirli bir dizini görmelerini ve bunun dışına çıkamamalarını sağlamak güvenlik açısından önemlidir. Bu, `sshd_config` dosyasında `ChrootDirectory` direktifi ile yapılır. Genellikle belirli bir gruba ait kullanıcılar için uygulanır:

```bash
# Önce sftp kullanıcıları için bir grup oluşturun (eğer yoksa)
# sudo groupadd sftpusers

# sshd_config dosyasını düzenleyin:
# sudo vim /etc/ssh/sshd_config

# Dosyanın sonuna aşağıdaki gibi bir blok ekleyin:

Match Group sftpusers
  ChrootDirectory %h  # Kullanıcıyı kendi ev dizinine (/home/kullanici) hapseder
  # Veya ChrootDirectory /sftp/%u # Kullanıcıyı /sftp/kullanici_adi dizinine hapseder
  ForceCommand internal-sftp # Sadece SFTP erişimine izin ver, SSH kabuğunu engelle
  AllowTcpForwarding no
  X11Forwarding no

# Yapılandırmayı kontrol edip sshd'yi yeniden başlatın
# sudo sshd -t
# sudo systemctl restart sshd
```

**Açıklamalar:**

*   `Match Group sftpusers`: Takip eden direktiflerin sadece `sftpusers` grubundaki kullanıcılar için geçerli olacağını belirtir.
*   `ChrootDirectory %h`: Kullanıcıyı kendi ev dizinine (`%h`) hapseder. Alternatif olarak, `%u` (kullanıcı adı) değişkenini kullanarak her kullanıcı için özel bir dizin belirleyebilirsiniz (örn. `/srv/sftp/%u`).
*   **Önemli:** `ChrootDirectory` ile belirtilen dizinin ve üstündeki tüm dizinlerin sahibinin `root` olması ve başka hiçbir kullanıcı (grup dahil) tarafından **yazılabilir olmaması** gerekir. Kullanıcının dosya yükleyebileceği alt dizinler daha sonra oluşturulup sahibi kullanıcı olarak ayarlanmalıdır. Örneğin, `ChrootDirectory /home/kullanici` kullanılıyorsa, `/home/kullanici` dizininin sahibi `root:root` ve izinleri `755` olmalı, dosya yükleme için `/home/kullanici/upload` gibi bir alt dizin oluşturulup sahibi `kullanici:kullanici` yapılmalıdır.
*   `ForceCommand internal-sftp`: Kullanıcı bağlandığında SSH kabuğu yerine doğrudan `sshd`'nin kendi içindeki SFTP sunucusunu çalıştırır. Bu, kullanıcının sadece SFTP yapabilmesini sağlar, SSH ile komut satırı erişimini engeller.
*   `AllowTcpForwarding no`, `X11Forwarding no`: Güvenlik amacıyla bu kullanıcılar için port yönlendirme ve X11 yönlendirmeyi devre dışı bırakır.

## SFTP İstemci Kullanımı

Komut satırından `sftp` istemcisi kullanılabilir:

```bash
sftp kullanici@uzak_sunucu
# veya farklı port için:
# sftp -P 2222 kullanici@uzak_sunucu
```

Bağlantı kurulduktan sonra, FTP'ye benzer komutlarla etkileşimli bir oturum başlar:
*   `ls`: Uzak dizini listeler.
*   `lls`: Yerel dizini listeler.
*   `cd <dizin>`: Uzak dizini değiştirir.
*   `lcd <dizin>`: Yerel dizini değiştirir.
*   `pwd`: Uzak çalışma dizinini gösterir.
*   `lpwd`: Yerel çalışma dizinini gösterir.
*   `get <uzak_dosya> [yerel_dosya]`: Uzak sunucudan dosya indirir.
*   `put <yerel_dosya> [uzak_dosya]`: Uzak sunucuya dosya yükler.
*   `mkdir <dizin>`: Uzak dizin oluşturur.
*   `rm <dosya>`: Uzak dosyayı siler.
*   `rmdir <dizin>`: Uzak dizini siler.
*   `help` veya `?`: Komut listesini gösterir.
*   `quit` veya `bye` veya `exit`: Oturumu sonlandırır.

Ayrıca FileZilla, WinSCP gibi birçok grafiksel istemci de SFTP protokolünü destekler.

SFTP, güvensiz FTP'ye modern, güvenli ve genellikle daha kolay yönetilebilir bir alternatiftir.
