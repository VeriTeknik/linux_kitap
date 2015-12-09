# Private ve Public Anahtarlar

SSH bağlantılarını şifreler haricinde, anahtarlar ile gerçekleştirebiliriz. Bu sayede sunucuların şifrelerini bilmemiz gerekmez. Başka birilerinin sunucumuza giriş yetkisi edinmesi için erişim şifresini kendileriyle paylaşmak yerine, kendilerinin anahtarlarını sisteme eklememiz yeterli olur.

## Çalışma Biçimi

SSH anahtarları temel olarak şu prensipte çalışır: Anahtarın iki yarısı bulunur. Birisine private (gizli) anahtar, diğerine public (açık) anahtar denilir. Public anahtarı yüklediğiniz sunucular, ilgili private anahtarı elinde bulunduranlara giriş yetkisi verir. Özetle public anahtarınızı herkesle paylaşabilirsiniz, bu yüzden ismi publictir. Ancak sisteminize tanımadığınız public anahtarlar eklememelisiniz, bu durum tanımadığınız kişilere giriş yetkisi vereceğiniz anlamına gelir.

Benzer şekilde private anahtarınızı kimseyle paylaşmamalısınız. Aksi takdirde public anahtarlarınızın yüklü olduğu bütün sistemlere girebilirler.

Oluşturulan private ve public anahtar çiftleri farklı kriptolama teknikleri kullanır.

## Kriptolama Teknikleri

Sunucularımızda kullandığımız OpenSSH yazılımı, RSA, DSA ve ECDSA şifreleme tekniklerini destekleyebilir. Nasıl çalıştıklarını anlatmak bu kitabın konusu dışında olduğundan detaya girmeyeceğiz.

ECDSA'nın OpenSSH 5.7 versiyonunda desteklenmeye başladığı ve bugün çoğu sistemin bu versiyona geçmediği (örneğin CentOS 6 sunucularda bulunmaz) düşünülürse, tercih edilmemesi yerinde olur.

