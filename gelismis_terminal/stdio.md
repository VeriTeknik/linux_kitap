# Standart Girdi ve Çıktı Yönlendirme

GNU/Linux ve benzeri POSIX sistemlerin en güçlü olduğu yönlerden birisi, programların standart girdi ve çıktılarının yönlendirilmesidir. Özellikle birden fazla programın işlemlerini birbirlerine kolayca gönderebilme gücü sağlayan bu yöntem, aslında UNIX'in oluşturulduğu dönemdeki temel felsefeleri en iyi yansıtan özelliklerinden biridir.

1978'de yayımlanan The Bell System Technical Journal'ın UNIX Time-Sharing System dokümanının önsözünde Douglas McIlroy, UNIX'in tasarlanması ve kullanılmasında temel alınan kritik noktaları 4 madde halinde listeler. Bu maddelerden ilki ilerleyen yıllarda çok popüler olmuştur. Maddenin tam hali şu şekildedir:

> Make each program do one thing well. To do a new job, build afresh rather than complicate old programs by adding new "features".

Bu maddede McIlroy, bir program yazılırken, bu programın _tek_ _bir şeyi iyi yapması gerektiğini_** **vurgular. Yeni bir iş yapılacağı zaman, mevcut programa yeni "özellikler" eklemek yerine, yeni bir program yazılmasının daha doğru olduğunu savunur.

Önsözde paylaşılan maddelerden ikincisinin tam hali ise şu şekildedir:

> Expect the output of every program to become the input to another, as yet unknown, program. Don't clutter output with extraneous information. Avoid stringently columnar or binary input formats. Don't insist on interactive input.

Bu madde başlı başına çok fazla şey söyler, ve bir süre GNU/Linux sistemler üzerinde yazılmış programları kullanan herkes, aslında bu maddelerin çoğu programda uygulandığını görecektir. Öncelikle bu bölümde bahsedeceğimiz girdi ve çıktıların yönlendirilmesi gerekliliği, maddenin ilk cümlesinde ifade edilmiştir. McIlroy, her programın çıktısının aynı zamanda girdi olarak tasarlanması gerektiğini, hatta ne iş yaptığını bilmediği bir programın girdisi olarak kullanılabileceğinin gözönünde bulundurulması gerektiğini belirtir. Çıktıların gereksiz bilgilerle kirletilmemesi gerektiğini, kolon bazlı girdilerin veya binary girdilerin dayatılmasından kaçınılması gerektiğini ifade eder. Ayrıca, gerekmediğinde _etkileşimli girdiden_ kaçınılması gerektiğini belirtir. Çünkü etkileşimli girdi, bir programa standart girdi olarak veri göndermenin zorlaşması demektir.

Eğer girdi ve çıktı yönlendirmeleri ile ilgili hiç tecrübeniz yoksa, yukarıdaki satırlar size soyut gelebilir. Bu bölümü okuduktan sonra yukarıdaki anlayışın ne ifade ettiği biraz daha netleşecektir. Ayrıca kitabın ilerleyen bölümlerini okuduktan sonra tekrar dönüp bu felsefeyi okumanızda fayda olabilir.

## Standart Çıktı

Eğer bu _standart çıktı_ söylemi sizin için hiçbir şey ifade etmiyorsa, önce _standart_ sözcüğünü ortadan kaldıralım: Bir programın _çıktısı_ ne demek?

Terminal üzerinden herhangi bir komut çalıştırdığınızda, aslında o program bir _şey_ üretecektir. Genellikle bu ürettiği _şeyi_ ekrana yazdığını görürüz. Örneğin `ls` komutu, bulunduğumuz dizindeki dosyaları gösterir.

```
eaydin@eaydin-vt ~/devel/pgpoolwatch $ ls
args.txt.sample    LICENSE      poolstatus.py  repmgrwatch.py  sendmail.py  test
config.ini.sample  pgpwatch.py  README.md      scripts         services
```

Burada ls komutu, aslında `LICENSE`, `pgpwatch.py`, `scripts` gibi dosya ve dizin isimlerini bize çıktı olarak sunmuştur. Örneğin `ls -l` yazsaydık, farklı bir çıktı sunacaktı.

```
eaydin@eaydin-vt ~/devel/pgpoolwatch $ ls -l 
total 92
-rw-rw-r-- 1 eaydin eaydin   203 Jan  8 14:30 args.txt.sample
-rw-rw-r-- 1 eaydin eaydin   401 Jan  8 15:51 config.ini.sample
-rw-r--r-- 1 eaydin eaydin  1070 Dec 18 16:32 LICENSE
-rwxrwxr-x 1 eaydin eaydin 12908 Jan  8 14:30 pgpwatch.py
-rwxrwxr-x 1 eaydin eaydin 15274 Jan  8 14:30 poolstatus.py
-rw-rw-r-- 1 eaydin eaydin 13491 Jan  8 14:30 README.md
-rwxrwxr-x 1 eaydin eaydin 12184 Jan  8 14:30 repmgrwatch.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 scripts
-rwxrwxr-x 1 eaydin eaydin  5955 Jan  8 14:30 sendmail.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 16:04 services
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 test
```

Her iki durumda da `ls` programına, çıktıyı nereye yazacağını söylemiyoruz, bu yüzden `ls`, _standart olan çıktıya_ yazıyor. Bu durumda da bizim terminal ekranımız.

UNIX sistemlerde, bu çıktıyı "standart olmaktan çıkarma" amacıyla, standart çıktıyı farklı bir yere yönlendirmenin faydalı olacağı görülmüştür. Örneğin yukarıdaki `ls -l` sonucunu, standart çıktı yerine bir dosyaya yönlendirebiliriz.

