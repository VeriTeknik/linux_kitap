# Debian

Debian kurulum cdsini boot ettikten sonra karşımıza gelen ekranda "Install Debian" seçeneğini tıklıyoruz.

![](../../.gitbook/assets/d1.jpg)

Yükleme esnasındaki dil paketini seçiyoruz.

![](../../.gitbook/assets/d2.jpg)

Konumumuzu bildiriyoruz.

![](../../.gitbook/assets/d3.jpg)

Debianda Türkiye ile uyuşan herhangi bir dil paketi bulunmamakta. Bu yüzden UTF-8 formatındaki United States seçeneğini isaretliyoruz.

![](../../.gitbook/assets/f4.jpg)

Bu ekranda ağ ayarlarını yapabileceğiniz gibi, daha sonradan ayarlamak üzere geçebilirsiniz.

![](../../.gitbook/assets/f5.jpg)

Hostname ayarlamamızı yapıyoruz. Sonraki gelen müteakip adımlarda ise sifremizi giriyoruz. Debian bu asamadan sonra sizden kullanıcı açmanızı isteyecek, bunlar admin,root veya user'dan farklı olmalıdır.

![](../../.gitbook/assets/d6.jpg)

Sonraki aşamada ise diskimizin bölümlerini ayarlıyoruz. Buradaki bölümlemeler

| Mount Point | Boyut           |
| ----------- | --------------- |
| /root       | 1024 MiB        |
| swap        | 8196 MiB        |
| /           | Geri Kalan Alan |

![](../../.gitbook/assets/f8.jpg)

İşaretli olan diski tıklayarak ilerliyoruz. Bu noktadan sonra sırasıyla 1. Boyut ayarlama 2. /root, swap, / bölümleri seçimi

şeklinde ilerlemektedir.

![](../../.gitbook/assets/f12.jpg)

![](../../.gitbook/assets/f9.jpg)

ext4 ve swap seçimleri en üstteki "use as:" bölümünde yapılmaktadır.

![](../../.gitbook/assets/f10.jpg)

En son disk bölümlendirmemiz tamamlanmış bulunmakta. "Finish partitioning and write changes to disk" seçeneği ile yükleme ekranımıza devam ediyoruz.

![](../../.gitbook/assets/f13.jpg)

Yüklemenin ilerleyen bölümlerinde GRUB loader seçenegini kendimize uygun şekilde hazırlıyoruz.

![](../../.gitbook/assets/d14.jpg)

Yükleme ekranı bittikten sonra sistem yeniden başlayacak ve Debian yüklemesi tamamlanacaktır.
