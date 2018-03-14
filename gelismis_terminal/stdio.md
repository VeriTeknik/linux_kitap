# Standart Girdi ve Çıktı Yönlendirme

GNU/Linux ve benzeri POSIX sistemlerin en güçlü olduğu yönlerden birisi, programların standart girdi ve çıktılarının yönlendirilmesidir. Özellikle birden fazla programın işlemlerini birbirlerine kolayca gönderebilme gücü sağlayan bu yöntem, aslında UNIX'in oluşturulduğu dönemdeki temel felsefeleri en iyi yansıtan özelliklerinden biridir.

1978'de yayımlanan The Bell System Technical Journal'ın UNIX Time-Sharing System dokümanının önsözünde David McIlroy, UNIX'in tasarlanması ve kullanılmasında temel alınan kritik noktaları 4 madde halinde listeler. Bu maddelerden ilki ilerleyen yıllarda çok popüler olmuştur. Maddenin tam hali şu şekildedir:

> Make each program do one thing well. To do a new job, build afresh rather than complicate old programs by adding new "features".

Bu maddede McIlroy, bir program yazılırken, bu programın _tek_ _bir şeyi iyi yapması gerektiğini_** **vurgular. Yeni bir iş yapılacağı zaman, mevcut programa yeni "özellikler" eklemek yerine, yeni bir program yazılmasının daha doğru olduğunu savunur.

Önsözde paylaşılan maddelerden ikincisinin tam hali ise şu şekildedir:

> Expect the output of every program to become the input to another, as yet unknown, program. Don't clutter output with extraneous information. Avoid stringently columnar or binary input formats. Don't insist on interactive input.

Bu madde başlı başına çok fazla şey söyler, ve bir süre GNU/Linux sistemler üzerinde yazılmış programları kullanan herkes, aslında bu maddelerin çoğu programda uygulandığını görecektir. Öncelikle bu bölümde bahsedeceğimiz girdi ve çıktıların yönlendirilmesi gerekliliği, maddenin ilk cümlesinde ifade edilmiştir. McIlroy, her programın çıktısının aynı zamanda girdi olarak tasarlanması gerektiğini, hatta ne iş yaptığını bilmediği bir programın girdisi olarak kullanılabileceğinin gözönünde bulundurulması gerektiğini belirtir. Çıktıların gereksiz bilgilerle kirletilmemesi gerektiğini, kolon bazlı girdilerin veya binary girdilerin dayatılmasından kaçınılması gerektiğini ifade eder. Ayrıca, gerekmediğinde _etkileşimli girdiden_ kaçınılması gerektiğini belirtir. Çünkü etkileşimli girdi, bir programa standart girdi olarak veri göndermenin zorlaşması demektir.

Eğer girdi ve çıktı yönlendirmeleri ile ilgili hiç tecrübeniz yoksa, yukarıdaki satırlar size soyut gelebilir. Bu bölümü okuduktan sonra yukarıdaki anlayışın ne ifade ettiği biraz daha netleşecektir. Ayrıca kitabın ilerleyen bölümlerini okuduktan sonra tekrar dönüp bu felsefeyi okumanızda fayda olabilir.

## Standart Çıktı

Eğer bu _standart çıktı_ sizin için hiçbir şey ifade etmiyorsa, önce _standart_ sözcüğünü ortadan kaldıralım: Bir programın _çıktısı_ ne demek?

Terminal üzerinden herhangi bir komut çalıştırdığınızda, aslın o program bir _şey_ üretecektir. Genellikle bu ürettiği _şeyi_ ekrana yazdığını görürüz. Örneğin ls komutu, bulunduğumuz dizindeki dosyaları gösterir.

```
eaydin@eaydin-vt ~/devel/pgpoolwatch $ ls
args.txt.sample    LICENSE      poolstatus.py  repmgrwatch.py  sendmail.py  test
config.ini.sample  pgpwatch.py  README.md      scripts         services
```

Burada ls komutu, aslında LICENSE, scripts gibi dosya ve dizin isimlerini bize çıktı olarak sunmuştur. Örneğin ls -l yazsaydık, farklı bir çıktı sunacaktı.

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

