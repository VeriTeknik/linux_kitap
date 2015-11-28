# Imagemagick ve Görüntü Dosyaları

GNU/Linux komut satırında birden görüntü dosyası üzerinde işlem yapmak için en uygun paketlerden birisi **imagemagick** paketidir. İçerisinde temel işlemleriniz için pek çok araç barındırır.

Örneğin dosya uzantılarından dosya tiplerini anlayacağı için, doğrudan png->jpg dönüşümü gibi işlemler yapabilirsiniz. Ayrıca kalite belirterek dosyanın kayıplı sıkıştırma miktarını belirleyebilirsiniz.

```bash
convert logo.png logo.jpg
convert logo.png -quality 70 logo.jpg
```

