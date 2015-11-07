# nano ve pico

nano ve pico programları aslında birbirlerinin aynısıdır. Pico (Pine composer) geliştirildiğinde bir *free software license* altında dağıtılmadığından, kodun değiştirilerek dağıtılması yasal değildi. Dolayısıyla GNU Projesi kapsamında pico'nun işlemlerini taklit eden bir program geliştirildi ve ismi **nano** oldu. Bugün **nano** orijinal pico'dan daha kapsamlı duruma gelmiştir. Kullandığınız Linux dağıtımlarında pico komutu da aslında nano'yu çalıştırmaktadır.

Aslında pico için özgür lisanslı bir yazılım amacıyla Chris Allegretta tarafından nano ile geliştirildiğinde ismi TIP konuldu *(This isn't Pico)*, ancak Unix üzerindeki *tip* yazılımı ile karışabileceği gerekçesiyle ismi daha sonra *nano* olarak değiştirildi. *nano* SI birim sisteminde *pico*dan 1000 kat daha büyük olan bir uzunluk birimidir.

nano ve pico farklarının yaratacağı kafa karışıklığını giderdiysek, programın genel kullanımına bakabiliriz.

Önceki bölümde crc8.py dosyamızın yetkilerini görüntülemiştik. Şimdi dosyanın içeriğine göz atalım. Dosyaya GNU/Linux üzerinde cat, less, more, head gibi komutlarla göz atabiliriz. Ancak dosyanın içeriğini değiştirmek için bu yöntemler pratik olmayacaktır. Dosyanın bulunduğu dizinde aşağıdaki komutlardan birini çalıştırarak dosyayı nano ile açabiliriz.

```bash
nano crc8.py
pico crc8.py
```

![](nano.png)

Burada dosyamızın içeriğini görüyoruz. Kullandığımız dağıtımda nano dosyanın uzantısına bakarak dosyamızın içeriğinin bir Python kodu olduğunu anlıyor ve gerekli renklendirmeyi yapıyor.

Alt iki satırda gördüğünüz komutlar, klavye kısayollarıyla erişiliyor. Komutlara erişmek için CTRL ile birlikte ilgili kısayolu kullanmak gerekiyor. Örneğin "Get Help" fonksiyonunu çağırmak için CTRL+g tuşlarına basmak gerekiyor. Gelen menüden nano için kullanılabilecek bütün komutları görebilirsiniz.

nano içerisinde gezinmek için ok tuşlarını kullanmanız yeterli. Değiştirmek/eklemek/silmek istediğiniz noktaya imleci getirdiğinizde istediğiniz değişikliği yaptıktan sonra dosyayı kaydetmek için CTRL+o programdan çıkmak içinse CTRL+x tuşlarını kullanmanız yeterlidir.