```
eaydin@eaydin-vt ~/devel/pgpoolwatch $ ls -l > /home/eaydin/ls_cikti
eaydin@eaydin-vt ~/devel/pgpoolwatch $
```

Farkındaysanız, artık ekranda `ls -l` komutunun sonucunu görmüyoruz, çünkü programın standart çıktısını `/home/eaydin/ls_cikti` dosyasına yönlendirdik. Böyle olunca standart çıktısı olan terminalimize veri yazmadı.Eğer gidip `/home/eaydin/ls_cikti` dosyasının içeriğini okursak, biraz önceki sonucun aynısını görürüz.

```
eaydin@eaydin-vt ~/devel/pgpoolwatch $ cat /home/eaydin/ls_cikti 
total 92
-rw-rw-r-- 1 eaydin eaydin   203 Jan  8 14:30 args.txt.sample
-rw-rw-r-- 1 eaydin eaydin   401 Jan  8 15:51 config.ini.sample
-rw-r--r-- 1 eaydin eaydin  1070 Dec 18 16:32 LICENSE
-rwxrwxr-x 1 eaydin eaydin 12908 Jan  8 14:30 pgpwatch.py
-rwxrwxr-x 1 eaydin eaydin 15274 Jan  8 14:30 poolstatus.py
-rw-rw-r-- 1 eaydin eaydin 13491 Jan  8 14:30 README.md
-rwxrwxr-x 1 eaydin eaydin 12184 Jan  8 14:30 repmgrwatch.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 scripts
-rwxrwxr-x 1 eaydin eaydin  5955 Jan  8 14:30 sendmail.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 16:04 services
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 test
```

Öyleyse, standart çıktı yönlendirme işlemini **&gt;** işaretiyle gerçekleştiriyoruz. Böylece standart çıktıya sonuç döndüren programların tamamının çıktılarını bir dosyaya yönlendirebiliriz.

Standart çıktı yönlendirmek için kullandığımız **&gt;** işareti, hedef dosya bulunmazsa oluşturur. Eğer hedef dosya bulunuyorsa, dosyanın içeriğini tamamen siler ve yeni sonucu yazar. Eğer hedef dosyanın içeriğinin silinmesini istemiyorsak, ancak yeni sonucun dosyanın _sonuna eklenmesini_ istiyorsak, bunun için **&gt;&gt;** işaretini kullanmamız gerekir.

Biraz önceki örneğimizde kullandığımız çıktı dosyasının sonuna, farklı bir dizinin çıktısını da eklemek isteseydik, şöyle bir işlem yapabilirdik.

```
eaydin@eaydin-vt ~/devel $ cd diskalert/
eaydin@eaydin-vt ~/devel/diskalert $ ls -l >> /home/eaydin/ls_cikti
eaydin@eaydin-vt ~/devel/diskalert $ cat /home/eaydin/ls_cikti
total 92
-rw-rw-r-- 1 eaydin eaydin   203 Jan  8 14:30 args.txt.sample
-rw-rw-r-- 1 eaydin eaydin   401 Jan  8 15:51 config.ini.sample
-rw-r--r-- 1 eaydin eaydin  1070 Dec 18 16:32 LICENSE
-rwxrwxr-x 1 eaydin eaydin 12908 Jan  8 14:30 pgpwatch.py
-rwxrwxr-x 1 eaydin eaydin 15274 Jan  8 14:30 poolstatus.py
-rw-rw-r-- 1 eaydin eaydin 13491 Jan  8 14:30 README.md
-rwxrwxr-x 1 eaydin eaydin 12184 Jan  8 14:30 repmgrwatch.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 scripts
-rwxrwxr-x 1 eaydin eaydin  5955 Jan  8 14:30 sendmail.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 16:04 services
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 test
total 32
drwxr-xr-x 4 eaydin eaydin 4096 Dec 18 16:32 build
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 DiskAlert
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 DiskAlert.egg-info
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 dist
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 etc
-rw-r--r-- 1 eaydin eaydin 1070 Dec 18 16:32 LICENSE
-rw-r--r-- 1 eaydin eaydin  593 Dec 18 16:32 README.md
-rw-r--r-- 1 eaydin eaydin 1465 Dec 18 16:32 setup.py
```

Yukarıdaki örnekte, önce `/home/eaydin/devel/diskalert` dizinine gidiyoruz. Daha sonra `ls -l` komutunu çalıştırıp, çıktısını `/home/eaydin/ls_cikti` dosyasına yönlendiriyoruz, ancak standart çıktının önceki dosyayı silmeden, mevcut verileri koruyup, yeni veriyi dosyanın sonuna eklemesi için **&gt;&gt;** işareti ile çıktı yönlendirmesi yapıyoruz. Sonra `/home/eaydin/ls_cikti` dosyasının içeriği `cat` ile okuyoruz \(aslında `cat` ile dosyanın içeriğini _standart çıktıya yazdırıyoruz_\) ve hem `pgpoolwatch`, hem de `diskalert` dizinlerinin içeriğini görüyoruz.

Modern GNU/Linux sistemlerinde, eğer özel bir durum söz konusu değilse, genellikle standart çıktı kullanıcının ekranıdır. Yani programlar standart çıktı olarak monitörünüzü kullanır. Teknik detaylarına girdiğimizde aslında "kullandığınız terminal standart çıktıdır" demek daha doğru olabilir ancak şu aşamada bunu düşünmenize gerek yok. Bir GNU/Linux sistemini istersek öyle ayarlayabiliriz ki, standart çıktısı terminal/monitör olmak yerine örneğin yazıcı \(_printer_\) olabilir. Örneğin dijital ekranı olmayan bir ödeme cihazımız \(POS cihazı\) için böyle bir durum söz konusu olabilir.

## Standart Girdi

