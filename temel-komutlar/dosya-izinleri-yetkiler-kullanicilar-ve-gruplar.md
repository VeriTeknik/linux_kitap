# Dosya İzinleri, Yetkiler, Kullanıcılar ve Gruplar

Linux ve türevi işletim sistemlerinin en önemli özelliklerinden birisi, "her şey bir dosyadır" felsefesidir. Bütün Linux komutlarının ve sistemlerinin çalışma prensibi bu felsefeye göre oluşturulmuştur, böylece klavye, ses cihazları, monitör gibi cihazlar bile bir metin dosyası gibi müdahale edilebilir. Örneğin cat komutu ile istenilen veri ses kayıt cihazına işaret eden "dosyaya" yönlendirilebilir.

Her şeyin bir dosya olması, beraberinde bu dosyalara erişim yetkilerini de getirmiştir. Linux üzerinde dosyalarınızın yetkilerini görüntülemek için ls komutunu -l parametresiyle çalıştırabilirsiniz.

```bash
eaydin@eaydin ~/devel/crc $ ls -l
total 4
-rwxr-xr-- 1 eaydin plugdev 1925 Nov  4 01:36 crc8.py
```

Yukarıdaki örnekte, \~/devel/crc dizininin içindeki dosyalar yetki ve izinleriyle birlikte listeleniyor. Linux üzerinde "\~" karakteri özel bir dizin işaretçisidir. Bu işaret ile mevcut kullanıcının "home" dizini kastedilir. Örneğin _eaydin_ kullanıcısı için \~ dizini her zaman /home/eaydin dizinine işaret eder, oysa _root_ kullanıcısı için "/root" dizinine işaret eder.

Bu örnekte dizinimizde tek dosya olduğunu görüyoruz, evet sadece bu dosyanın özelliklerine bakmak isteseydik, parametre olarak dosyanın ismini verebilirdik.

ls komutu çıktısının üçüncü ve dördüncü sütunlarında bu dosyanın hangi kullanıcı ve hangi gruba ait olduğu görülüyor. _crc8.py_ dosyası _eaydin_ kullanıcısına ve _plugdev_ grubuna aitmiş.

ls komutu çıktısının ilk sütunu ise bu kullanıcı ve grubun hangi yetkilere sahip olduğunu belirtiyor.

```bash
-rwxr-xr--
```

Buradaki ilk karakter ("-") şimdilik bir kenara bırakırsak, diğer karakterler 3'erli olarak sırasıyla dosya sahibinin, grubunun ve diğer kullanıcıların yetkilerini göstermektedir. Kısacası _rwx_ yazan kısım _eaydin_ kullanıcısının yetkilerini, _r-x_ yazan kısım _plugdev_ grubunun yetkilerini, _r--_ yazan kısım için sistemde geriye kalan bütün kullanıcıların yetkilerini göstermektedir.

Bu yetkiler çeşitli anlamların kısaltmalarıdır. _r_ harfi okuma yetkisi (read), _w_ yazma yetkisi (write) ve _x_ çalıştırma yetkisi (execute) anlamına gelmektedir. Bu durumda sistemimizdeki crc8.py dosyasını herkes okuyabiliyor, dosya içeriğini sadece _eaydin_ kullanıcısı değiştirebiliyor, dosyayı _eaydin_ kullanıcısı ve _plugdev_ grubuna dahil bütün kullanıcılar çalıştırabiliyor demektir.

_rwx_ yetkilendirme kullanımını `chmod` komutuyla yapabiliriz. `chmod` komutu hem sembolik (harflerle) hem de oktal (rakamlarla) gösterimi destekler.

**Sembolik Gösterim:**

*   **Kimi etkileyecek:** `u` (user/sahip), `g` (group/grup), `o` (others/diğerleri), `a` (all/tümü - varsayılan).
*   **Ne yapılacak:** `+` (izin ekle), `-` (izin çıkar), `=` (izinleri tam olarak ayarla).
*   **Hangi izin:** `r` (okuma), `w` (yazma), `x` (çalıştırma).

Örnekler:

