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

## Yarım Kalan İndirmeler

Normalde wget programı, indirilen dosya hali hazırda dizinde mevcutsa, yeni indirilen dosayı sonuna **.1 .2** gibi rakamlar koyarak yazar. Örneğin yukarıdaki örnekte **plugged.sh** dosyası zaten dizinde mevcutsa, yeniden indirdiğimizde wget dosyayı **plugged.sh.1** olarak kaydeder.

Oysa indirme sırasında dosya yarım kalmışsa, kaldığı yerden devam ettirmek için **-c** parametresini vermemiz yeterlidir.

```bash
wget -c http://www.plugged.in/downloads/plugged.sh
```

## Hız Limitleme

İndirme hızını limitlemek için ```--limit-rate``` parametresi kullanılır.

```bash
wget --limit-rate=400k http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
```

## İndirme İşlemini Arka Planda Gerçekleştirme

Büyük dosyaları indirme işlemini arka planda yürütebilirsiniz. Böyle bir durumda wget size programın PID numarasını verecek, ve logları yazdığı dosyayı bildirecekir. Bu sayede isterseniz indirme işlemini log dosyasından takip edebilir, dilediğiniz zaman PID numarasını kullanarak programı durdurabilirsiniz.

```bash
wget -b http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
Continuing in background, pid 25965.
Output will be written to ‘wget-log’.
```