Unix dünyasında, programların standart çıktıları gibi, aslında standart girdileri de vardır. Biraz önceki örneğimizde `cat` programı ile `ls_cikti` dosyasının içeriğini görüntülerken, okunacak veriyi standart girdi olarak sunmayıp, aslında parametre olarak gönderdik. Yani `cat /home/eaydin/ls_cikti` yazdığımız zaman, aslında `cat` programı ilk parametresi olan `/home/eaydin/ls_cikti` değerini okudu. Eğer bu dosyayı parametrik olarak değil de, standart girdi olarak sunmak isteseydik, şöyle bir  işlem yapmamız gerekirdi:

```
eaydin@eaydin-vt ~ $ cat < ls_cikti
total 92
-rw-rw-r-- 1 eaydin eaydin   203 Jan  8 14:30 args.txt.sample
-rw-rw-r-- 1 eaydin eaydin   401 Jan  8 15:51 config.ini.sample
-rw-r--r-- 1 eaydin eaydin  1070 Dec 18 16:32 LICENSE
-rwxrwxr-x 1 eaydin eaydin 12908 Jan  8 14:30 pgpwatch.py
-rwxrwxr-x 1 eaydin eaydin 15274 Jan  8 14:30 poolstatus.py
-rw-rw-r-- 1 eaydin eaydin 13491 Jan  8 14:30 README.md
-rwxrwxr-x 1 eaydin eaydin 12184 Jan  8 14:30 repmgrwatch.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 scripts
-rwxrwxr-x 1 eaydin eaydin  5955 Jan  8 14:30 sendmail.py
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 16:04 services
drwxrwxr-x 2 eaydin eaydin  4096 Jan  8 14:30 test
total 32
drwxr-xr-x 4 eaydin eaydin 4096 Dec 18 16:32 build
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 DiskAlert
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 DiskAlert.egg-info
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 dist
drwxr-xr-x 2 eaydin eaydin 4096 Dec 18 16:32 etc
-rw-r--r-- 1 eaydin eaydin 1070 Dec 18 16:32 LICENSE
-rw-r--r-- 1 eaydin eaydin  593 Dec 18 16:32 README.md
-rw-r--r-- 1 eaydin eaydin 1465 Dec 18 16:32 setup.py
```

Gördüğünüz gibi, burada standart çıktı işaretinin tersini \(**&lt;**\) kullanıyoruz. Standart girdilerde, _üzerine yazma_ gibi bir durum söz konusu olmadığından, **&lt;&lt;** gibi bir notasyona gerek kalmaz.

Yukarıda kullandığımız `cat` programı örneği, her ne kadar standart girdi kullanmış olsa da, pek bir anlam ifade etmeyebilir. En nihayetinde kullanıcı açısından dosya ismini parametrik olarak belirtmek ile standart girdi olarak belirtmek arasında bir fark bulunmamaktadır. Aslında teknik olarak bir fark var ancak buna birazdan değineceğiz.

`cat` programı eğer standart girdiden veri beklentisi içindeyse, `cat` programını hiçbir parametre kullanmadan çalıştırırsak ne olur? Bu sorunun cevabını `cat` programı çalıştırıp "Bu bir cümle" yazarak deneyelim.

```
eaydin@k9 ~ $ cat
Bu bir cümle
Bu bir cümle
^C
```

Yukarıda ne olup bittiğine bir bakalım: Hiçbir parametre göndermeden `cat` programını çalıştırıyoruz. Sonra program bizden \(standart girdiden\) bir girdi bekliyor. Biz de "Bu bir cümle" yazıp ENTER tuşuna basıyoruz. Ardından ekranda bir satır daha kendiliğinden beliriyor ve bizim yazdığımız cümlenin aynısını yazıyor. Sonra CTRL+c tuşu ile programı sonlandırıyoruz.

Bu örnekteki ikinci "Bu bir cümle" satırının yazmasının sebebi, `cat` programının işlevinde yatmaktadır. Standart girdi veya parametrik dosya belirtilmesi farketmeksizin `cat` programı, kendisine gönderilen verinin standart çıktıya yazdırılması işlemini gerçekleştirdiği için, biz "Bu bir cümle" yazıp programa bunu sunduktan sonra, program bunu standart çıktıya yazıyor. Dolayısıyla ekranda iki kez görmüş oluyoruz.

Eğer `cat` programını çalıştırırken standart çıktıyı bir dosyaya yönlendirseydik, durum farklı olurdu.

```
eaydin@k9 ~ $ cat > deneme
Bu da başka bir cümle
^C
```

Burada programa standart girdiden veri gönderdik, ancak standart çıktıyı `deneme` dosyasına yönlendirdiğimiz için, ekranımızda ikinci kez görmedik. `deneme` dosyasının içeriğini okuyacak olursak ne beklediğimiz aşikar sanırım.

```
eaydin@k9 ~ $ cat deneme
Bu da başka bir cümle
```

Bütün bu örneklerin hala bir şey ifade etmediğinin farkındayız. Çünkü `cat`, zaten dosya okuma amacı güden bir program. Bunun yerine daha sık kullanılan bir örnek olan, MySQL veritabanının komut satırı aracı `mysql` programının kullanımına bakalım.

MySQL ile ilgili detaylar kitabımızın farklı bir bölümünde anlatılmakta ancak kısaca programın bir veritabanının yapısını komut satırından yönetmeye olanak sağladığını belirtelim. `mysql` programı etkileşimli bir programdır, yani Douglas McIlroy'un ikinci maddede bahsettiği, aslında kaçınmamız gereken program tiplerinden birisidir. Ancak bir veritabanını yönetmenin en kullanışlı yollardından birinin bu olduğu unutulmamalıdır. `mysql` programını doğru parametrelerle çalıştırıp veritabanına bağlandığımızda, bizi kendi kabuğu \(etkileşimli ortamı\) karşılar.