```bash
# Herkese (a) yazma (+) izni ekle
chmod a+w crc8.py 
# veya kısaca (a varsayılan olduğu için):
chmod +w crc8.py

# Grup (g) ve diğerlerinden (o) çalıştırma (x) iznini kaldır (-)
chmod go-x crc8.py

# Sahip (u) için okuma ve yazma (rw), grup (g) için sadece okuma (r), diğerleri (o) için hiçbir izin verme (=)
chmod u=rw,g=r,o= crc8.py 
```

**Oktal (Rakamlarla) Gösterim:**

Öte yandan, _rwx_ yetkilendirme biçiminin farklı bir notasyonu bulunmaktadır. Okuma, yazma ve çalıştırma işlemlerinin her biri farklı bir rakamla ifade edilmek üzere, toplamları bir dosya için tüm yetkileri gösterebilir.

Örnekle açıklamak gerekirse, okuma işlemi için 4 sayısı, yazma işlemi için 2 sayısı ve çalıştırma işlemi için 1 sayısı tahsis edilmiştir. Bu durumda Dosyanın hem okunması, hem yazılması hem de çalıştırılması için 7 sayısı (4+2+1=7) yetkilendirmeyi ifade edebilir. Veya sadece okuma ve çalıştırma yetkisi vermek için 5 sayısı (4+1=5) yeterli olacaktır.

Bu durumda crc8.py dosyamızın **rwxr-xr--** yetkisini rakamla ifade etmek istersek, 754 sayısı yeterli olacaktır. İlk rakam olan 7 dosya sahibi eaydin kullanıcısının **rwx** yetkisine, ikinci rakam olan 5 dosyayla ilişkilendirilen plugdev grubunun **r-x** yetkisine, 4 sayısı ise sistemde geri kalan tüm kullanıcıların **r--** yetkisine işaret etmektedir.

**chmod** ile bu yetkilendirmeyi vermek için aşağıdaki komutu uygulayabiliriz.

```bash
chmod 754 crc8.py
```

## Dizin Yetkilendirmesi

Dizin yetkilendirmeleri, dosya yetkilendirmelerinden farklı anlamlar taşır.

| Yetki      | Rakamsal Gösterim | Anlamı                                                                                                                                        |
| ---------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Okuma      | 4                 | Kullanıcının, dizinin içini görüntüleyebileceği anlamına gelir. Örneğin ls komutu ile içeriği görüntülenebilir                                |
| Yazma      | 2                 | Kullanıcının, dizinin içinde yeni dosya oluşturabileceği veya mevcut dosyaları silebileceği anlamına gelir. (Aşağıdaki önemli notu inceleyin) |
| Çalıştırma | 1                 | Kullanıcının ilgili dizine girebileceği (cd) anlamına gelir.                                                                                  |

## Garip Yetkilendirme Durumları

Bazı dosyaların yetkileri pek alışıldık durumda olmayabilir. Dosyanızı bu şekilde tutmanızın pek bir anlamı yoktur.

* **100 veya 300:** Dosyanın 1 veya 3 yetkisinin olması (yani _--x_ veya _-wx_ yetkisinin olması) hiçbir anlam ifade etmeyecektir. Çünkü bu dosyanın kişi tarafından çalıştırılabileceği ancak okunamayacağı anlamına gelir. Linux üzerinde bir dosya okunamazsa, çalıştırılamaz da.
* **200:** Bu durum bir dosyaya yazabileceğiniz, ancak okuyamayacağınız anlamına gelir. Belki kullanıcının okumasını istemediğiniz, ancak yazmasını istediğiniz log/rapor dosyaları için kullanılabilir, ancak pek rastlanan bir durum değildir.
* **007:** Bu dosya sahibi ve grubu tarafından hiçbir şekilde erişilemezken geri kalan tüm kullanıcılar tarafından erişilebilir. Potansiyel güvenlik açığıdır ve durumun incelenmesi gerekir.
* **000:** Dosyaya kimsenin bir şey yapamayacağı anlamına gelir. Sadece root kullanıcısı dosyayı okuyabilir veya yazabilir. Öte yandan dosyanın sahibi dosyanın izinlerini değiştirebilir. Dolayısıyla bazı programlar dosyanın geçici süre koruma altında olması için bu durumu seçebilir.

