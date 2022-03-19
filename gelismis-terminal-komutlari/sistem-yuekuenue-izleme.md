# Sistem Yükünü İzleme

## top

Linux üzerinde sistem kaynaklarınızı tüketen yazılımları izlemek için kullanılan en pratik programlardan birisi **top** programıdır. Program doğrudan CPU kullanımına göre sıralandırılmış sonuçlar döndürür.

![](../.gitbook/assets/top.png)

_top ekranı_

Sisteminizdeki bütün CPU'ların işlem miktarını görmek için **1** tuşuna basılabilir.

![](../.gitbook/assets/top2.png)

_Bütün CPU'ları gösteren top ekranı_

CPU kullanımı incelenirken dikkat edilmesi gereken nokta, toplam değeri %100'ün üzerinde olmasının mümkün olduğudur Örneğin yukarıdaki örnekte kullandığımız sistemin 4 tane CPU'su bulunduğundan, toplam değer %400 olabilir. Bazı durumlarda bir CPU'nun %100'ün üzerinde değer vermesi mümkündür, bu _top_ programının işlemci kullanımını tespit ederkenki hassasiyetiyle ilgilidir.

**h** tuşuna basılarak programın yardım ekranına erişilebilir.

![](../.gitbook/assets/top3.png)

Yukarıdaki yardım ekranından görülebileceği gibi, top ekranındaki değerleri sıralamak için **f** tuşu kullanılabilirmiş.

![](../.gitbook/assets/top-sort.png)

_Hangi parametreye göre sıralanacağını gösteren ekran._

Yukarıdaki ekrandan **memory** seçerek **top** programının bellek tüketimine göre programları sıralaması sağlanabilir. Ardından **q** ile çıktığımızda ekranımız aşağıdakine benzer olacaktır.

![](../.gitbook/assets/top-memory.png)

_Yukarıdaki örnekte python3 belleğin büyük çoğunluğun tüketirken görülüyor._

**top** programının renklendirilmiş ve daha kolay kullanılır hale getirilmiş bir versiyonu **htop** bazı sistemlerde bulunabilir. Daha kolay kullanım ve daha alışıldık bir görünüm sağlar.

![](../.gitbook/assets/htop.png)

_htop ekran görüntüsü_

## free

Sistemin genel bellek tüketimini görmek için **free** komutu kullanılabilir.

```bash
free
             total       used       free     shared    buffers     cached
Mem:       7947480    7095948     851532     482716       6864     781848
-/+ buffers/cache:    6307236    1640244
Swap:      7193596        276    7193320
```

## atop

Sistem kaynaklarının tümünü görmek istediğinizde en iyi seçeneklerden birisi atop'tır. Tüm donanımı ve prosesleri aynı pencerede görüntüleyebilen uygulama, yavaşlamaya neden olan donanım ya da yazılımın kolayca bulunabilmesini sağlamaktadır. Yapı olarak top'a çok benzer ancak daha detaylıdır ve sorunlu kaynakları daha parlak renkte gösterir.

![](../.gitbook/assets/atop.jpg)

## kill

**top** kullanılırken işlemleri öldürmek mümkündür. Ekranda **k** tuşuna bastığınızda, doğrudan en üstteki işlem seçilecektir. Bunun dışında bir işlem öldürülmek istenirse, ilgili işlemin PID'si yazılabilir. Ardından hangi sinyal ile öldürüleceği belirtilmelidir. Genel kanı **15** sinyali ile öldürmektir.

![](../.gitbook/assets/top-pid.png)

Aslında top programı arka planda aşağıdaki komutu çalıştırır.

```bash
kill -15 6392
```

**15** numaralı sinyal, aslında programa **TERMINATION** sinyalini gönderir. Doğru yazılmış programlar bu sinyali işleyip kapanmaya çalışırlar. Örneğin açık dosyalarını kapatır, gerekli işlemlerini tamamlar vs. Eğer bir program bu sinyal ile ölmüyorsa, muhtemelen işlemleri arasında bir problem yaşamış demektir. Bu programları sonlandırmak için **KILL** sinyalini **9** ile göndermek gerekir. Bu sinyal her ne olursa olsun programın sonlandırılmasını sağlayacaktır.