```
[root@emre ~]# mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 160953
Server version: 5.5.50 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Burada örneğin, mevcut veritabanlarını listeletmek için, `show databases` komutunu girmemiz gerekir.

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| veriteknik         |
| sanallastirma.com  |
| mysql              |
| wordpress          |
+--------------------+
5 rows in set (0.01 sec)
```

Aslında, buradaki `show databases` bilgisini, `mysql` komutuna standart girdiden, yani klavyemizden girmiş bulunduk.

Bunun yerine, bir metin dosyasına `show databases` yazıp, bu metin doyasını `mysql` programına standart girdi olarak sunabilirdik. `komutlar` dosyasının içeriği `show databases` olmak üzere, aşağıdaki gibi bir notasyonla, `mysql` programına komutlarımızı gönderebiliriz.

```
[root@emre ~]# mysql < komutlar
Database
information_schema
veriteknik
sanallastirma.com
mysql
wordpress
```

Çıktılardaki ufak fark \(tablolu yapı, kaç satır döndüğü vs.\) sizi yanıltmasın. Bu tamamen `mysql` programının ayarlarıyla ilgili. Burada esas dikkat edilmesi gereken nokta, MySQL geliştiricilerinin, programlarını standart girdiden veri okuyabilecek şekilde yazmış olması. Bu açıdan bakınca, aslında David McIlroy'un ikinci maddesi ile uyum bir yazılım ortaya çıkmış oluyor. Program, _etkileşimli girdi konusunda ısrarcı olmuyor_. Ayrıca çıktıda, yine McIlroy'un bahsettiği gibi "gereksiz tablolu yapılar" bulunmuyor.

MySQL bir yana, hemen her veritabanı programı bu şekilde çalışmaktadır. Bu özellikle de veritabanlarına yedeklerin yüklenmesi konusunda kullanılan yöntemlerden birisidir. Aslında bir veritabanı yedeği aldığınızda, size bir dizi komut çıktısı verir. Bu çıktıyı dilediğiniz gibi şekillendirebilir, ve yeniden programlara standart girdiden sunabiliriniz.

Hatırlarsanız bu bölümde `cat` programına parametrik kullanım ve standart girdi ile veri sağlanmasının kullanıcı açısından pek bir fark yaratmadığını, ancak ufak bir farklılığı olduğundan bahsetmiştik.

`cat dosya-ismi` kullanımında, önce `cat` programı çalıştırılır, `cat` programı da parametreleri hangi sırada nasıl okuyorsa ona göre ilgili işlemleri yapar. Ancak `cat < dosya-ismi` kullanımında, kabuk \(_shell_\) önce `dosya-ismi` dosyasını yüklemeye çalışır, eğer bu dosya mevcut değil veya kullanıcı tarafından erişilemez durumdaysa, hata verir ve `cat` programını hiç çalıştırmaz bile. Bu, özellikle `cat` gibi küçük programlarda hissedilmeyen etkilere sebep olur ancak `mysql` veya çok daha büyük bir programın her defasında boş yere çağırılmasına engel olabileceği için, aslında kabuk programlamada mümkünse kullanılması gereken yöntemlerden birisidir ve doğru kullanıldığında bazı noktalarda sisteminizin daha verimli çalışmasını sağlayabilir.

Standart girdinin **&lt;** notasyonu ile kullanımı, yıllarca UNIX sistemler yönetmiş bir kişinin bile çok nadir karşılaştığı durumlar olabilir. Bu kitapta da özellikle **crontab** ve **netcat** bölümlerinde birkaç örneği dışında denk gelmemeniz olası. Ancak standart girdinin ne işe yaradığının anlaşılması, özellikle birazdan göreceğimiz **pipe** mekanizmasının anlaşılmasında belkemiği görevi görmektedir.

## Unix Pipeline

Douglas McIlroy, işletim sistemindeki programların \(veya parçacıkların\) birer standart girdisinin ve standart çıktısının olması gerektiği fikrini aslında yukarıdaki örneklerde bahsettiğimiz gibi sadece dosya yönlendirme amacıyla geliştirmemiştir. McIlroy'un amacı, aslında programların birbirleriyle, arada geçici bir dosya veya kullanıcı müdahalesine gerek olmaksızın veri akışının sağlanmasıdır. Bunu yaparken de, bir programın çıktısının diğer programın girdisi olarak yönlendirilmesini kurgulamıştır. McIlroy bu metodolojiyi ilk defa 1973'te kurgulamıştır ve Ken Thompson, `pipe()` sistem çağrısını Unix versiyon 3 üzerinde uyarlamıştır. Bunu yaparken de yönlendirme işareti olarak `|` kullanılmıştır. McIlroy, bu karakterin kullanımını Thompson'ın geliştirdiğini belirtir. Bu yatak çubuk, aslında bir _boru \(pipe\)_ olarak düşünülünce, tıpkı bir su tesisatındaki boru hattı gibi, bir noktadan giren bilginin, diğer noktadan çıkmasını sağladığı için_ pipeline_ terminolojisini doğurmuştur.

Örneğin `less` programı, aslında kendisine sağlanan bilginin terminal ekranına sığacak şekilde parçalanmasını sağlar. Tıpkı `cat` programında olduğu gibi, parametrik kullanımla dosya adı belirtilebilir, veya standart girdi olarak veri sağlanabilir.

Eğer çok fazla dosya içeren bir dizin içerisinde `ls -l` komutunu çalıştırırsak, standart çıktı içerisinde kaybolabiliriz. Bunun için `ls -l`komutunun çıktısını, `less` programına girdi olarak sunabiliriz. Bunun için yapmamız gereken tek şey, aralarında _pipe_ işaretini kullanmak olacaktır.