|   |   | Önemli NOT                                                                                                                                                                                                                                                                                                                              |   |
| - | - | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | - |
|   |   | Bir dosyaya yazma yetkinizin olmaması, onu silemeyeceğiniz anlamına gelmez. Dizin yetkileri dosya yetkilerinden farklıdır ve bir dizine yazma yetkinizin olması, o dizin içindeki dosyaları silebileceğiniz anlamına gelmektedir. Kısacası dosyanın izinleri **000** olsa bile, dizinde yazma yetkiniz varsa, o dosyayı silebilirsiniz. |   |
|   |   |                                                                                                                                                                                                                                                                                                                                         |   |

## Diğer Dosya Yetkileri

Dosya izinlerini incelerken, _ls -l_ komut çıktısının ilk sütunundaki ilk karakteri şimdilik gözardı etmiştik. Bu karakter dosya hakkındaki bazı özel durumları göstermektedir. Aşağıdaki değerleri alabilir:

| Gösterim | Açıklaması                                                                                                                                                                                                          |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -        | Normal dosya.                                                                                                                                                                                                       |
| l        | Sembolik link. Dosyanın bir link olduğu, işaret ettiği dosyanın farklı noktada olduğunu belirtir.                                                                                                                   |
| b        | Dosyanın bir blok dosyası olduğu anlamına gelir. Kısacası bu dosyaya yazarken (ve okurken) bloklar halinde yazılmalıdır. Genellikle /dev altındaki sabit disk dosyalarında görülür.                                 |
| c        | Dosyanın bir karakter özel dosyası olduğu anlamına gelir. Yani blok dosyalarına benzerdir ancak yazıp okuma işlemlerinde karakter kullanılmalıdır. Örneğin /dev altındaki seri port cihazları bu özelliğe sahiptir. |
| s        | Soket dosyası. Programların birbirleri ile iletişim kurarken, doğrudan dosya işaretçisi aracılığıyla veri iletmelerini sağlar.                                                                                      |

Öte yandan **setuid** ve **setgid** bitleri, dosyanın çalıştırma izninin yerine geçebilir (`x` yerine `s` veya `S` olarak görünür).

*   **setuid (SUID):** Bir çalıştırılabilir dosyada setuid biti ayarlandığında, dosyayı çalıştıran kişi, dosyayı çalıştırırken geçici olarak dosyanın *sahibinin* yetkilerine sahip olur. Örneğin, `passwd` komutu normal kullanıcıların kendi şifrelerini değiştirebilmesi için setuid root olarak ayarlanmıştır. Dikkatli kullanılmazsa ciddi güvenlik açıklarına neden olabilir.
*   **setgid (SGID):** Bir çalıştırılabilir dosyada setgid biti ayarlandığında, dosyayı çalıştıran kişi, dosyayı çalıştırırken geçici olarak dosyanın *grubunun* yetkilerine sahip olur.
*   **setgid (Dizinlerde):** Bir dizine setgid biti (`chmod g+s dizin_adi`) ayarlandığında, o dizin içinde oluşturulan yeni dosyalar ve alt dizinler, otomatik olarak o dizinin grubuna sahip olur (oluşturan kullanıcının birincil grubu yerine). Bu, bir grup projesi için paylaşılan dizinlerde çok kullanışlıdır. Dizinlerde setuid bitinin ise standart bir etkisi yoktur ve genellikle kullanılmaz.

_crc8.py_ dosyamızın setuid (sahip için) ve setgid (grup için) olması durumunda _ls -l_ çıktısı aşağıdaki gibi olurdu:

```bash
-rwsr-sr-- 1 eaydin plugdev 1925 Nov  4 01:36 crc8.py 
```
Eğer setuid veya setgid biti ayarlanmışsa ancak ilgili konumda çalıştırma (`x`) izni yoksa, `s` yerine büyük `S` harfi görünür. Bu genellikle anlamsız bir durumu veya bir hatayı belirtir. Örneğin:
```bash
-rwSr-Sr-- 1 eaydin plugdev 1925 Nov  4 01:36 crc8.py 
```
Yukarıdaki örnekte, hem sahip hem de grup için setuid/setgid bitleri ayarlanmış ancak çalıştırma izni verilmemiştir.

Bir başka özel yetki biçimi **sticky bit**'tir (`t` veya `T`). Genellikle `/tmp` gibi herkesin yazabildiği dizinlerde kullanılır. Bir dizinde sticky bit ayarlıysa (`chmod +t dizin_adi`), o dizin içindeki bir dosyayı veya alt dizini yalnızca dosyanın/dizinin sahibi, dizinin sahibi veya root kullanıcısı silebilir ya da yeniden adlandırabilir. Yetki dizisinin son karakterinde (`x` yerine) `t` veya `T` olarak görünür (`T` yine çalıştırma izni olmayan durumu belirtir).

