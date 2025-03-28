# Private ve Public Anahtarlar

SSH bağlantılarını şifreler haricinde, anahtarlar ile gerçekleştirebiliriz. Bu sayede sunucuların şifrelerini bilmemiz gerekmez. Başka birilerinin sunucumuza giriş yetkisi edinmesi için erişim şifresini kendileriyle paylaşmak yerine, kendilerinin anahtarlarını sisteme eklememiz yeterli olur.

## Çalışma Biçimi

SSH anahtarları temel olarak şu prensipte çalışır: Anahtarın iki yarısı bulunur. Birisine private (gizli) anahtar, diğerine public (açık) anahtar denilir. Public anahtarı yüklediğiniz sunucular, ilgili private anahtarı elinde bulunduranlara giriş yetkisi verir. Özetle public anahtarınızı herkesle paylaşabilirsiniz, bu yüzden ismi publictir. Ancak sisteminize tanımadığınız public anahtarlar eklememelisiniz, bu durum tanımadığınız kişilere giriş yetkisi vereceğiniz anlamına gelir.

Benzer şekilde private anahtarınızı kimseyle paylaşmamalısınız. Aksi takdirde public anahtarlarınızın yüklü olduğu bütün sistemlere girebilirler.

Oluşturulan private ve public anahtar çiftleri farklı kriptolama teknikleri kullanır.

## Kriptolama Teknikleri ve Anahtar Oluşturma (`ssh-keygen`)

OpenSSH, kimlik doğrulama için çeşitli açık anahtar algoritmalarını destekler. Geçmişte RSA ve DSA yaygın olarak kullanılmış olsa da, günümüzde güvenlik ve performans açısından **Ed25519** algoritması **şiddetle tavsiye edilmektedir**. ECDSA da iyi bir alternatiftir. RSA hala kullanılabilir (özellikle eski sistemlerle uyumluluk için), ancak en az 3072 veya 4096 bit anahtar uzunluğu önerilir. DSA ise artık güvensiz kabul edilmektedir ve kullanılmamalıdır.

SSH anahtar çifti (private ve public) oluşturmak için standart araç `ssh-keygen` programıdır.

**Ed25519 Anahtarı Oluşturma (Önerilen):**
```bash
ssh-keygen -t ed25519 -C "kullanici@makine_adi"
```
*   `-t ed25519`: Anahtar türünü Ed25519 olarak belirtir.
*   `-C "..."`: Anahtara bir yorum ekler (genellikle e-posta adresi veya kullanıcı@makine formatında). Bu yorum, anahtarı tanımlamaya yardımcı olur ancak işlevsel bir etkisi yoktur.

**RSA Anahtarı Oluşturma (Eski Sistemlerle Uyumlu):**
```bash
ssh-keygen -t rsa -b 4096 -C "kullanici@makine_adi"
```
*   `-t rsa`: Anahtar türünü RSA olarak belirtir.
*   `-b 4096`: Anahtar uzunluğunu bit cinsinden belirtir (RSA için en az 3072 veya 4096 önerilir).

`ssh-keygen` komutu çalıştırıldığında size birkaç soru soracaktır:

