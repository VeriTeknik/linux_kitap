# Debian Yükleme Rehberi

Debian kurulum cdsini boot ettikten sonra karşımıza gelen ekranda "Install Debian" seçeneğini tıklıyoruz.

![](d1.jpg)

Yükleme esnasındaki dil paketini seçiyoruz.

![](d2.jpg)

Konumumuzu bildiriyoruz.

![](d3.jpg)

Debianda Türkiye ile uyuşan herhangi bir dil paketi bulunmamakta. Bu yüzden UTF-8 formatındaki United States seçeneğini isaretliyoruz.

![](f4.jpg)

Bu ekranda ağ ayarlarını yapabileceğiniz gibi, daha sonradan ayarlamak üzere geçebilirsiniz.

![](f5.jpg)

Hostname ayarlamamızı yapıyoruz. Sonraki gelen müteakip adımlarda ise sifremizi giriyoruz. Debian bu asamadan sonra sizden kullanıcı açmanızı isteyecek, bunlar admin,root veya user'dan farklı olmalıdır.

![](d6.jpg)

Sonraki aşamada ise diskimizin bölümlerini ayarlıyoruz. Buradaki bölümlemeler

|Mount Point | Boyut |
|--|--|
|/root|1024 MiB|
|swap |8196 MiB|
|/  |Geri Kalan Alan|

![](f8.jpg)

İşaretli olan diski tıklayarak ilerliyoruz. Bu noktadan sonra sırasıyla
1. 
Boyut ayarlama
2. 
/root, swap, / bölümleri seçimi

şeklinde ilerlemektedir.

![](f12.jpg)
![](f9.jpg)

ext4 ve swap seçimleri en üstteki "use as:" bölümünde yapılmaktadır.

![](f10.jpg)

En son disk bölümlendirmemiz tamamlanmış bulunmakta. "Finish partitioning and write changes to disk" seçeneği ile yükleme ekranımıza devam ediyoruz.

![](f13.jpg)

Yüklemenin ilerleyen bölümlerinde GRUB loader seçenegini kendimize uygun şekilde hazırlıyoruz.

![](d14.jpg)

Yükleme tamamlanmıştır.



