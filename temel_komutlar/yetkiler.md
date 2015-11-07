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

Öte yandan, *rwx* yetkilendirme biçiminin farklı bir notasyonu bulunmaktadır. 