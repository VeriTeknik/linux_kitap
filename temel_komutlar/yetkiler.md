# Dosya İzinleri, Yetkiler, Kullanıcılar ve Gruplar

Linux ve türevi işletim sistemlerinin en önemli özelliklerinden birisi, "her şey bir dosyadır" felsefesidir. Bütün Linux komutlarının ve sistemlerinin çalışma prensibi bu felsefeye göre oluşturulmuştur, böylece klavye, ses cihazları, monitör gibi cihazlar bile bir metin dosyası gibi müdahale edilebilir. Örneğin cat komutu ile istenilen veri ses kayıt cihazına işaret eden "dosyaya" yönlendirilebilir.

Her şeyin bir dosya olması, beraberinde bu dosyalara erişim yetkilerini de getirmiştir. Linux üzerinde dosyalarınızın yetkilerini görüntülemek için ls komutunu -l parametresiyle çalıştırabilirsiniz.

```bash
eaydin@eaydin ~/devel/crc $ ls -l
total 4
-rwxrwxr-- 1 eaydin plugdev 1925 Nov  4 01:36 crc8.py
```

Yukarıdaki örnekte, ~/devel/crc dizininin içindeki dosyalar yetki ve izinleriyle birlikte listeleniyor. Linux üzerinde "~" karakteri özel bir dizin işaretçisidir. Bu işaret ile mevcut kullanıcının "home" dizini kastedilir. Örneğin *eaydin* kullanıcısı için ~ dizini her zaman /home/eaydin dizinine işaret eder, oysa *root* kullanıcısı için "/root" dizinine işaret eder.

Bu örnete dizinimizde tek dosya olduğunu görüyoruz, evet sadece bu dosyanın özelliklerine bakmak isteseydik, parametre olarak dosyanın ismini verebilirdik.

ls komutu çıktısının üçüncü ve dördüncü sütunlarında bu dosyanın hangi kullanıcı ve hangi gruba ait olduğu görülüyor. *crc8.py* dosyası *eaydin* kullanıcısına ve *plugdev* grubuna aitmiş.