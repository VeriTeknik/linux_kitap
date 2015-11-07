# Dosya İzinleri, Yetkiler, Kullanıcılar ve Gruplar

Linux ve türevi işletim sistemlerinin en önemli özelliklerinden birisi, "her şey bir dosyadır" felsefesidir. Bütün Linux komutlarının ve sistemlerinin çalışma prensibi bu felsefeye göre oluşturulmuştur, böylece klavye, ses cihazları, monitör gibi cihazlar bile bir metin dosyası gibi müdahale edilebilir. Örneğin cat komutu ile istenilen veri ses kayıt cihazına işaret eden "dosyaya" yönlendirilebilir.

Her şeyin bir dosya olması, beraberinde bu dosyalara erişim yetkilerini de getirmiştir. Linux üzerinde dosyalarınızın yetkilerini görüntülemek için ls komutunu -l parametresiyle çalıştırabilirsiniz.

```bash
eaydin@eaydin ~/devel/crc $ ls -l
total 4
-rwxr-xr-- 1 eaydin plugdev 1925 Nov  4 01:36 crc8.py
```

Yukarıdaki örnekte, ~/devel/crc dizininin içindeki dosyalar yetki ve izinleriyle birlikte listeleniyor. Linux üzerinde "~" karakteri özel bir dizin işaretçisidir. Bu işaret ile mevcut kullanıcının "home" dizini kastedilir. Örneğin *eaydin* kullanıcısı için ~ dizini her zaman /home/eaydin dizinine işaret eder, oysa *root* kullanıcısı için "/root" dizinine işaret eder.

Bu örnete dizinimizde tek dosya olduğunu görüyoruz, evet sadece bu dosyanın özelliklerine bakmak isteseydik, parametre olarak dosyanın ismini verebilirdik.

ls komutu çıktısının üçüncü ve dördüncü sütunlarında bu dosyanın hangi kullanıcı ve hangi gruba ait olduğu görülüyor. *crc8.py* dosyası *eaydin* kullanıcısına ve *plugdev* grubuna aitmiş.

ls komutu çıktısının ilk sütunu ise bu kullanıcı ve grubun hangi yetkilere sahip olduğunu belirtiyor.

```bash
-rwxr-xr--
```

Buradaki ilk karakter ("-") şimdilik bir kenara bırakırsak, diğer karakterler 3'erli olarak sırasıyla dosya sahibinin, grubunun ve diğer kullanıcıların yetkilerini göstermektedir. Kısacası *rwx* yazan kısım *eaydin* kullanıcısının yetkilerini, *r-x* yazan kısım *plugdev* grubunun yetkilerini, *r--* yazan kısım için sistemde geriye kalan bütün kullanıcıların yetkilerini göstermektedir.

Bu yetkiler çeşitli anlamların kısaltmalarıdır. *r* harfi okuma yetkisi (read), *w* yazma yetkisi (write) ve *x* çalıştırma yetkisi (execute) anlamına gelmektedir. Bu durumda sistemimizdeki crc8.py dosyasını herkes okuyabiliyor, dosya içeriğini sadece *eaydin* kullanıcısı değiştirebiliyor, dosyayı *eaydin* kullanıcısı ve *plugdev* grubuna dahil bütün kullanıcılar çalıştırabiliyor demektir.

*rwx* yetkilendirme kullanımını *chmod* komutuyla yapabiliriz. Örneğin dosyaya herkesin yazmasını sağlamak istiyorsak aşağıdaki komut yeterli olacaktır.

```bash
chmod +w crc8.py
```

Öte yandan, *rwx* yetkilendirme biçiminin farklı bir notasyonu bulunmaktadır. Okuma, yazma ve çalıştırma işlemlerinin her biri farklı bir rakamla ifade edilmek üzere, toplamları bir dosya için tüm yetkileri gösterebilir.

Örnekle açıklamak gerekirse, okuma işlemi için 4 sayısı, yazma işlemi için 2 sayısı ve çalıştırma işlemi için 1 sayısı tahsis edilmiştir. Bu durumda Dosyanın hem okunması, hem yazılması hem de çalıştırılması için 7 sayısı (4+2+1=7) yetkilendirmeyi ifade edebilir. Veya sadece okuma ve çalıştırma yetkisi vermek için 5 sayısı (4+1=5) yeterli olacaktır.

Bu durumda crc8.py dosyamızın **rwxr-xr--** yetkisini rakamla ifade etmek istersek, 754 sayısı yeterli olacaktır. İlk rakam olan 7 dosya sahibi eaydin kullanıcısının **rwx** yetkisine, ikinci rakam olan 5 dosyayla ilişkilendirilen plugdev grubunun **r-x** yetkisine, 4 sayısı ise sistemde geri kalan tüm kullanıcıların **r--** yetkisine işaret etmektedir.

**chmod** ile bu yetkilendirmeyi vermek için aşağıdaki komutu uygulayabiliriz.

```bash
chmod 754 crc8.py
```

## Dizin Yetkilendirmesi

Dizin yetkilendirmeleri, dosya yetkilendirmelerinden farklı anlamlar taşır.

| Yetki | Rakamsal Gösterim | Anlamı |
| -- | -- | -- |
| Okuma | 4 | Kullanıcının, dizinin içini görüntüleyebileceği anlamına gelir. Örneğin ls komutu ile içeriği görüntülenebilir |
| Yazma | 2 | Kullanıcının, dizinin içinde yeni dosya oluşturabileceği veya mevcut dosyaları silebileceği anlamına gelir. (Aşağıdaki önemli notu inceleyin) |
| Çalıştırma | 1 | Kullanıcının ilgili dizine girebileceği (cd) anlamına gelir. |