Ayrıca DSA algoritmaları daha eski olduğu için eski sistemlerde denk gelmek olasıdır, ancak artık çok güvenilir olduğu düşünülmüyor. Örneğin OpenSSH 7.0 versiyonunda DSA desteğini öntanımlı olarak kapalı biçimde tanımladı. Her ne kadar konfigürasyon dosyalarından DSA kullanımı tekrar çalıştırılabilir hale getirilse de ileride bu desteği tamamen kaldırılacağı [belirtiliyor. ](https://www.gentoo.org/support/news-items/2015-08-13-openssh-weak-keys.html)

Bütün bu parametreler göz önünde bulundurulunca bugün en yaygın kullanılan SSH kriptolama tekniği RSA oluyor. Biz de kitabımızda bu teknik ile anahtarlama anlatacağız.

## RSA Anahtar Çiftleri Oluşturma

RSA kriptolama algoritması, Ron Rivest, Adi Shamir ve Leonard Adleman tarafından 1997'de [yayımlandı.](https://people.csail.mit.edu/rivest/Rsapaper.pdf) Bu yöntem ile birlikte amaç, özellikle ileride yaygınlaşacağı öngörülen eposta trafiğinde kişilerin kriptolu iletişim sağlamasını, ayrıca okunan mesajların doğru kaynaktan geldiğinin teyit edilmesini sağlamaktı. Bugün yeterince yüksek asal sayılar kullanıldığında RSA'nin kriptolama mekanizması kırılamamıştır. Bu açıdan RSA'yi en güvenilir kriptolama mekanizmalarından biri yapar.

**NOT:** RSA'yi geliştirenlerden biri olan Adi Shamir'in de dahil olduğu bir ekip 4096-bit RSA şifrelemeyi **dolaylı yoldan** kırmayı [başarmıştır.](http://www.cs.tau.ac.il/~tromer/acoustic/) Teknik olarak algoritmanın bir açığından faydalanılmamıştır, dolayısıyla yöntem RSA'in bir zaafiyetini göstermemektedir. Dolaylı yoldan şifre kırma yöntemlerinde şifreleme yöntemine saldırılmaz, ancak ilgili bilgiyi edinmek için sistemin farklı zaafiyetlerinden faydalanılır. Örneğin telefon şifrenizi öğrenmek için telefonunuzun ekranında parmaklarınızın bıraktığı yağ/kir miktarına bakarak şifrenizi elde etmek, şifreleme mekanizmanızın bir zaafiyeti değil, sistemin bir zaafiyetidir. Adi Shamir, Daniel Genkin ve Eran Tromer de benzer şekilde RSA şifreleme kullanan sistemlerin işlemcilerinin çok yüksek frekansta çıkardığı gürültüyü özel cihazlarla dinleyip, analiz edip, bu gürültüden elde ettikleri periyodik desenlerle RSA anahtarlarının desenlerini karşılaştırarak 4096-bit'lik anahtarları çözmüşlerdir. *Acoustic cryptoanalysis* adı verilen bu inceleme yöntemiyle aslında dinlenen gürültü doğrudan işlemciden kaynaklanmamaktadır, ancak işlemciyi besleyen elektronik bileşenlerden kaynaklanmaktadır. Araştırmacılar basit bir cep telefonunu bir laptoptan 30 cm uzakta tutarak bu gürültünün algılanabildildiğini, hassas bir parabolik mikrofon kullanaraksa 4 metre uzaktaki bir cihazın işlemci gürültüsünü tespit ederek şifreyi kırabildiklerini belirtmişlerdir. Ayrıca gürültü elektrik voltaj dalgalanmalarından kaynaklandığı için, benzer desenlerin bilgisayarın VGA, USB veya Ethernet kablolarının topraklama hatlarından algılanabildiğini belirtmişlerdir. Konu hakkındaki makaleye [şuradan](http://www.cs.tau.ac.il/~tromer/papers/acoustic-20131218.pdf), CRYPTO 2014'te yöntemi tanıttıkları video sunumuna ise [şuradan](https://www.youtube.com/watch?v=DU-HruI7Q30) erişebilirsiniz.

### ssh-keygen

SSH anahtar çifti oluşturmak için pek çok progrma bulunur. Linux üzerinde bunu yapmanın standart yolu ssh-keygen programını kullanmaktır.

```bash
ssh-keygen -t rsa -b 4096 -C "egitim@veriteknik.com"
```

Yukarıdaki komut ile 4096-bit'lik bir RSA anahtarı oluşturulur. Eğer parametre olarak rsa belirtmeseydik de ssh-keygen programı kriptolama algoritması olarak RSA'yı tercih edecekti. -C ile belirttiğimiz e-posta adresi aslında bir açıklama satırıdır. Çoğunlukla bu satırlar hangi anahtarın kime ait olduğunu hatırlamakta kullanılırlar. Açıklama satırı belirtmemekte sakınca yoktur.

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

Anahtarınızı oluşturduktan sonra ekranda görülen grafik, anahtarınızın görsel ifadesidir. OpenSSH 5.1 versiyonuyla [gelen](http://lists.mindrot.org/pipermail/openssh-unix-dev/2008-July/026693.html) bu özellik, sıkça bağlandığınız makinaların SSH anahtarlarının değişimini görsel olarak daha rahat hatırlamanız için geliştirilmiştir. Aslında bir sunucuya her bağlandığınızda bunu görmeyi sağlayabilirsiniz, ```-o VisualHostKey=yes``` seçeneği bunu sağlar, ancak her defasında görmemenizin sebebi, genellikle ```/etc/ssh/ssh_config``` dosyasında disable edilmiş olmasıdır.

Yukarıdaki işlemlerin ardından iki dosyamız oluşmuştur. ```egitim_rsa``` ve ```egitim_rsa.pub``` dosyaları anahtar çiftimizdir. Uzak sunucuya ```egitim_rsa.pub``` dosyasının içeriğini yerleştirdiğimizde, artık ```egitim_rsa``` anahtarını kullanarak karşı sunucuya bağlanabiliriz.

### authorized_keys

Karşı sunucuda yer alan ```authorized_keys``` dosyası, bu sunucuya bağlanmaya yetkili kişilerin public anahtarlarının tutulduğu yerdir. İlgili kullanıcıların ssh dizinlerinin altında bulunabilir. Örneğin: ```/root/.ssh/authorized_keys``` dosyası, root kullanıcısı olarak bağlanabilecek kişilerin ssh anahtarlarının tutulduğu dosyadır.

Bu dosyaya ilgili satırı eklemek için SSH ile bir kez bağlanmak, veya sunucu başında bu işi yapmak gerekmektedir. Eğer sunucuya erişim için şifresini biliyorsanız bağlanıp ilgili satırı ekleyebilirsiniz. Öte yandan ```ssh-copy-id``` komutu bu işi sizin için yapar.

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

Aynı komutu tekrar edersek, program eklemeyeceğini çünkü anahtarı zaten orada bulduğunu belirtecektir.

```bash
eaydin@dixon ~ $ ssh-copy-id root@test-centos1
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed

/usr/bin/ssh-copy-id: WARNING: All keys were skipped because they already exist on the remote system.
```

Yukarıdaki komutlar, kullanıcının standart SSH anahtarlarını kullanıp bunları ekler. Yani yukarıdaki örneklerde ```~/.ssh/id_rsa.pub``` dosyası okunup içeriği karşı tarafa aktarılmıştır. Eğer farklı bir dosya kullanmak istersek, ```-i``` parametresiyle belirtebiliriz. Bu ifadede dosya adının sonunda **.pub** bulunmazsa, program kendisi bu uzantıyı ekleyip dosyayı okumaya çalışır.

```bash
ssh-copy-id -i /root/farkli_id_dosyasi -p 2299 root@sunucu-ip
```

Yukarıdaki komut, ```sunucu-ip``` sunucusuna **2299** portundan **root** kullanıcısı ile bağlanıp, yerel makinamızdaki ```/root/farkli_id_dosyasi.pub``` dosyasının içeriğini aktarmaya çalışır.