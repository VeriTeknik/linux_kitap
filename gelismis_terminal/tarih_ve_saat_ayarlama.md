# Tarih ve Saat Ayarlama

##Sistem Saati

Linux üzerinde tarih ve saat ayarlama işlemleri **date** komutu ile gerçekleştirilir. Komutu tek başına yazdığınızda tarih ve saati çıktı olarak verir.

```bash
date
Sun Nov 15 19:22:23 EET 2015
```

date komutu ile istediğimiz biçimde çıktı alabiliriz.

```bash
date +%d_%m_%Y
15_11_2015
```

Daha kompleks işlemler için boşluklu parametreleri çift tırnak içinde birleştirip tek parametre olarak programa sunmak gerekir.

```bash
date +"Ay:%m / Yıl:%y"
Ay:11 / Yıl:15
```

Saati ve tarihi ayarlamak içinse aşağıdaki gibi bir yol izlenebilir.

```bash
date -s "2 NOV 2014 17:45:12"
Sun Nov 2 17:45:12 EET 2014
```

Sadece saati değiştirmek için,

```bash
date -s "15:12:13"
Sun Nov 2 15:12:13 EET 2014
```

Veya dilediğiniz biçimde tarih belirtmek için,


```bash
date +%Y%m%d%T -s "20120423 09:15:12"
```
## Fiziksel Saat

Sistem üzerine yazılan saatler, bilgisayarın pil ile tutulan fiziksel saatine doğrudan kaydedilmez. Fiziksel saatteki bilgiye erişmek için **hwclock** komutu kullanılmalıdır.

Fiziksel saatteki bilgiyi okumak için

```bash
hwclock -r
Sun 15 Nov 2015 07:37:57 PM EET  -0.960295 seconds
```

Yukarıdaki çıktıda -0.960295 saniye'lik bir fark görünüyor. Bu komutu verdiğimiz an ile, cihazdan okuduğumuz saat arasındaki fark. Kısacası cihaz bize neredeyse 1 saniye içerisinde cevap vermiş. Bu süre zarfında hwclock komutunun yaptıklarını daha detaylı görmek için **-D** (debug) parametresiyle çalıştırabilirsiniz.

```bash
hwclock -D
hwclock from util-linux 2.20.1
Using /dev interface to clock.
Last drift adjustment done at 1435275720 seconds after 1969
Last calibration done at 1435275720 seconds after 1969
Hardware clock is on UTC time
Assuming hardware clock is kept in UTC time.
Waiting for clock tick...
...got clock tick
Time read from Hardware Clock: 2015/11/15 17:39:39
Hw clock time : 2015/11/15 17:39:39 = 1447609179 seconds since 1969
Sun 15 Nov 2015 07:39:39 PM EET  -0.969248 seconds
```

Fiziksel saatteki değeri okuyup, sistem saatinizi bununla güncellemek isteyebilirsiniz. Aslında bilgisayarlarınızın açılışta yaptığı bir işlemdir bu. Böylelikle sisteminize hiç enerji verilmediği durumlarda saat pili ile sistem geçen zamanı tutmaya devam eder, sisteminiz tekrar açıldığında bu cihazdan (RTC) saati okur ve kendi tarihini bu değerle günceller.

```bash
hwclock -s
```

Öte yandan, **date** komutu ile sistem saatini güncellemeyi öğrenmiştik. Bu komut ile sistem saatinizi güncelledikten sonra fiziksel saatin değerinin değişmemesi normal. Eğer fiziksel saatin değerini de sistem saatinizdekine ayarlamak isterseniz, bu bilgiyi RTC modülüne yazmanız gerekir.

```bash
hwclock -w
```