```
root@ubuntu:/mnt/backups/orthogonal/# ls -l | less
total 7019320
-rwxr-xr-x 1 root root     15099 May 23  2016 E28b30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root  77772699 May 23  2016 E28b30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28b40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root  13464608 May 23  2016 E28b40_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c10_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 260127268 May 23  2016 E28c10_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c20_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 261387832 May 23  2016 E28c20_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 262376992 May 23  2016 E28c30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 250611074 May 23  2016 E28c40_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d10_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root  12259662 May 23  2016 E28d10_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d20_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 130844393 May 23  2016 E28d20_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 244966770 May 23  2016 E28d30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 208044160 May 23  2016 E28d40_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E29a30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 216910411 May 23  2016 E29a30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E29a40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 176545547 May 23  2016 E29a40_0HGK030HF2015COG.ecw
--More--
```

Bu çıktının en altında görülen `--More--` kısmı aslında, `less` programının getirdiği bir sonuç. Aynı şekilde `less` çalıştırıldıktan sonra, standart girdiden \(klavyeden\) herhangi bir _input_ beklemektedir. Kısacası `less` programı devreye girdikten sonra eğer bir tuşa basarsak, `ls -l` çıktısının geri kalanının ekranımıza sığdığı kadarını karşımızda görebiliriz.

Aslında buradaki notasyon şu şekilde işler. `komut1 | komut2 | komut3` şeklinde sonsuza kadar[^1] komutları birbirine bağlayabilirsiniz. Buradaki komutlar birbirinden farklı olabileceği gibi, birbirleriyle aynı da olabilir. Bu komutların birbirleriyle _konuşabilmeleri_ için bilmeleri gereken bir şey de yoktur. McIlroy'un 2. maddede bahsettiği, _farklı programların birbirleriyle konuşmasını sağlama_ düşüncesi de burada ortaya çıkar.

Örneğin, sistemimizdeki yüklü Python modüllerinin bulunduğu `/usr/local/lib/python3.5/dist-packages` dizini içerisinde sonu `.py` ile _bitmeyen_ dosyaların sayısını öğrenmek istersek, aşağıdaki komut dizisini kullanabiliriz.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | wc -l
129
```

Buradaki ilk komut, `ls /usr/local/lib/python3.5/dist-packages` çok tanıdık bir iş yapıyor: dizinin içeriğini listeliyor. Ancak içeriğini standart çıktıda göstermek yerine, `grep` programına iletiyor. `grep` ise standart girdiden okuduğu veriler üzerinde `-v .py$` parametrelerini çalıştırıyor. Burada yaptığı iş, "sonu `.py` ile biten dosyaları göstermemek". Detaylarına grep ve Regular Expressions bölümünde değineceğiz. Elimizde artık sonu `.py` ile bitmeyen dosyaların bir listesi var, ancak bunu görüntelemek istemiyoruz, çünkü bunların sayısını öğrenmek istiyoruz. Öyleyse standart çıktısını `wc` \(_word count_\) programına iletiyoruz. `wc -l` ile standart girdiden gelen verinin kaç satır olduğunu sayıyoruz, ve bu sonucu artık standart çıktıya yazıyoruz. Bu yüzden standart çıktı olarak **129** değerini görüyoruz.

Tahmin edeceğiniz gibi, burada elde ettiğimiz sayıyı standart çıktı yerine aşağıdaki gibi bir dosyaya da yönlendirebilirdik.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | wc -l > paket_sayisi
```

## T-Pipe

Yaptığımız son örnekte, dosyaların sayısını `paket_sayisi` isminde bir dosyaya yazdırdık. Ancak eğer bu dosya sayısını ekranda görmek istiyor, ancak dosyaların bir listesini bir dosyaya yazdırmak istiyor olsaydık?

Örneğin, eğer paketlerin listesini bir dosyaya yazdırmak isteseydik, sondaki word count komutunu kullanmayıp, grep'in standart çıktısını bir dosyaya yönlendirmemiz gerekecekti.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ > paket_listesi
```

Öte yandan, paketlerin sayısını ekranda görmek için, daha önce kullandığımız komutu kullanmamız gerekirdi.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | wc -l
129
```