Sistem üzerinde pek çok sinyal bulunur. Sinyallerin bir listesini görmek için `kill -l` komutu kullanılabilir. Örneğin **2** numaralı sinyal, **INTERRUPT** sinyalidir, kısacası bir program çalışırken klavyenizle **CTRL+C** göndermekle aynı işi yapar. Bazı durumlarda bu sinyali göndermek, veya programlarınız test etmek için yararlı olabilir.

Bir diğer örnek Hang Up (HUP) sinyalidir. **1** numaralı sinyal olan HUP, pek çok _daemon_ tarafından _restart_ olarak işlenir. Örneğin _apache_ bu sinyali alıp işleyen servislerden birisidir. Apache'nin PID'sine _HUP_ sinyali gönderilirse, Apache kendisini yeniden başlatır.

## uptime

Sistem hakkında bilgi edinmeyi sağlayan bir diğer komut **uptime** komutudur. Sistemin ne kadar zamandır çalıştığını, kaç kişinin (terminalin) açık olduğunu, son birkaç dakikanın işlemci yükünün ortalamasını gösterir.

Sistemin ne zaman başlatıldığını görmek için `who -b` komutu da oldukça faydalıdır.

Öte yandan sisteme bağlı terminallerin bir listesini elde etmek için `w` programı kullanılabilir.

## ps

**ps** programı (_process status_) sistem üzerinde çalışan yazılımları, işlemleri tespit etmek için kullanılır. Yaygınlıkla kullanılan parametreler `ps aux` ve `ps -ef` şeklindedir. Bu parametreler ile işlemleri listeleyip, farklarına bakabilirsiniz. İşlemler listesinin başını görmek için çıktınızı `head` ile sınırlandırın, veya `more` ile sayfalara bölün.

İşlemlerin hiyerarşilerini görebilmek için `ps axjf` kullanılabilir. Böylece hangi işlemin, hangisinin **parent**ı olduğu görülebilir. GNU/Linux üzerinde her zaman 1 numaralı **PID**'ye (Process ID) sahip işlem _init\_tir (sebebinin detaylarını ilerleyen bölümlerde göreceğiz). Bütün işlemler bu program tarafından **spawn** edilir. GNU/Linux'ta bir işlemin **parent** işlemini öldürdüğünüzde, **child** işlemleri de ölecektir. Dolayısıyla örneğin bir şekilde **1** numaralı işlemimiz ölürse, bütün sistem duracaktır. Bu durumun önüne geçebilmek için \_init_ işlemi sistem tarafından korunur. _init_ kendisine hangi sinyallerin gönderilebileceğine karar verir. Bu, yazılan Linux çekirdeklerine göre değişiklik gösterebilir, ancak hemen hepsi **kill** sinyaline izin vermez.

## nice

Linux üzerinde işlemlerin önceliği, onların ne kadar _nice_ (iyi) olduklarıyla ifade edilir. Bir işlemin _nice_ değeri ne kadar düşükse, o kadar "az iyi" olacağından, o kadar işlemci önceliği vardır, yani daha fazla sistem kaynağı tüketen işlemlerin _nice_ değeri düşüktür. Önceliği düşük olan işlemleri daha \_iyi\_dir çünkü daha az sistem kaynağı tüketirler.

**top** komutu çıktısındaki **NI** sütunu, ilgili işlemin _nice_ değerine karşılık gelir.

_nice_ değeri -19/-20 ile 19/20 arasında değişebilir. Üst ve alt limitlerin değeri sistemlere göre farklılık gösterir.

Bir programı belirli bir _nice_ değeri ile çalıştırmak istersek, programı çalıştırma esnasında bu değeri belirtmek gerekir.

```bash
nice -n 12 crc8
```

Öte yandan, mevcut bir programın _nice_ değerini değiştirmek istersek, programın **PID** değerini parametre olarak vermek gerekir.

```bash
renice -3 5486
```

Komutların mevcut nice değerini **ps** ile öğrenmek için, ps çıktısından bunu talep edebiliriz.

```bash
ps ax -o pid,ni,cmd
```

**PROBLEM:** Sistem üzerinde 20 dakikadan daha uzun süredir çalışan belirli bir işlemi bulun. **ps** komutunun uygun parametreleriyle elde edilebilir. Bu işlemi öldürün.

Yukarıdaki problemi çözen bir Python yazılımı [https://github.com/eaydin/timebomb](https://github.com/eaydin/timebomb) adresinde görülebilir. Bu yazılım standart GNU/Linux komutlarını kullanmaktadır.
