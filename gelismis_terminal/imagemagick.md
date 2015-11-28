# Imagemagick ve Görüntü Dosyaları

GNU/Linux komut satırında birden görüntü dosyası üzerinde işlem yapmak için en uygun paketlerden birisi **imagemagick** paketidir. İçerisinde temel işlemleriniz için pek çok araç barındırır.

## Tip Dönüşümü

Örneğin dosya uzantılarından dosya tiplerini anlayacağı için, doğrudan png->jpg dönüşümü gibi işlemler yapabilirsiniz. Ayrıca kalite belirterek dosyanın kayıplı sıkıştırma miktarını belirleyebilirsiniz.

```bash
convert logo.png logo.jpg
convert logo.png -quality 70 logo.jpg
```

## Boyut Dönüşümü

Imagemagick ile dosya boyutlarını değiştirmek kolaydır.

```bash
convert logo.jpg -resize 300x120 logo.jpg
```

Yukarıdaki komut, görüntü oranını (aspect ratio) korur. Yani görüntüyü 300x120'lik bir dikdörtgen içerisine sığdırmaya çalışır. Eğer korumasını istemiyorsanız ve oran bozulsa bile tam olarak 300x120'lik çıktı elde etmek istiyorsanız bunu ünlem (!) koyarak belirtebilirsiniz.

```bash
convert logo.jpg -resize 300x120! logo.jpg
```

Tabii oran korunarak boyutlandırma için sadece genişlik (width) veya yükseklik (height) belirtebilirdik.

```bash
convert logo.jpg -resize 300 logo.jpg
convert logo.jpg -resize x120 logo.jpg
```

Ayrıca yüzde belirterek küçültme işlemleri de yapılabilir.

```bash
convert logo.jpg -resize 50% logo.jpg
```

### Sadece Küçült
Imagemagick ile sadece belirli boyuttan büyük olanları küçültmek için kullanılabilecek bir işaretçi var, büyüktür **>** işareti. Alışıla geldiğin tersinde bir gösterim ancak bunu şu şekilde okumak gerekir: "Sadece bu boyuttan **büyük** olan dosyalarda çalış".

```bash
convert logo.jpg -resize 128x128\> logo_thmb.jpg
```

### Sadece Büyüt
Yukarıdaki işlemin tam tersidir. Eğer dosya belirtilen limitlerden küçükse çalışır.

```bash
convert logo.jpg -resize 128x128\< logo_large.jpg
```

## Döndürme İşlemleri

Imagemagick ile görüntüyü döndürmek tahmin edileceği gibi **rotate** ile yapılır.

```bash
convert logo.jpg -rotate 90 logo.jpg
```
![Orijinal Görüntü](images/1604.png) ![Döndürülen görüntü](images/rotated90.png)


## Aynalama İşlemleri

Öte yandan aynalama işlemleri için **flip** ve **flop** seçenekleri kullanılır. **flip** görüntüyü başaşağı çevirir (x-ekseninde aynalama), **flop** ise soldan sağa doğru (y-ekseninde) çevirir.

```bash
eaydin@dixon ~/calisma/im $ convert 1604.png -flip flipped.png
eaydin@dixon ~/calisma/im $ convert 1604.png -flop flopped.png
eaydin@dixon ~/calisma/im $ convert 1604.png -flip -flop flipflop.png
```

![Orijinal görüntü](images/1604.png) ![Flip edilmiş görüntü](images/flipped.png) ![Flop edilmiş görüntü](images/flopped.png) ![Hem Flip hem de Flop edilmiş görüntü](images/flipflop.png)