Bir akış içerisinde standart çıktıyı yönlendirmek yerine, birkaç parçaya bölme işini yapmak için `tee` isimli program geliştirilmiştir. Örneğin `tee` kullanarak aşağıdaki komut dizisini yazarsak, yukarıdaki iki komutu ayrı ayrı çalıştırmamıza gerek kalmaz.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | tee paket_listesi | wc -l
129
```

Gördüğünüz gibi, standart çıktıya yine word count programının sonucu olan **129** yazıldı. Ancak arada `tee` programı, `grep`'in çıktısını `paket_listesi` dosyasına yazdı. Dosyanın baş kısmına `head` programıyla bakıp içeriğine göz atabiliriz.

```
eaydin@eaydin-vt ~ $ head paket_listesi 
alabaster
alabaster-0.7.10.dist-info
asn1crypto
asn1crypto-0.24.0.dist-info
attr
attrs-17.3.0.dist-info
babel
Babel-2.5.1.dist-info
bcrypt
bcrypt-3.1.4.dist-info
```

`tee` programı, aslında Unix pipeline'ında bir T-Pipe görevi gördüğü için bu ismi almıştır. Unix pipeline'ı aslında bir boru tesisatı gibi düşünülebilir, ismi de buradan gelir zaten. Yazılımlar birbirlerine borularla bağlıdır, ve akış tek yönlüdür. Soldan sağa doğru akış gerçekleşir. Eğer bu akış içerisinde bir dallanmaya ihtiyaç duyarsak, boru tesisatlarında olduğu gibi T şeklinde bir boru kullanmamız gerekir. Böylece akışı iki veya daha fazla dala ayırabiliriz. Programa birden fazla parametre verip, çıktının birden fazla dosyaya da yazdırılmasını sağlayabiliriz.

Kısacası `tee` programı, standart girdiden gelen veriyi, hem kendisine parametre olarak verilen dosyalara yazar, hem de standart çıktıya yönlendirir. Böylece kendi standart çıktısı hangi programa standart girdi olarak sunulmuşsa, komutların akışı devam edebilir.

Normal şartlar altında **&gt;** işaretinin yaptığı gibi, kendisine parametre olarak verilen dosyaları silip üzerine yazar. Ancak bu dosyaların mevcut bilgilerini koruyup sonuna veri eklemesini istersek \(_append_ etmesini istersek\) `-a` parametresiyle çalıştırmak gerekir. Bu, standart çıktı yönlendirmedeki **&gt;&gt;** işaretinin karşılığı gibi düşünülebilir.

### Bazı tee Örnekleri

Kullanımı hakkında, GNU Core Utils'de paylaşılan birkaç örnek fikir verebilir.

Örneğin herhangi bir dosyanın internetten indirilmesi ile dosyanın MD5 Checksum hesaplanmasının sağlanmasını bir arada yapmak için aşağıdaki komut incelenebilir.

```
wget -O - http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso | tee centos7.iso | md5sum > centos7.md5
```

Yukarıdaki komut VeriTeknik mirrorlarından CentOS 7 Minimal güncel versiyonunu indirir, `tee` ile dosya `centos7.iso` olarak kaydedilir ancak standart çıktıya yazılan veri `tee` ile `md5sum` programına aktarılır ve MD5 Checksum hesaplandıktan sonra `centos7.md5` dosyasına kaydedilir. Burada önemli olan noktalardan birisi, MD5 hesaplanması için verinin tamamının indirilmesinin beklenmemesidir. Veri indikçe md5sum programına veri akacak ve hesaplama başlayacaktır. Dolayısıyla verinin önce diske yazılması beklenmemektedir. Verinin önce diske yazılmasını, sonra `md5sum` programının diskten tekrar okumasını sağlamak için şöyle yapabilirdik:

```
wget http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso centos7.iso && md5sum centos7.iso
```

Ancak burada işlemler sırayla yapılacağı için, UNIX Pipeline'ın avantajlarından hiçbir şekilde faydalanmamış olurduk.

### Process Substitution

Eğer yukarıda indirdiğimiz dosyanın, indirilirken, aynı anda hem MD5 Checksum'ının, hem de SHA1 Checksum'ının hesaplanmasını isteseydik, `tee` ile standart çıktıyı iki farklı programa yönlendirmemiz gerekecekti. Bu işleme _process substitution_ denilir. Aşağıdaki örnek ile görülebilir.

```
eaydin@eaydin-vt ~/Downloads $ wget -O - http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso | tee >(sha1sum > centos7.sha1) >(md5sum  > centos7.md5) > centos7.iso
--2018-03-14 14:19:36--  http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso
Resolving mirror.veriteknik.net.tr (mirror.veriteknik.net.tr)... 94.103.33.100, 2a00:7300:1::101
Connecting to mirror.veriteknik.net.tr (mirror.veriteknik.net.tr)|94.103.33.100|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 830472192 (792M) [application/octet-stream]
Saving to: ‘STDOUT’

-                                 100%[=============================================================>] 792,00M  11,1MB/s    in 73s     

2018-03-14 14:20:49 (10,9 MB/s) - written to stdout [830472192/830472192]

eaydin@eaydin-vt ~/Downloads $ cat centos7.md5
5848f2fd31c7acf3811ad88eaca6f4aa  -
eaydin@eaydin-vt ~/Downloads $ cat centos7.sha1 
aae20c8052a55cf179af88d9dd35f1a889cd5773  -
```

Yukarıdaki komut dizisinin kritik noktası, `tee >(sha1sum > centos7.sha1) >(md5sum  > centos7.md5)` bölümüdür. Burada tee, standart çıktısını iki farklı işleme daha yönlendirir. Bu işlemler parantez içinde belirtilir, ve daha önce gördüğümüz **&gt;** işareti ile bu işlemlere yönlendirme yapılır. Burada dikkat edilmesi gereken nokta, **&gt;** işareti ile **\(** arasında boşluk bulunmaması gerektiğidir. Öte yandan `sha1sum` ve `md5sum` sonrasında gelen **&gt;** işaretleri doğrudan bu programların standart çıktılarını yönlendirme amacıyla yazıldığından, boşluk konması problem yaratmaz, zaten parantez içinde kullanılma sebepleri de budur.

Sanki `tee` programı iki farklı dosyaya yazmak yerine, iki farklı işleme standart çıktıyı yönlendirmektedir. Burada dosya \(_file_\) yerine işlem \(_process_\) koyduğumuz için, bir değişiklik \(_substitution_\) işlemi yapmış olduk. Bu yüzden bu yönteme _process substituion_ denilir. Bu örnekte de, indirme işleminin, SHA1 hesaplamasının ve MD5 hesaplamasının birbirlerini beklemediğini, işlemin paralel gerçekleştirildiğini hatırlatmakta fayda var.

## stdio.h

Herhangi bir C programı yazdıysanız, hemen hemen her zaman `stdio.h` başlık dosyasını programın başında çağırdığınızı fark etmişsinizdir. Aslında bu dosya, programın standart girdi ve standart çıktı ile etkileşimini sağlayan bileşenleri barındırır. İsmi bu yüzden _Standard Input/Output_'un kısaltmasıdır.

Brian Kernighan ve Dennis Ritchie'nin meşhur **The C Programming Language** kitabındaki basit bir örneği uygulayacak olursak, tanıdık sonuçlar elde ettiğimizi görebilirsiniz.

```
#include <stdio.h>
#include <ctype.h>

