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

