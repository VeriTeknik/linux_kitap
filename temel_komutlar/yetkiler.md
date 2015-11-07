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

Bu durumda crc8.py dosyamızın rwxr-xr-- yetkisini rakamla ifade etmek istersek, 754 sayısı yeterli olacaktır. İlk rakam olan 7 dosya sahibi eaydin kullanıcısının rwx yetkisine, ikinci rakam olan 5 dosyayla ilişkilendirilen plugdev grubunun r-x yetkisine, 4 sayısı ise sistemde geri kalan tüm kullanıcıların r-- yetkisine işaret etmektedir.

chmod ile bu yetkilendirmeyi vermek için aşağıdaki komutu uygulayabiliriz.

```bash
chmod 754 crc8.py
```

## Dizin Yetkilendirmesi

Dizin yetkilendirmeleri, dosya yetkilendirmelerinden farklı anlamlar taşır.




## Garip Yetkilendirme Durumları

Bazı dosyaların yetkileri pek alışıldık durumda olmayabilir. Dosyanızı bu şekilde tutmanızın pek bir anlamı yoktur.

* **100 veya 300:** Dosyanın 1 veya 3 yetkisinin olması (yani *--x* veya *-wx* yetkisinin olması) hiçbir anlam ifade etmeyecektir. Çünkü bu dosyanın kişi tarafından çalıştırılabileceği ancak okunamayacağı anlamına gelir. Linux üzerinde bir dosya okunamazsa, çalıştırılamaz da.
* **200:** Bu durum bir dosyaya yazabileceğiniz, ancak okuyamayacağınız anlamına gelir. Belki kullanıcının okumasını istemediğiniz, ancak yazmasını istediğiniz log/rapor dosyaları için kullanılabilir, ancak pek rastlanan bir durum değildir.
* **007:** Bu dosya sahibi ve grubu tarafından hiçbir şekilde erişilemezken geri kalan tüm kullanıcılar tarafından erişilebilir. Potansiyel güvenlik açığıdır ve durumun incelenmesi gerekir.
* **000:** Dosyaya kimsenin bir şey yapamayacağı anlamına gelir. Sadece root kullanıcısı dosyayı okuyabilir veya yazabilir. Öte yandan dosyanın sahibi dosyanın izinlerini değiştirebilir. Dolayısıyla bazı programlar dosyanın geçici süre koruma altında olması için bu durumu seçebilir.

* Önemli NOT: Bir dosyaya yazma yetkinizin olmaması, onu silemeyeceğiniz anlamına gelmez. Dizin yetkileri dosya yetkilerinden farklıdır ve bir dizine yazma yetkinizin olması, o dizin içindeki dosyaları silebileceğiniz anlamına gelmektedir. Kısacası dosyanın izinleri **000** olsa bile, dizinde yazma yetkiniz varsa, o dosyayı silebilirsiniz.