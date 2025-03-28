# sshd Sunucu Ayarları

Bir önceki bölümde istemciyi nasıl kullanacağımızı ve nasıl ayarlayacağımızı gördük. Bu kısımda sunucu üzerinde OpenSSH ayarlamalarını nasıl yapacağımızı göreceğiz.

Genellikle sunucular üzerinde ssh sunucusu yüklü gelir, ancak bazı sistemlerde \(örneğin masaüstü sistemlerde\) gelmemesi halinde paket yöneticinizden edinebilirsiniz.

Örneğin Ubuntu masaüstü sistemler için aşağıdaki komut ilgili paketleri yükleyecektir.

`apt-get install openssh-server`

## `sshd_config` Dosyası

SSH sunucusunun (sshd - SSH daemon) davranışını kontrol eden ana yapılandırma dosyası genellikle `/etc/ssh/sshd_config` yolunda bulunur. Bu dosyada yapılan değişikliklerin geçerli olması için `sshd` servisinin yeniden yüklenmesi (reload) veya yeniden başlatılması (restart) gerekir.

Buradaki seçenekler istemcide kullanılana benzerdir, ancak bazı noktaların üzerinden geçmekte fayda var.

sshd\_config dosyası hakkında yardım dosyalarına erişmek için

```bash
man 5 sshd_config
```

Aşağıda sıkça değiştirilen veya güvenlik açısından önemli olan bazı seçenekler açıklanmıştır. Dosyadaki varsayılan değerler genellikle `#` ile yorum satırı halindedir; değiştirmek için `#` işaretini kaldırıp istediğiniz değeri yazmalısınız.

### Port

SSH sunucusunun dinleyeceği port numarasını belirtir. Varsayılan değer 22'dir. Güvenlik amacıyla (otomatik saldırıları azaltmak için) bu portu standart olmayan bir porta (örn. 2222) değiştirmek yaygın bir pratiktir.
```
Port 2222 
```
Portu değiştirirseniz, istemciden bağlanırken `-p` seçeneği ile yeni portu belirtmeniz gerekir (`ssh kullanici@sunucu -p 2222`) ve sunucu güvenlik duvarında (firewall) bu porta izin vermelisiniz.

### Protocol

Kullanılacak SSH protokol sürümünü belirtir. SSHv1 güvensizdir ve **kesinlikle kullanılmamalıdır**.
```
Protocol 2
```
Modern sistemlerde bu genellikle varsayılandır veya belirtilmese bile sadece v2 kullanılır.

### Root Girişi (`PermitRootLogin`)

`root` kullanıcısının SSH üzerinden doğrudan bağlanıp bağlanamayacağını kontrol eder. Güvenlik açısından **doğrudan root girişini engellemek şiddetle tavsiye edilir**. Bunun yerine normal bir kullanıcı ile bağlanıp, `sudo` veya `su` komutları ile root yetkisi alınmalıdır.
```
# En güvenli seçenek: Root girişini tamamen engelle
PermitRootLogin no

# Sadece anahtar tabanlı root girişine izin ver, şifre ile girişi engelle
# PermitRootLogin prohibit-password 
# (veya eski sürümlerde: PermitRootLogin without-password)

# Hem şifre hem anahtarla root girişine izin ver (TAVSİYE EDİLMEZ!)
# PermitRootLogin yes 
```

### Şifre ile Kimlik Doğrulama (`PasswordAuthentication`)

Şifre kullanarak SSH bağlantısına izin verilip verilmeyeceğini belirler. Anahtar tabanlı kimlik doğrulama (daha güvenli) kullanılıyorsa, şifre ile kimlik doğrulamayı kapatmak güvenliği artırır.
```
# Şifre ile girişi kapat (Anahtar kullanılmalı)
PasswordAuthentication no

# Şifre ile girişe izin ver (Anahtar yoksa veya tercih edilirse)
# PasswordAuthentication yes 
```
**Not:** Şifre ile girişi kapatmadan önce, en az bir kullanıcı için anahtar tabanlı girişin çalıştığından emin olun, aksi takdirde sunucuya erişiminizi kaybedebilirsiniz!

### Boş Şifre Kullanımı (`PermitEmptyPasswords`)

Boş şifreye sahip kullanıcıların SSH ile bağlanıp bağlanamayacağını belirler. Güvenlik nedeniyle daima `no` olarak ayarlanmalıdır.
```
PermitEmptyPasswords no
```

### Bağlantıyı Canlı Tutma (KeepAlive)

İstemci ile sunucu arasındaki bağlantı belirli süre sonra öldürülebilir. Bunun için sunucunuzda aşağıdaki parametreleri düzenleyebilirsiniz.

```
ClientAliveInterval 300
ClientAliveCountMax 0
```

