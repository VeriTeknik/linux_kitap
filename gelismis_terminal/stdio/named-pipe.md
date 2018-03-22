## Named Pipe

Bu bölümde programların standart girdi ve çıktılarını birbirlerine veya çeşitli dosyalara yönlendirme işlemlerini detaylarını gördük. Özellikle UNIX pipeline'ı oluşturan bu süreçte, genellikle programları birbirleriyle bağlarken `|` işaretinin nasıl kullanıldığını gördük.

Bu kullanımdaki temel problemler, programlar arasındaki bağlantının tek seferlik yapılması, tek yönlü olması ve tek yazılım tarafından kullanılabilmesi. Örneğin bir programın çıktısının birden fazla program tarafından girdi olarak kullanılmasını sağlamak, veya birden fazla programın girdi sağlayabileceği ortam sağlamak mümkün olmaz.

Bunun için _named pipe_ denilen dosyalar kullanılır. Hatırlarsanız daha önce "Unix üzerinde her şey bir dosyadır" demiştik. Burada da özel bir dosya tipinden bahsediyoruz. Sistem üzerinde öyle bir dosya tanımlanır ki, bu dosyaya birden fazla program yazabilir, ve bu dosyayı birden fazla program okuyabilir. Üstelik bu dosya hiç yer kaplamaz. Aslında daha önce kullandığımız pipe işleminin sadece iki program arasında değil, birden fazla program arasında kullanılmasına olanak sağlar. Bunu yapmak için dosya sistemi üzerinde ilgili pipe'ı isimlendirmemiz gerektiğinden, bunlara named pipe denilir. Böyle olunca da, daha önce kullandığımız pipe işaretlerine _anonymous pipe_ \(anonim pipe\) veya _regular pipe_ \(normal pipe\) denilir.

Named pipe'lar, üzerine yazılan verinin aynı sırayı koruyarak okunmasını sağlarlar. Kısacası ilk yazdığımız veri, ilk okunacak veridir. Bu tip sistemlere _First In First Out_'un kısaltması olan FIFO denilir. Yani işleme alınacak verilerde öncelik, en eski oluşturulan veriye tanınır. Örneğin bunun bir alternatifi, _Last In First Out_'un kısaltması olan LIFO'lardır. Ancak named pipe'lar, FIFO şeklinde çalıştığı için, aşağıdaki komut ile bir named pipe oluşturulur.

```
eaydin@eaydin-vt ~/devel/namedpipe $ mkfifo mario
```

Bu işlem sonucunda aslında `~/devel/namedpipe` dizini içinde `mario` isimli özel bir dosya oluşmuştur. Bu dizinin içeriğine bakacak olursak:

```
eaydin@eaydin-vt ~/devel/namedpipe $ ls -l
total 0
prw-rw-r-- 1 eaydin eaydin 0 Mar 16 15:39 mario
```

Burada `mario`'nun dosya tipinin **p** olduğu görülüyor. Bu, dosyanın pipe olduğunu ifade etmektedir. Gördüğünüz üzere dosyanın boyutu da 0 Byte'dır. Yani disk üzerinde yer kaplamaz.

Eğer aşağıdaki gibi ufak bir script yazarsak, çalıştırıldığında her saniye mevcut tarih/saati standart çıktıya yazdırmasını sağlayabiliriz. Bu scripti telltime.sh olarak kaydettiğimizi düşünelim.

```
eaydin@eaydin-vt ~/devel/namedpipe $ cat telltime.sh 
while true; do
date
sleep 1
done
```

Eğer bu scripti çalıştırıp standart çıktısını `mario`'ya yönlendirirsek, sonra başka bir terminal üzerinden `mario`'yu okursak, her saniye tarih/saati görmeyi bekleriz.

```
eaydin@eaydin-vt ~/devel/namedpipe $ ./telltime.sh > mario
```