Her iki durumda da `ls` programına, çıktıyı nereye yazacağını söylemiyoruz, bu yüzden `ls`, standart olan çıktıya yazıyor. Bu durumda da bizim terminal ekranımız.

UNIX sistemlerde, bu çıktıyı "standart olmaktan çıkarma" amacıyla, standart çıktıyı farklı bir \_yere \_yönlendirmenin faydalı olacağı görülmüştür. Örneğin yukarıdaki `ls -l` sonucunu, standart çıktı yerine bir dosyaya yönlendirebiliriz.

```
eaydin@eaydin-vt ~/devel/pgpoolwatch $ ls -l > /home/eaydin/ls_cikti
eaydin@eaydin-vt ~/devel/pgpoolwatch $
```

Farkındaysanız, artık ekranda ls -l komutunun sonucunu görmüyoruz, çünkü programın standart çıktısını `/home/eaydin/ls_cikti` dosyasına yönlendirdik. Böyle olunca standart çıktısı olan terminalimize veri yazmadı.Eğer gidip `/home/eaydin/ls_cikti` dosyasının içeriğini okursak, biraz önceki sonucun aynısını görürüz.

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

Örneğin biraz önceki örneğimizde kullandığımız çıktı dosyasının sonuna, farklı bir dizinin çıktısını da eklemek isteseydik, şöyle bir işlem yapabilirdik.

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

UNIX Dünyasında, programların standart çıktıları gibi, aslında standart girdileri de vardır. Biraz önceki örneğimizde `cat` programı ile `ls_cikti` dosyasının içeriğini görüntülerken, okunacak veriyi standart girdi olarak sunmayıp, aslında parametre olarak gönderdik. Yani `cat /home/eaydin/ls_cikti` yazdığımız zaman, aslında `cat` programı ilk parametresi olan `/home/eaydin/ls_cikti` değerini okudu. Eğer bu dosyayı parametrik olarak değil de, standart girdi olarak sunmak isteseydik, şöyle bir  işlem yapmamız gerekirdi:

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

MySQL ile ilgili detaylar kitabımızın farklı bir bölümünde anlatılmakta ancak kısaca programın bir veritabanının yapısını komut satırından yönetmeye olanak sağladığını belirtelim. `mysql` programı etkileşimli bir programdır, yani David McIlroy'un ikinci maddede bahsettiği, aslında kaçınmamız gereken program tiplerinden birisidir. Ancak bir veritabanını yönetmenin en kullanışlı yollardından birinin bu olduğu unutulmamalıdır. `mysql` programını doğru parametrelerle çalıştırıp veritabanına bağlandığımızda, bizi kendi kabuğu \(etkileşimli ortamı\) karşılar.

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

Çıktılardaki ufak fark \(tablolu yapı, kaç satır döndüğü vs.\) sizi yanıltmasın. Bu tamamen mysql programının ayarlarıyla ilgili. Burada esas dikkat edilmesi gereken nokta, MySQL geliştiricilerinin, programlarını standart girdiden veri okuyabilecek şekilde yazmış olması. Bu açıdan bakınca, aslında David McIlroy'un ikinci maddesi ile uyum bir yazılım ortaya çıkmış oluyor. Program, _etkileşimli girdi konusunda ısrarcı olmuyor_.

MySQL bir yana, hemen her veritabanı programı bu şekilde çalışmaktadır. Bu özellikle de veritabanlarına yedeklerin yüklenmesi konusunda kullanılan yöntemlerden birisidir. Aslında bir veritabanı yedeği aldığınızda, size bir dizi komut çıktısı verir. Bu çıktıyı dilediğiniz gibi şekillendirebilir, ve yeniden programlara standart girdiden sunabiliriniz.

Hatırlarsanız bu bölümde `cat` programına parametrik kullanım ve standart girdi ile veri sağlanmasının kullanıcı açısından pek bir fark yaratmadığını, ancak ufak bir farklılığı olduğundan bahsetmiştik.