main()
{
    int c;
    while ((c = getchar()) != EOF)
        putchar(tolower(c));
    return 0;
}
```

Yukarıdaki kodu derlediğimizde, standart girdiden okuduğu kelimelerin, küçük harfe çevrilerek standart çıktıya yazdığı görülebilir. Derlediğimiz programa `lower` ismini verecek olursak, örneğin aşağıdaki şekilde kullanabiliriz.

```
eaydin@eaydin-vt ~/devel/lower $ echo AbCdE | ./lower
abcde
```

Gördüğünüz gibi, pipe işareti ile doğrudan standart girdiden gelen veriyi okuyabildik. Eğer bu verileri bir metin dosyasına yazsaydık, standart girdi olarak metin dosyasından da yönlendirebilirdik.

Önce metin dosyamızı oluşturalım.

```
eaydin@eaydin-vt ~/devel/lower $ echo AbCdE > karakterler
eaydin@eaydin-vt ~/devel/lower $ cat karakterler 
AbCdE
```

Şimdi programımıza standart girdi olarak sunalım.

```
eaydin@eaydin-vt ~/devel/lower $ ./lower < karakterler
abcde
```

Kısacası programımız için bilginin pipe ile veya **&lt;** gelmesinin bir önemi yok. İkisi de standart girdi çünkü. Benzer şekilde `ls` çıktısını da programımıza yönlendirebilirdik.

```
eaydin@eaydin-vt ~/devel/lower $ ls
Buyuk_HARFLI_diZin  karakterler  lower  lower.c
eaydin@eaydin-vt ~/devel/lower $ ls | ./lower
buyuk_harfli_dizin
karakterler
lower
lower.c
```

Halbuki bölümün başında McIlroy'un belirttiği gibi, ne `ls` programının bizim `lower` programımızdan haberi var, ne de `lower` programı yazılırken `ls` ile ilgili bir noktayı dikkate aldık.

Programımızdaki `getchar` fonksiyonu, aslında standart girdiden veri okuyan kısımdır. `puthcar` fonksiyonu ise standart çıktıya veri yazmaktan sorumludur. Bu fonksiyonlar `stdio.h` ile sağlanır. Bu yüzden kullanmak için _include_ etmemiz gerekir.

Eğer programı tek başına çalıştırırsak, biraz daha anlaşılabilir durum.

```
eaydin@eaydin-vt ~/devel/lower $ ./lower 
Bu bir Cümle
bu bir cümle
^C
```

Tanıdık geldi mi? Bu bölümün başlarında `cat` programını tek başına çalıştırdığımızda da benzer manzarayla karşılaşmıştık. Yine standart girdiden veri bekledi. Biz `Bu bir Cümle` yazdıktan sonra, kendisi yine standart çıktıya verinin aynısını yazdı, tıpkı `cat` gibi. Ama bu sefer karakterleri küçülttü. Yine Ctrl+c ile programımızı sonlandırarak çıktık.

Burada dikkat edilmesi gereken bir diğer nokta, programımızın parametrik şekilde girdi almaması. Örneğin karakterler dosyasını argüman olarak kendisine veremezdik, çünkü programımızda gelen argümanlarla neler yapılması gerektiğini belirtmedik. Böyle olunca argümanları görmezden gelecektir, ve yukarıdaki durumun aynısı gerçekleşecektir.

```
eaydin@eaydin-vt ~/devel/lower $ ./lower karakterler 
Argümanları Dikkate ALMADI
argümanları dikkate almadi
^C
```

## Standart Hata

Şimdiye kadar standart girdi ve çıktı yönlendirmelerini, Unix pipeline dahilinde programların birbirleriyle iletişim sağlayabilmesi için nasıl kullandığımızı gördük.

Aslında Unix üzerinde programların üç temel veri akış biçimi vardır. Standart girdi, standart çıktı ve standart hata. İngilizceleri _Standard Input_, _Standard Output_ ve _Standard Error_ olarak geçer. Bu yüzden bazen kısaltmalarını _stdin_, _stdout_, _stderr_ olarak görebilirsiniz.

Standart girdi ve standart çıktının varoluş amaçlarını biraz önceki örnelerde irdeledik. Standart hata ise, program çalıştırıldığında, programın hatalarını yönlendireceği noktayı belirtir. Normal şartlar altında bir program sonuçlarını da \(standart çıktı\) hatalarını da \(standart hata\) ekranımıza yönlendirir. Böylece program hata verdiyse, çıktıları arasında görürüz. Ancak bu her zaman istediğimiz bir şey olmayabilir. Özellikle çok fazla çıktı veren programlarda gerçekleşen hataların ayıklanması zorlaşabilir. Ayrıca, programların standart çıktılarını başka program veya dosyalara yönlendirdiğimiz, hataların da burada yer almasını istemeyebiliriz. Bu tip durumların önüne geçebilmek için, standart çıktıdan bağımsız olan bir çıktı biçimi olarak standart hata tanımlanmıştır. Bu sayede örneğin bir programın standart çıktısı pipe ile bir başka programa girdi olarak sunulurken, programın çalışması sırasında oluşacak hatalar kullanıcının terminal ekranında görülebilir.

Standart hata kavramı, Unix'in 6. versiyonunun ardından Dennis Ritchi tarafından geliştirilmiştir. Bu yöntem ile birbirine standart girdi-çıktı yönlendiren programların standart hatalarının terminale yazdırılması durumunda, hangi hata mesajının hangi programdan geldiğinin bilinmemesi problemiyle de karşılaşılmıştır. Douglas McIlroy, bu problemin farkında olduklarını ama asla tamamen çözülmediğini, _UNIX Programmer's Manual_'ın 3. versiyonunda belirtir.

> All programs placed diagnostics on the standard output. This had always caused trouble when the output was redirected into a file, but became intolerable when the output was sent to an unsuspecting process. Nevertheless, unwilling to violate the simplicity of the standard-input-standard-output model, people tolerated this state of affairs through v6. Shortly thereafter Dennis Ritchie cut the Gordian knot by introducing the standard error file. That was not quite enough. With pipelines diagnostics could come from any of sev eral programs running simultaneously. Diagnostics needed to identify themselves. Thus began a never quite finished pacification campaign: a few recalcitrant diagnostics still remain anonymous or appear on the standard output.

Standart girdi, standart çıktı ve standart hata, ayrı file descriptor'lar ile temsil edilir. Daha önce incelediğimiz stdio.h için bu gösterimler stdin, stdout, stderr iken, bunlara karşılık gelen tam sayılar da genellikle bash gibi kabuklarda kullanılır.

| İsim | Sayısal Değer | &lt;stdio.h&gt; Kullanımı |
| :--- | :--- | :--- |
| Standart Girdi | 0 | stdin |
| Standart Çıktı | 1 | stdout |
| Standart Hata | 2 | stderr |

Yukarıdaki tablodan görüleceği gibi, aslında standart çıktının file descriptor'ını sayısal karşılığı 1'dir. Yani, aşağıdaki iki örnek, aynı anlama gelmektedir. İlk başta, daha önce uyguladığımız örneklerde olduğu gibi, file descriptor kullanmadan oluşan sonuca bakalım.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler > cikti1
eaydin@eaydin-vt ~/devel/lower $ cat cikti1 
AbCdE
```