1.  **Dosya Yolu:** Anahtarın nereye kaydedileceğini sorar. Varsayılan yol genellikle kullanıcının ev dizinindeki `~/.ssh/` klasörüdür (örn. `~/.ssh/id_ed25519` veya `~/.ssh/id_rsa`). Varsayılanı kabul etmek için Enter'a basabilir veya farklı bir yol/isim belirtebilirsiniz.
2.  **Parola (Passphrase):** Özel anahtarınızı korumak için bir parola belirlemenizi ister. **Güçlü bir parola belirlemek şiddetle tavsiye edilir.** Bu parola, özel anahtarınızın çalınması durumunda bile kullanılmasını engeller. Parola olmadan anahtar oluşturmak için bu adımı boş geçebilirsiniz (Enter'a iki kez basın), ancak bu daha az güvenlidir.

Örnek Ed25519 anahtar oluşturma adımları:
```bash
$ ssh-keygen -t ed25519 -C "test@example.com"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:aBcDeFgHiJkLmNoPqRsTuVwXyZ1234567890abcDef user@hostname
The key's randomart image is:
+--[ED25519 256]--+
|        . .. .   |
|       . + ..    |
|      . =.o ..   |
|     . *.*o..    |
|    . = S=o.     |
|   . =.B.B .     |
|    =o*.B . .    |
|   =o=o= . .     |
|  .E+*+..        |
+----[SHA256]-----+
```

Anahtar çifti oluşturmak istediğimiz belirttikten sonra program bize dosyayı nereye kaydedeceğini sorar, standart yolu kullanıcının ev dizininin altındaki ```.ssh``` dizini'dir.

Anahtar çiftinin yolunu belirttikten sonra, anahtarımızı şifrelemek istiyorsak bize şifresini sorar. Bu adımı boş bırakırsak anahtarımız şifresiz kullanılabilir olur. Bir şifre belirlemenizi şiddetle tavsiye ediyoruz. Böylece özel anahtarınız başkası tarafından çalınsa bile, şifreyi bilmedikleri için kullanamayacaklardır.

Örnek adımlar aşağıdaki gibi görülebilir.

```bash
eaydin@dixon ~/calisma/anahtar $ ssh-keygen -t rsa -b 4096 -C "egitim@veriteknik.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/eaydin/.ssh/id_rsa): egitim_rsa
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in egitim_rsa.
Your public key has been saved in egitim_rsa.pub.
The key fingerprint is:
41:67:e7:38:b4:9e:fe:94:c8:47:73:be:77:77:d6:ce egitim@veriteknik.com
The key's randomart image is:
+--[ RSA 4096]----+
|        . + .    |
|       . + =     |
|        . + .    |
|         o o     |
|        S o o .  |
|         o o =   |
|          + + . .|
|           +   oB|
|            . .+E|
+-----------------+
```

Bu işlemlerin ardından iki dosya oluşur:
*   `id_ed25519` (veya belirttiğiniz isim): **Özel (Private) Anahtar**. Bu dosyayı **gizli tutmalı** ve kimseyle paylaşmamalısınız. İzinleri genellikle `600` (`-rw-------`) olmalıdır.
*   `id_ed25519.pub` (veya belirttiğiniz isim`.pub`): **Açık (Public) Anahtar**. Bu dosyanın içeriğini, şifresiz bağlanmak istediğiniz uzak sunuculardaki ilgili kullanıcının `~/.ssh/authorized_keys` dosyasına eklemelisiniz. Bu anahtarı başkalarıyla paylaşmanızda genellikle bir sakınca yoktur.

### `authorized_keys` Dosyası ve İzinler

Uzak sunucuda, bağlanmak istediğiniz kullanıcının ev dizini altında `~/.ssh/authorized_keys` dosyası bulunur. Bu dosya, o kullanıcı olarak sunucuya bağlanmasına izin verilen kişilerin **açık (public)** anahtarlarını içerir (her anahtar genellikle tek bir satırda yer alır).

Yerel makinenizde oluşturduğunuz `.pub` uzantılı dosyanın içeriğini kopyalayıp, uzak sunucudaki ilgili kullanıcının `~/.ssh/authorized_keys` dosyasının sonuna eklemeniz gerekir.

**Önemli Dosya İzinleri:** SSH anahtar tabanlı kimlik doğrulamanın çalışması için uzak sunucudaki dosya ve dizin izinleri çok önemlidir:
*   Kullanıcının ev dizini (`~`) grup veya diğer kullanıcılar tarafından yazılabilir olmamalıdır (`drwxr-xr-x` veya `755` genellikle uygundur).
*   `.ssh` dizininin izinleri `700` (`drwx------`) olmalıdır (sadece sahip okuyabilir, yazabilir, çalıştırabilir).
*   `authorized_keys` dosyasının izinleri `600` (`-rw-------`) olmalıdır (sadece sahip okuyabilir, yazabilir).

Bu izinler doğru ayarlanmazsa, SSH sunucusu güvenlik nedeniyle anahtar tabanlı kimlik doğrulamayı reddedebilir.

### Açık Anahtarı Sunucuya Kopyalama (`ssh-copy-id`)

Açık anahtarınızı (`.pub` dosyasının içeriğini) manuel olarak kopyalayıp `authorized_keys` dosyasına eklemek yerine, `ssh-copy-id` komutunu kullanmak daha kolay ve genellikle daha güvenli bir yoldur. Bu komut, belirtilen sunucuya şifre ile bağlanır (eğer şifre tabanlı giriş açıksa) ve yerel makinenizdeki açık anahtarı otomatik olarak uzak sunucudaki doğru `authorized_keys` dosyasına ekler, ayrıca gerekli dizin ve dosya izinlerini de ayarlamaya çalışır.

```bash
# Varsayılan anahtarı (örn. ~/.ssh/id_ed25519.pub) kopyala
ssh-copy-id kullanici@uzak_sunucu_adresi

# Belirli bir anahtarı (-i) ve portu (-p) kullanarak kopyala
ssh-copy-id -i ~/.ssh/baska_bir_anahtar.pub -p 2299 kullanici@uzak_sunucu_adresi
```
Komut sizden uzak sunucudaki kullanıcının şifresini isteyecektir. Başarıyla tamamlandıktan sonra, artık o sunucuya şifre yerine SSH anahtarınızla (ve eğer belirlediyseniz anahtar parolanızla) bağlanabilirsiniz.

```bash
eaydin@dixon ~ $ ssh-copy-id root@test-centos1
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Host key fingerprint is 9c:74:68:d4:2d:93:70:bb:fe:c1:e7:6e:63:b0:f0:f1
root@test-centos1's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@test-centos1'"
and check to make sure that only the key(s) you wanted were added.
```

`ssh-copy-id` komutu, anahtar zaten `authorized_keys` dosyasında varsa tekrar eklemez. `-n` seçeneği ile kopyalama işlemini yapmadan sadece deneme (dry-run) yapabilirsiniz.