```
eaydin@eaydin-vt ~/devel/namedpipe $ cat < mario
Fri Mar 16 16:05:17 +03 2018
Fri Mar 16 16:05:18 +03 2018
Fri Mar 16 16:05:19 +03 2018
Fri Mar 16 16:05:20 +03 2018
Fri Mar 16 16:05:21 +03 2018
Fri Mar 16 16:05:22 +03 2018
Fri Mar 16 16:05:23 +03 2018
Fri Mar 16 16:05:24 +03 2018
Fri Mar 16 16:05:25 +03 2018
^C
```

Tahmin edeceğiniz üzere, mario'yu birden fazla programın okumasını da sağlayabilirdik.

Şimdi bir başka program yazalım, ve bu program da iki saniyede bir VeriTeknik sözcüğünü standart çıktıya yazsın.

```
eaydin@eaydin-vt ~/devel/namedpipe $ cat tellname.sh 
while true; do
echo VeriTeknik
sleep 2
done
```

Biraz önce çalıştırdığımız `./telltime.sh > mario` komutu hala çalışmaya devam ederken, bir de `./tellname.sh > mario` komutunu farklı terminalde çalıştırabiliriz. Böylece `telltime` scripti her saniye `mario`'ya saati yazacak, `tellname` scripti ise her iki saniyede bir `mario`'ya `VeriTeknik` yazacaktır. Sonucu yine farklı bir terminalde `mario`'yu okuyarak gözleyebiliriz.

```
eaydin@eaydin-vt ~/devel/namedpipe $ cat < mario
Fri Mar 16 16:09:21 +03 2018
Fri Mar 16 16:09:22 +03 2018
Fri Mar 16 16:09:23 +03 2018
Fri Mar 16 16:09:24 +03 2018
VeriTeknik
Fri Mar 16 16:09:25 +03 2018
Fri Mar 16 16:09:26 +03 2018
VeriTeknik
Fri Mar 16 16:09:27 +03 2018
Fri Mar 16 16:09:28 +03 2018
VeriTeknik
Fri Mar 16 16:09:29 +03 2018
Fri Mar 16 16:09:30 +03 2018
VeriTeknik
Fri Mar 16 16:09:31 +03 2018
Fri Mar 16 16:09:32 +03 2018
VeriTeknik
Fri Mar 16 16:09:33 +03 2018
Fri Mar 16 16:09:34 +03 2018
Fri Mar 16 16:09:35 +03 2018
^C
```

Özellikle başlarda ve sonlarda 2'den fazla saat satırı görmeniz normal, çünkü `tellname` scripti arada çalıştırıp durdurduk. Yukarıdaki örnekten `tellme` scriptini saat 16:09:24'te çalıştırdığımızı ve 16:09:33 veya 16:09:34'te durdurduğumuzu anlayabiliriz. Yani `mario` pipe'ına aynı anda yeni programlar veri yazıp durdurabilir, ve okuyan taraf sadece sonuçları görerek etkilenir. Dosyayı yeniden açıp kapatmasına bile ihtiyaç duymaz.

Named pipe'lar hakkında GNU/Linux manual sayfalarında önemli bir bilgi yer alır.

> The FIFO must be opened on both ends \(reading and writing\) before data can be passed. Normally, opening the FIFO blocks until the other end is opened also.

Yani bir named pipe oluşturduğumuzda, eğer aynı anda okuyan ve yazan birileri varsa veri akışı sağlanır. Yoksa bir taraftan sürekli yazsanız bile, bunu okuyan birileri olmadığı sürece o veri kaybolur. Tıpkı `/dev/null` dosyasına yazmışsınız gibi veri hiçbir yere kaydedilmez.

Named pipe'lar ile yapılan işlemlerin dosya sisteminde inode oluşturmak dışında herhangi bir yük oluşturmadığını hatırlatmakta fayda var. Birden fazla programın birbiriyle iletişim kurmasını sağlayan bu mekanizma tamamen çekirdek \(kernel\) tarafından idare edilir.

Oluşturduğunuz bir named pipe'ı rm komutuyla kolayca silebilirsiniz.

```
eaydin@eaydin-vt ~/devel/namedpipe $ rm mario
```