Yukarıdaki sunucu ayarında istemciden 5 dakikada bir \(300 saniye\) **alive** sinyali beklenir. Eğer bu sinyali **0** defa göndermezse bağlantı kesilir.

Bu ayarlamaya sahip bir sunucuya bağlanırken ssh istemcinizde

```bash
ssh -o ServerAliveInterval=300 -o ServerAliveCountMax=0 sunucu-adi
```

şeklinde bir kullanıma ihtiyaç duyarsınız. Tabii bu ayarları `/etc/ssh/ssh_config` dosyanıza kaydedebilirsiniz de.

Bir diğer bağlantı canlılığı, şifre sorulduğu anda gerçekleşir. Şifre sorgu ekranının ne kadar süreyle aktif kalacağını `LoginGraceTime` değeriyle düzenleyebilirsiniz.

```
LoginGraceTime 60
```

Yukarıdaki ayar ile kullanıcı 1 dakika içerisinde giriş bilgilerini girmezse sunucudan bağlantısı kesilir. Sonsuza kadar bu süreyi uzatmak için **0** değeri girmek yeterlidir.

### Dosya İzin Kontrolleri (`StrictModes`)

SSH sunucusunun, kullanıcının ev dizini, `.ssh` dizini ve `authorized_keys` dosyası gibi dosyaların izinlerini kontrol edip etmeyeceğini belirler. `yes` olarak ayarlanması (varsayılan), bu dosyaların izinleri güvensizse (örn. başkaları tarafından yazılabilirse) anahtar tabanlı kimlik doğrulamayı reddeder. Güvenlik için `yes` olarak kalması önerilir.

```
StrictModes yes
```

### X11 Forwarding (`X11Forwarding`)

Uzak sunucudaki grafiksel uygulamaların (X11) arayüzünün, güvenli SSH tüneli üzerinden yerel makinenize yönlendirilip yönlendirilmeyeceğini belirler. Güvenlik riskleri oluşturabileceğinden, gerekmiyorsa `no` olarak ayarlanabilir.

```
X11Forwarding yes
```

İstemcide bağlanırken `-X` (güvenilir) veya `-Y` (daha az güvenli ama bazı uygulamalar için gerekli olabilir) parametresi kullanılır.

### Diğer Önemli Ayarlar

*   **`PubkeyAuthentication yes`**: Açık anahtar ile kimlik doğrulamayı etkinleştirir (genellikle varsayılan).
*   **`AllowUsers` / `AllowGroups`**: Sadece belirtilen kullanıcıların veya grupların SSH ile bağlanmasına izin verir. Güvenliği artırmak için kullanılabilir (örn. `AllowUsers kullanici1 admin`).
*   **`DenyUsers` / `DenyGroups`**: Belirtilen kullanıcıların veya grupların SSH ile bağlanmasını engeller.
*   **`MaxAuthTries`**: Bir bağlantı için izin verilen maksimum kimlik doğrulama denemesi sayısını sınırlar (örn. `3`). Brute-force saldırılarını yavaşlatır.
*   **`UsePAM yes`**: Kimlik doğrulama için PAM (Pluggable Authentication Modules) kullanımını etkinleştirir. Çoğu sistemde varsayılan ve gereklidir (örn. `/etc/pam.d/sshd` yapılandırmasını kullanır).
*   **`HostKey`**: Sunucunun kimliğini doğrulamak için kullanacağı özel anahtar dosyalarının yollarını belirtir (örn. `/etc/ssh/ssh_host_ed25519_key`).
*   **`HostKeyAlgorithms` / `PubkeyAcceptedKeyTypes` / `KexAlgorithms` / `Ciphers` / `MACs`**: Güvenlik politikalarına göre izin verilen anahtar türlerini, anahtar değişim algoritmalarını, şifreleme algoritmalarını ve mesaj doğrulama kodlarını kısıtlamak için kullanılabilir. Modern ve güvenli algoritmaları (örn. Ed25519, ChaCha20-Poly1305) tercih edip eskileri (örn. DSA, SHA1, CBC) devre dışı bırakmak önerilir.

### Ayarları Uygulama

`/etc/ssh/sshd_config` dosyasında değişiklik yaptıktan sonra, yapılandırmanın geçerli olup olmadığını kontrol etmek ve servisi yeniden yüklemek/başlatmak gerekir:

```bash
# Yapılandırma dosyasının sözdizimini kontrol et
sudo sshd -t

# Eğer hata yoksa, sshd servisini yeniden yükle (genellikle yeterlidir)
sudo systemctl reload sshd
# veya yeniden başlat (gerekirse)
# sudo systemctl restart sshd 
```
Servis adı dağıtıma göre `ssh` veya `sshd` olabilir (`systemctl status sshd` veya `systemctl status ssh` ile kontrol edilebilir).
