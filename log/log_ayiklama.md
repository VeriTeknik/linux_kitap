# Regular Expressions Kullanarak Log Ayıklama Egzersizleri

Log dosyalarının UNIX sistemler üzerinde en verimli şekilde kullanmanın yolu, log dosyası oluşurken anlık olarak takip etmektir. Bu işlemi yaparken "tail" komutu kullanılur. tail argüman verilmeden çalıştırıldığında dosyanın en son satırlarını gösterir, ancak "-f" argümanı ile (çoğu işletim sisteminde **tailf** şeklinde alias olarak bulunmaktadır) dosya oluşurken takip etmek mümkündür. Tipik kullanımı şu şekildedir:

```bash
tailf /var/log/messages
```

Yukarıdaki komutu girdikten sonra, en son nerede kaldığınızı hatırlamak için bir kaç kez enter tuşuna basıp satırlar arasında ayırt edici bir boşluk bırakabilirsiniz. Siz enter'a bassanız dahi yeni log oluştuğunda boşluklardan sonra log akmaya devam edecektir.


