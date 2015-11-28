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
convert logo.jpg 300x120 logo.jpg
```

Yukarıdaki komut, görüntü oranını (aspect ratio) korur. Yani görüntüyü 300x120'lik bir dikdörtgen içerisine sığdırmaya çalışır. Eğer korumasını istemiyorsanız ve oran bozulsa bile tam olarak 300x120'lik çıktı elde etmek istiyorsanız bunu ünlem (!) koyarak belirtebilirsiniz.

```bash
convert logo.jpg 300x120! logo.jpg
```

Tabii oran korunarak boyutlandırma için sadece genişlik (width) veya yükseklik (height) belirtebilirdik.

```bash
convert logo.jpg 300 logo.jpg
convert logo.jpg x120 logo.jpg
```