## Garip Yetkilendirme Durumları

Bazı dosyaların yetkileri pek alışıldık durumda olmayabilir. Dosyanızı bu şekilde tutmanızın pek bir anlamı yoktur.

* **100 veya 300:** Dosyanın 1 veya 3 yetkisinin olması (yani *--x* veya *-wx* yetkisinin olması) hiçbir anlam ifade etmeyecektir. Çünkü bu dosyanın kişi tarafından çalıştırılabileceği ancak okunamayacağı anlamına gelir. Linux üzerinde bir dosya okunamazsa, çalıştırılamaz da.
* **200:** Bu durum bir dosyaya yazabileceğiniz, ancak okuyamayacağınız anlamına gelir. Belki kullanıcının okumasını istemediğiniz, ancak yazmasını istediğiniz log/rapor dosyaları için kullanılabilir, ancak pek rastlanan bir durum değildir.
* **007:** Bu dosya sahibi ve grubu tarafından hiçbir şekilde erişilemezken geri kalan tüm kullanıcılar tarafından erişilebilir. Potansiyel güvenlik açığıdır ve durumun incelenmesi gerekir.
* **000:** Dosyaya kimsenin bir şey yapamayacağı anlamına gelir. Sadece root kullanıcısı dosyayı okuyabilir veya yazabilir. Öte yandan dosyanın sahibi dosyanın izinlerini değiştirebilir. Dolayısıyla bazı programlar dosyanın geçici süre koruma altında olması için bu durumu seçebilir.

||Önemli NOT||
|-|-|-|
||Bir dosyaya yazma yetkinizin olmaması, onu silemeyeceğiniz anlamına gelmez. Dizin yetkileri dosya yetkilerinden farklıdır ve bir dizine yazma yetkinizin olması, o dizin içindeki dosyaları silebileceğiniz anlamına gelmektedir. Kısacası dosyanın izinleri **000** olsa bile, dizinde yazma yetkiniz varsa, o dosyayı silebilirsiniz. ||
|||||

## Diğer Dosya Yetkileri

Dosya izinlerini incelerken, *ls -l* komut çıktısının ilk sütunundaki ilk karakteri şimdilik gözardı etmiştik. Bu karakter dosya hakkındaki bazı özel durumları göstermektedir. Aşağıdaki değerleri alabilir:

| Gösterim | Açıklaması |
| -- | -- |
| - | Normal dosya. |
| l | Sembolik link. Dosyanın bir link olduğu, işaret ettiği dosyanın farklı noktada olduğunu belirtir. |
| b | Dosyanın bir blok dosyası olduğu anlamına gelir. Kısacası bu dosyaya yazarken (ve okurken) bloklar halinde yazılmalıdır. Genellikle /dev altındaki sabit disk dosyalarında görülür. |
| c | Dosyanın bir karakter özel dosyası olduğu anlamına gelir. Yani blok dosyalarına benzerdir ancak yazıp okuma işlemlerinde karakter kullanılmalıdır. Örneğin /dev altındaki seri port cihazları bu özelliğe sahiptir.  |
| s | Soket dosyası. Programların birbirleri ile iletişim kurarken, doğrudan dosya işaretçisi aracılığıyla veri iletmelerini sağlar.  |

Öte yandan *setuid* özelliği, dosyanın çalıştırma izninin yerine geçebilir. Bu yetki verildiğinde, dosyayı çalıştıran kişiler, sanki dosyanın sahibiymiş gibi çalıştırabilirler. Örneğin root kullanıcısına ait ancak **apache** grubuna ait bir dosya, **+s** izni verildiğinde, apache tarafından çalıştırılınca root etkisiyle çalışır. Dikkatli kullanılmazsa sistemde açıklara neden olabilir. Öte yandan setuid izni dizinlerde farklı davranır. Bir dizine **+s** yetkisi verildiğinde, bu dizin içine kim dosya oluşturursa oluştursun, dosya dizin sahibi tarafından yaratılmış gibi davranır. *crc8.py* dosyamızın setuid olması durumunda *ls -l* çıktısı aşağıdaki gibi olurdu:

```bash
-rwsr-Sr-- 1 eaydin plugdev 1925 Nov  4 01:36 crc8.py
```

Yukarıdaki örnekte, **s**'lerden birinin küçük, diğerinin büyük harf olduğu dikkatinizi çekmiştir. Büyük harf olan, dosyanın setuid izninin olduğu ancak çalıştırma izninin olmadığı anlamına gelir. Kısacası setuid belirlemek anlamsızdır, dolayısıyla sistem bizi uyarmak için o harfi büyük yapar. Özetle yukarıdaki örnekte crc8.py dosyasının grup izninde setupid tanımlanmış ama çalıştırma izni verilmemiştir.

Bir başka yetki biçimi sticky bit'tir. Dosya izinlerinden bağımsız olarak, sadece sahibinin (ve root'un) dosyayı silebileceği anlamına gelir. Yetki dizininin son karakterinde görülür.

```bash
drwxrwxrwt 1 eaydin plugdev    144 Nov  7 16:00 crc
```

## Kullanıcı Ekleme

Linux üzerinde bir kullanıcı eklemek son derece kolaydır. **root** yetkisine sahip olduğunuzda aşağıdaki komutu çalıştırırsanız **veriteknik** isimli bir kullanıcı oluşturulacaktır.

```bash
useradd veriteknik
```

**veriteknik** kullanıcısını şifresini tanımlamak için aşağıdaki komut çalıştırılmalı, ardından iki kere şifre girilmelidir.

```bash
passwd veriteknik
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
```
