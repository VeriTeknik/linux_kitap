# Temel Terminal Komutları

Linux üzerinde temel olarak sınıflandırabileceğimiz komutlar şu şekilde sıralanabilir.

| Komut | Açıklama | Örnek |
| -- | -- | -- |
| cd | Dizin değiştirme | cd /var/www |
| ls | Dizin içeriği listeleme | ls /var/www |
| mkdir | Dizin oluşturma | mkdir /var/www/html |
| rmdir | Dizin silme | rmdir /var/www/html |
| pwd | Mevcut dizini gösterme | pwd |
| rm | Dosya silme | rm /var/www/html/index.html |
| cp | Dosya kopyalama | cp /etc/hosts /root/hosts.bak |
| mv | Dosya taşıma | mv /root/hosts.bak /root/hosts.bak2 |
| cat | Dosya içeriği görüntüleme | cat /etc/hosts |
| echo | Standart çıktıya yazdırma | echo "test" |
| du | Dizin boyutu gösterme | du -sh . |

Yukarıda sıralanan komutlar, Linux komut satırının çok küçük bir kısmını oluştursa da, en sık kullanılan komutlar arasında yer aldıklarını söyleyebiliriz. Burada altının çizilmesi gereken önemli bir nokta, Linux komutlarının birbirleriyle uyum içinde çalışabilecek biçimde tasarlanmışlardır ve bunu yapmak için standart girdi/çıktı yönlendirmeleri haricinde, pek çok parametre alabilmektedirler. Programların parametreleri ve programların genel özellikleri hakkında bilgi almak için man komutu kullanılabilir. Örneğin du komutu hakkında bilgi almak için
```bash
man du
```
yazmak yeterli olacaktır. Bu durumda terminalde du komutunun manual dosyaları görülür. Bu dosyalar artık standartlaştığı için, internetten de bulunabilir. Örneğin du için manual dosyasına şu adresten erişebilirsiniz: http://man7.org/linux/man-pages/man1/du.1.html

Bir diğer önemli nokta, Linux dizin yapısının anlaşılmasındadır. Linuxta dizin gösteriminin birkaç yolu vardır. Örneğin pek çoğumuzun aşina olduğu, "bir üst dizine çıkma" işlemi aşağıdaki şekilde yapılır.

```bash
cd ..
```

Bu komutu uygularken aslında yaptığımız işlem ".. isimli dizine gir" demek oluyor. Linux dizin yapısında her zaman ".." isimli dizin, mevcut dizinin bir üst dizinine işaret ettiği için üst dizine çıkmış oluruz. Dolayısıyla eğer iki üst dizine çıkmak isteseydik
```bash
cd ../..
```
komutu yeterli olacaktı. Benzer şekilde tek nokta ile ifade edilen dizin "." her zaman mevcut dizindir. Dolayısıyla mevcut dizin ile ilgili bir işlem yapmak istiyorsak, "." koymamız yeterli olacaktır.

Örneğin hosts dosyamızı mevcut dizine kopyalamak istiyorsak aşağıdaki komutu uygulamamız gerekir.
```bash
cp /etc/hosts .
```
Çoğunlukla Linux dizin yapısına yeni başlayan kişilerin karıştırdığı nokta, göreli dizinlerdir. Yukarıdaki örnekte /etc/hosts şeklinde dizin ifade ettik. Eğer bunu başındaki / olmadan yazsaydık, yani "etc/hosts" şeklinde yazsaydık, mevcut dizinin altında bir etc klasörü, onun da altında bir hosts dosyası arayacaktı sistem. Dolayısıyla mevcut dizinimize göre (yani göreli) bir gösterim kullanmış olacaktık.