`cat dosya-ismi` kullanımında, önce `cat` programı çalıştırılır, `cat` programı da parametreleri hangi sırada nasıl okuyorsa ona göre ilgili işlemleri yapar. Ancak `cat < dosya-ismi` kullanımında, kabuk \(_shell_\) önce `dosya-ismi` dosyasını yüklemeye çalışır, eğer bu dosya mevcut değil veya kullanıcı tarafından erişilemez durumdaysa, hata verir ve `cat` programını hiç çalıştırmaz bile. Bu, özellikle `cat` gibi küçük programlarda hissedilmeyen etkilere sebep olur ancak `mysql` veya çok daha büyük bir programın her defasında boş yere çağırılmasına engel olabileceği için, aslında kabuk programlamada mümkünse kullanılması gereken yöntemlerden birisidir ve doğru kullanıldığında bazı noktalarda sisteminizin daha verimli çalışmasını sağlayabilir.

Standart girdinin **&lt;** notasyonu ile kullanımı, yıllarca UNIX sistemler yönetmiş bir kişinin bile çok nadir karşılaştığı durumlar olabilir. Bu kitapta da özellikle **crontab** ve **netcat** bölümlerinde birkaç örneği dışında denk gelmemeniz olası. Ancak standart girdinin ne işe yaradığının anlaşılması, özellikle birazdan göreceğimiz **pipe** mekanizmasının anlaşılmasında belkemiği görevi görmektedir.

## UNIX Pipeline

David McIlroy, işletim sistemindeki programların \(veya parçacıkların\) birer standart girdisinin ve standart çıktısının olması gerektiği fikrini aslında yukarıdaki örneklerde bahsettiğimiz gibi sadece dosya yönlendirme amacıyla geliştirmemiştir. McIlroy'un amacı, aslında programların birbirleriyle, arada geçici bir dosya veya kullanıcı müdahalesine gerek olmaksızın veri akışının sağlanmasıdır. Bunu yaparken de, bir programın çıktısının diğer programın girdisi olarak yönlendirilmesini kurgulamıştır. McIlroy bu metodolojiyi ilk defa 1973'te kurgulamıştır ve Ken Thompson, `pipe()` sistem çağrısını Unix versiyon 3 üzerinde uyarlamıştır. Bunu yaparken de yönlendirme işareti olarak `|` kullanılmıştır. McIlroy, bu karakterin kullanımını Thompson'ın geliştirdiğini belirtir. Bu yatak çubuk, aslında bir _boru \(pipe\) \_olarak düşünülünce, tıpkı bir su tesisatındaki boru hattı gibi, bir noktadan giren bilginin, diğer noktadan çıkmasını sağladığı için \_pipeline_ terminolojisini doğurmuştur.

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

Örneğin, sistemimizdeki yüklü Python modüllerinin bulunduğu `/usr/local/lib/python3.5/dist-packages` dizini içerisinde sonu `.py` ile _bitmeyen_ dosyaların sayısını öğrenmek istersek, aşağıdaki komut dizini kullanabiliriz.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | wc -l
129
```

Buradaki ilk komut, `ls /usr/local/lib/python3.5/dist-packages` çok tanıdık bir iş yapıyor: dizinin içeriğini listeliyor. Ancak içeriğini standart çıktıda göstermek yerine, `grep` programına iletiyor. `grep` ise standart girdiden okuduğu veriler üzerinde `-v .py$` parametrelerini çalıştırıyor. Burada yaptığı iş, "sonu `.py` ile biden dosyaları göstermemek". Detaylarına grep ve Regular Expressions bölümünde değineceğiz. Elimizde artık sonu .py ile bitmeyen dosyaların bir listesi var, ancak bunu görüntelemek istemiyoruz, çünkü bunların sayısını öğrenmek istiyoruz. Öyleyse standart çıktısını `wc` \(_word count_\) programına iletiyoruz. `wc -l`standart girdiden gelen verinin kaç satır olduğunu sayıyoruz, ve bu sonucu artık standart çıktıya yazıyoruz. Bu yüzden standart çıktı olarak **129** değerini görüyoruz.

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









## Standart Hata

## Yaygın Kullanım Biçimleri

## stdio.h

## Useless Use of cat

## Named Pipe

[^1]: Aslında teknik olarak sonsuza kadar değil, sisteminizin _open file descriptor limit_'ine kadar, ancak bu limit pratik olarak sizi etkilemeyecek kadar büyüktür.

