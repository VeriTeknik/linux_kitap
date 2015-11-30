# sshd Sunucu Ayarları

Bir önceki bölümde istemciyi nasıl kullanacağımızı ve nasıl ayarlayacağımızı gördük. Bu kısımda sunucu üzerinde OpenSSH ayarlamalarını nasıl yapacağımızı göreceğiz.

Genellikle sunucular üzerinde ssh sunucusu yüklü gelir, ancak bazı sistemlerde (örneğin masaüstü sistemlerde) gelmemesi halinde paket yöneticinizden edinebilirsiniz.

Örneğin Ubuntu masaüstü sistemler için aşağıdaki komut ilgili paketleri yükleyecektir.

```apt-get instal openssh-server```

## sshd Dosyası

Nasıl istemciyi ```/etc/ssh/ssh_config``` dosyasından ayarladıysak, sunucuyu da benzer biçimde ```/etc/ssh/sshd_config``` dosyasından ayarlayabiliriz.

Buradaki seçenekler istemcide kullanılana benzerdir, ancak bazı noktaların üzerinden geçmekte fayda var.

### Port Değiştirme

Çoğunlukla saldırganlar SSH portunuzun standart 22 olduğunu tahmin edip buradan giriş yapmayı denerler. Bunun için genellikle portu değiştirmekte fayda vardır.

Dosya içinde Port yazan satırı bulup karşılığındaki değeri değiştirebilirsiniz.

```
Port 22
```

### Root Girişi

SSH ile root kullanıcısının girişini farklı biçimde ayarlayabilirsiniz. Aşağıda olası seçenekler ve açıklamaları belirtilmiştir.

```
# root hiçbir şekilde ssh ile bağlanamaz
PermitRootLogin no

# root dosyada tanımlanan yöntemlerle bağlanabilir
PermitRootLogin yes

# root şifre kullanarak bağlanamaz, anahtar gerekir
PermitRootLogin without-password
```

### Boş Şifre Kullanımı

Bazı sistemlerde boş şifre kullanımına izin verebilirsiniz. Örneğin sisteminize bir **dummy** kullanıcı oluşturabilirsiniz, bu kullanıcının şifresi olmaz, ve ssh bağlantısı kurulabilir.

Öte yandan sisteminize hiçbir şekilde boş şifre ile bağlanılmasını istemeyebilirsiniz, ancak kullanıcılarınızın bir şifresi olmayabilir. Bu tip durumları engellemek için aşağıdaki seçeneğin bulunmasını tavsiye ederiz.

```
PermitEmptyPasswords no
```

### Bağlantıyı Canlı Tutmak

İstemci ile sunucu arasındaki bağlantı belirli süre sonra öldürülebilir. Bunun için sunucunuzda aşağıdaki parametreleri düzenleyebilirsiniz.

```
ClientAliveInterval 300
ClientAliveCountMax 0
```

Yukarıdaki sunucu ayarında istemciden 5 dakikada bir (300 saniye) **alive** sinyali beklenir. Eğer bu sinyali **0** defa göndermezse bağlantı kesilir.

Bu ayarlamaya sahip bir sunucuya bağlanırken ssh istemcinizde

```bash
ssh -o ServerAliveInterval=300 -o ServerAliveCountMax=0 sunucu-adi
```

şeklinde bir kullanıma ihtiyaç duyarsınız. Tabii bu ayarları ```/etc/ssh/ssh_config``` dosyanıza kaydedebilirsiniz de.

Bir diğer bağlantı canlılığı, şifre sorulduğu anda gerçekleşir. Şifre sorgu ekranının ne kadar süreyle aktif kalacağını ```LoginGraceTime``` değeriyle düzenleyebilirsiniz.

```
LoginGraceTime 60
```

Yukarıdaki ayar ile kullanıcı 1 dakika içerisinde giriş bilgilerini girmezse sunucudan bağlantısı kesilir. Sonsuza kadar bu süreyi uzatmak için **0** değeri girmek yeterlidir.

### StrictModes

Kullanıcıların ssh ayarlarının tutulduğu dosyaların güvenilirliğini kontrol etmeye yarar. Bazen kullanıcılar bu dosyaları 777 gibi güvensiz modlarda tutabilirler. Bu tip durumlarda bağlantıyı engellemek için StrictModes seçeneğini aktif halde tutmak en iyisidir.

```
StrictModes yes
```

### X11 Forwarding

Sunucu üzerindeki programlar X11 pencereleri ile çalışıyorsa, bu pencereleri SSH üzerinden gönderebilirsiniz. Sunucu tarafında aşağıdaki gibi bir ayar olmalı.

```
X11Forwarding yes
```

İstemcide ise bağlanırken ```-X``` parametresi kullanılmalıdır.