Aşağıda ise, file descriptor ile aynı sonucun elde edildiğini görebiliyoruz.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler 1> cikti2
eaydin@eaydin-vt ~/devel/lower $ cat cikti2
AbCdE
```

Yönlendirmelerde standart çıktı çok sık kullanıldığı için, `1>` kullanımı olmadan da standart çıktı yönlendirmesi gerçekleştirilir.

Şimdi benzer işlemi standart hata yönlendirmesinde kullanalım.

cat programı, kendisine parametre olarak kaç dosya verilirse, tamamını peş peşe eklemekle görevlidir. Aşağıdaki kullanım açıklayıcı olacaktır.

```
eaydin@eaydin-vt ~/devel/lower $ echo Bu bir cümle > cumleler
eaydin@eaydin-vt ~/devel/lower $ cat cumleler 
Bu bir cümle
eaydin@eaydin-vt ~/devel/lower $ cat karakterler cumleler 
AbCdE
Bu bir cümle
```

Eğer parametre olarak verdiğimiz dosyalardan birisi yoksa \(veya okunamıyorsa\), hata verir.

```
eaydin@eaydin-vt ~/devel/lower $ cat cumle
cat: cumle: No such file or directory
```

Aşağıda, iki dosyayı birleştirmesini istiyoruz.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler cumle > deneme
cat: cumle: No such file or directory
eaydin@eaydin-vt ~/devel/lower $ cat deneme
AbCdE
```

Ancak gördüğünüz gibi, dosyalardan birisi \(`cumle`\) olmadığı için program hata verdi. Yine de `deneme` dosyası oluşturuldu ve içinde sadece `karakterler` dosyasının içeriği yer alıyor. Eğer çok fazla dosyayı birleştiriyor olsaydık, veya bu işlemi bir script'in içerisinde kullanıyor olsaydık, veya pipe ile birçok işlemi birleştiriyor olsaydık, bu işlemin hatalarını terminal ekranına yazdırmak yerine bir dosyaya yönlendirmesini tercih edebilirdik. Bunun için file descriptor kullanımı gerekir ve yukarıdaki tablodan anlaşılacağı gibi `2>` notasyonuyla bu işlem gerçekleştirilir.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler cumle > deneme 2> hatalar
eaydin@eaydin-vt ~/devel/lower $ cat deneme 
AbCdE
eaydin@eaydin-vt ~/devel/lower $ cat hatalar
cat: cumle: No such file or directory
```

Burada önce standart çıktıyı `deneme` dosyasına yönlendirdiğimizi, standart hatayı ise `hatalar` dosyasına yönlendirdiğimizi görebilirsiniz. Yaptığımız işlemin sırasının bir önemi yok. Yani önce standart hatayı yönlendirip, sonra standart çıktıyı yönlendirebilirdik. Genellikle bu yapılmaz \(1 ve 2 sırasını içgüdüsel olarak koruruz\) ancak yapılmasında bir mahsur bulunmaz.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler cumle 2> hatalar > deneme
eaydin@eaydin-vt ~/devel/lower $ cat hatalar
cat: cumle: No such file or directory
eaydin@eaydin-vt ~/devel/lower $ cat deneme
AbCdE
```

Bütün bu işlemlerde, standart çıktı yönlendirmesi için 1&gt; notasyonunu da kullanabilirdik. Yine, alışkanlık gereği pek kullanılmaz sadece. Yani aşağıdaki işlem ile yukarıdaki aynı işe yarayacaktır.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler cumle 2> hatalar 1> deneme
```



## Yaygın Kullanım Biçimleri

## Useless Use of cat

## Named Pipe

[^1]: Aslında teknik olarak sonsuza kadar değil, sisteminizin _open file descriptor limit_'ine kadar, ancak bu limit pratik olarak sizi etkilemeyecek kadar büyüktür.

