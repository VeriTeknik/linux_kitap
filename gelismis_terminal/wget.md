# wget

Bu kitapçıkta daha önce de karşılaştığımız komutlardan **wget**, internetten dosya çekmeye yarayan, **Web GET** sözcüklerinin kısaltması olan bir program.

İnternetten herhangi bir dosyayı indirmek için dosya adresini wget'e parametre olarak vermek yeterlidir.

```bash
wget http://www.plugged.in/downloads/plugged.sh
```

Yukarıdaki komutla bulunduğumuz dizine ```plugged.sh``` dosyası inecektir.

Bazen linkler istediğimiz isim yapısında olmayabilir. İndirilen dosyayı farklı isimle kaydetmek için aşağıdaki yöntem izlenebilir.

```bash
wget wget http://www.plugged.in/downloads/plugged.sh -O install.sh
```

Artık dosya ```plugged.sh``` ismiyle değil, ```install.sh``` ismiyle kaydedilecektir.

