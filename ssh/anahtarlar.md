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

**NOT:** RSA'yi geliştirenlerden biri olan Adi Shamir'in de dahil olduğu bir ekip 4096-bit RSA şifrelemeyi **dolaylı yoldan** kırmayı [başarmıştır.](http://www.cs.tau.ac.il/~tromer/acoustic/) Teknik olarak algoritmanın bir açığından faydalanılmamıştır, dolayısıyla yöntem RSA'in bir zaafiyetini göstermemektedir. Dolaylı yoldan şifre kırma yöntemlerinde şifreleme yöntemine saldırılmaz ancak ilgili bilgiyi edinmek için sistemin farklı zaafiyetlerinden faydalanılır. Örneğin telefon şifrenizi öğrenmek için telefonunuzun ekranında parmaklarınızın bıraktığı yağ/kir miktarına bakarak şifrenizi elde etmek, şifreleme mekanizmanızın bir zaafiyeti değil, sistemin bir zaafiyetidir. Adi Shamir, Daniel Genkin ve Eran Tromer de benzer şekilde RSA şifreleme kullanan sistemlerin işlemcilerinin çok yüksek frekansta çıkardığı gürültüyü özel cihazlarla dinleyip, analiz edip, bu gürültüden elde ettikleri periyodik desenlerle RSA anahtarlarının desenlerini karşılaştırarak 4096-bit'lik anahtarları çözmüşlerdir. *Acoustic cryptoanalysis* adı verilen bu inceleme yöntemiyle aslında dinlenen gürültü doğrudan işlemciden kaynaklanmamaktadır, ancak işlemciyi besleyen elektronik bileşenlerden kaynaklanmaktadır. Araştırmacılar basit bir cep telefonunu bir laptoptan 30 cm uzakta tutarak bu gürültünün algılanabildildiğini, hassas bir parabolic mikrofon kullanaraksa 4 metre uzaktaki bir cihazın işlemci gürültüsünü tespit ederek şifreyi kırabildiklerini belirtmişlerdir. Ayrıca gürültü elektrik voltaj dalgalanmalarından kaynaklandığı için, benzer desenlerin bilgisayarın VGA, USB veya Ethernet kablolarının topraklama hatlarından algılanabildiğini belirtmişlerdir. Konu hakkındaki makaleye [şuradan](http://www.cs.tau.ac.il/~tromer/papers/acoustic-20131218.pdf), CRYPTO 2014'te yöntemi tanıttıkları video sunumuna ise [şuradan](https://www.youtube.com/watch?v=DU-HruI7Q30) erişebilirsiniz.

### ssh-keygen

SSH anahtar çifti oluşturmak için pek çok progrma bulunur. Linux üzerinde bunu yapmanın standart yolu ssh-keygen programını kullanmaktır.

```bash
ssh-keygen -t rsa -b 4096 -C "egiti@veriteknik.com"
```

Yukarıdaki komut ile 4096-bit'lik bir RSA anahtarı oluşturulur. Eğer parametre olarak rsa belirtmeseydik de ssh-keygen programı kriptolama algoritması olarak RSA'yı tercih edecekti. -C ile belirttiğimiz e-posta adresi aslında bir açıklama satırıdır. Çoğunlukla bu satırlar hangi anahtarın kime ait olduğunu hatırlamakta kullanılırlar. Açıklama satırı belirtmemekte sakınca yoktur.

Anahtar çifti oluşturmak istediğimiz belirttikten sonra program bize dosyayı nereye kaydedeceğini sorar, standart yolu kullanıcının ev dizininin altındaki ```.ssh``` dizini'dir.

