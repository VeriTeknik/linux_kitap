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
| echo | Standart çıktıya yazdırma | echo "test |
| du | Dizin boyutu gösterme | du -sh . |

Yukarıda sıralanan komutlar, Linux komut satırının çok küçük bir kısmını oluştursa da, en sık kullanılan komutlar arasında yer aldıklarını söyleyebiliriz. Burada altının çizilmesi gereken önemli bir nokta, Linux komutlarının birbirleriyle uyum içinde çalışabilecek biçimde tasarlanmışlardır ve bunu yapmak için standart girdi/çıktı yönlendirmeleri haricinde, pek çok parametre alabilmektedirler. Programların parametreleri ve programların genel özellikleri hakkında bilgi almak için man komutu kullanılabilir. Örneğin du komutu hakkında bilgi almak için
```bash
man du
```
yazmak yeterli olacaktır. Bu durumda terminalde du komutunun manual dosyaları görülür. Bu dosyalar artık standartlaştığı için, internetten de bulunabilir. Örneğin du için manual dosyasına şu adresten erişebilirsiniz: http://man7.org/linux/man-pages/man1/du.1.html