```bash
drwxrwxrwt 1 eaydin plugdev    144 Nov  7 16:00 crc
```

## Kullanıcı ve Grup Düzenleme

Linux üzerinde bir kullanıcı eklemek son derece kolaydır. **root** yetkisine sahip olduğunuzda aşağıdaki komutu çalıştırırsanız **eaydin** isimli bir kullanıcı oluşturulacaktır.

```bash
useradd veriteknik
```

**eaydin** kullanıcısını şifresini tanımlamak için aşağıdaki komut çalıştırılmalı, ardından iki kere şifre girilmelidir.

```bash
passwd eaydin
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
```

Şimdi sistemimizde bir **veriteknik** grubu tanımlayacağız, ardından **eaydin** kullanıcısını bu gruba ekleyeceğiz.

```bash
groupadd veriteknik
usermod -a -G veriteknik eaydin
```

Yukarıdaki tekniklerle kullanıcı ve grup ayrı ayrı oluşturulur. Örneğin aşağıdaki komut, mevcut gruba yeni bir kullanıcı oluşturup ekler.

```bash
useradd -G veriteknik ckaraca
```

Sistemden kullanıcı ve grup silmek içinse aşağıdaki komutlar uygulanabilir.

```bash
userdel ckaraca
groupdel veriteknik
```

Bir kullanıcının hangi gruplarda yer aldığını görüntülemek için **id** komutu kullanılabilir.

```bash
id eaydin
uid=1001(eaydin) gid=1002(eaydin) groups=1002(eaydin),1001(veriteknik)
```

Benzer şekilde **groups** komutu da kullanılabilir.

```bash
groups eaydin
eaydin : eaydin adm cdrom sudo dip plugdev lpadmin sambashare veriteknik
```

Gördüğünüz gibi **eaydin** kullanıcısı hem **eaydin** grubunda, hem de **veriteknik** grubunda yer almaktadır. Her kullanıcının kendi grubu da bulunur ve bu gruplar silinemez, bu gruplara _primary group_ denilir.

```bash
groupdel eaydin
groupdel: cannot remove the primary group of user 'eaydin'
```

Bir kullanıcıyı belirli bir ikincil gruptan silmek için genellikle `gpasswd` komutu kullanılır (bu komut çoğu dağıtımda bulunur):

```bash
# eaydin kullanıcısını veriteknik grubundan sil
gpasswd -d eaydin veriteknik 
```
Debian/Ubuntu tabanlı sistemlerde `deluser` komutu da bu iş için kullanılabilir:
```bash
# Debian/Ubuntu'da alternatif:
# deluser eaydin veriteknik
# Removing user `eaydin' from group `veriteknik' ...
# Done.
```

Bir kullanıcıyı, _primary group_ haricindeki tüm gruplardan (yani tüm _secondary group_lardan) silmek içinse aşağıdaki komut uygulanabilir.

```bash
usermod -G "" eaydin
```

Sistemdeki bütün gruplar, /etc/group dosyasında yer alır, aşağıdaki komut ile tamamı listelenebilir. Komutun çalışma prensibi **Gelişmiş Terminal Komutları** bölümünde incelenecektir.

```bash
cut -d: -f1 /etc/group
```

Benzer şekilde sistemdeki tüm kullanıcıları aşağıdaki gibi görüntüleyebilirsiniz.

```bash
cut -d: -f1 /etc/passwd
```

Bu bilgiler doğrudan `/etc/group` (gruplar için) ve `/etc/passwd` (kullanıcılar için) dosyalarında saklanır. Ancak bu dosyaları doğrudan düzenlemek yerine kullanıcı/grup yönetimi komutlarını (useradd, usermod, userdel, groupadd, groupmod, groupdel, gpasswd vb.) kullanmak genellikle daha güvenli ve tutarlıdır. `getent passwd` ve `getent group` komutları da bu dosyaların içeriğini (ve potansiyel olarak diğer veritabanlarını, örneğin LDAP) sorgulamak için kullanılabilir.
