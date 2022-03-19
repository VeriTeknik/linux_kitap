## Standart Girdi

UNIX dünyasında, programların standart çıktıları gibi, aslında standart girdileri de vardır. Biraz önceki örneğimizde `cat` programı ile `ls_cikti` dosyasının içeriğini görüntülerken, okunacak veriyi standart girdi olarak sunmayıp, aslında parametre olarak gönderdik. Yani `cat /home/eaydin/ls_cikti` yazdığımız zaman, aslında `cat` programı ilk parametresi olan `/home/eaydin/ls_cikti` değerini okudu. Eğer bu dosyayı parametrik olarak değil de, standart girdi olarak sunmak isteseydik, şöyle bir  işlem yapmamız gerekirdi:

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

Bunun yerine, bir metin dosyasına `show databases` yazıp, bu metin dosyasını `mysql` programına standart girdi olarak sunabilirdik. `komutlar` dosyasının içeriği `show databases` olmak üzere, aşağıdaki gibi bir notasyonla, `mysql` programına komutlarımızı gönderebiliriz.

```
[root@emre ~]# mysql < komutlar
Database
information_schema
veriteknik
sanallastirma.com
mysql
wordpress
```

Çıktılardaki ufak fark \(tablolu yapı, kaç satır döndüğü vs.\) sizi yanıltmasın. Bu tamamen `mysql` programının ayarlarıyla ilgili. Burada esas dikkat edilmesi gereken nokta, MySQL geliştiricilerinin, programlarını standart girdiden veri okuyabilecek şekilde yazmış olması. Bu açıdan bakınca, aslında David McIlroy'un ikinci maddesi ile uyumlu bir yazılım ortaya çıkmış oluyor. Program, _etkileşimli girdi konusunda ısrarcı olmuyor_. Ayrıca çıktıda, yine McIlroy'un bahsettiği gibi "gereksiz tablolu yapılar" bulunmuyor.

MySQL bir yana, hemen her veritabanı programı bu şekilde çalışmaktadır. Bu özellikle de veritabanlarına yedeklerin yüklenmesi konusunda kullanılan yöntemlerden birisidir. Aslında bir veritabanı yedeği aldığınızda, size bir dizi komut çıktısı verir. Bu çıktıyı dilediğiniz gibi şekillendirebilir, ve yeniden programlara standart girdiden sunabiliriniz.

Eğer `mysql` programına komutları dosya içerisinden değil de, doğrudan terminal ile sunmak istersek, **&lt;&lt;&lt;** notasyonunu kullanabiliriz. Biraz önceki örneğimizde komutlar dosyasının içerisine `show databases` yazıp, `komutlar` dosyasını standart girdi olarak sunmuştuk. Aslında bu dosyayı hiç oluşturmadan aşağıdaki gibi bir yöntem de izleyebilirdik.

```
[root@emre ~]# mysql <<< 'show databases'
Database
information_schema
veriteknik
sanallastirma.com
mysql
wordpress
```

Hatırlarsanız bu bölümde `cat` programına parametrik kullanım ve standart girdi ile veri sağlanmasının kullanıcı açısından pek bir fark yaratmadığını, ancak ufak bir farklılığı olduğundan bahsetmiştik.

`cat dosya-ismi` kullanımında, önce `cat` programı çalıştırılır, `cat` programı da parametreleri hangi sırada nasıl okuyorsa ona göre ilgili işlemleri yapar. Ancak `cat < dosya-ismi` kullanımında, kabuk \(_shell_\) önce `dosya-ismi` dosyasını yüklemeye çalışır, eğer bu dosya mevcut değil veya kullanıcı tarafından erişilemez durumdaysa, hata verir ve `cat` programını hiç çalıştırmaz bile. Bu, özellikle `cat` gibi küçük programlarda hissedilmeyen etkilere sebep olur ancak `mysql` veya çok daha büyük bir programın her defasında boş yere çağırılmasına engel olabileceği için, aslında kabuk programlamada mümkünse kullanılması gereken yöntemlerden birisidir ve doğru kullanıldığında bazı noktalarda sisteminizin daha verimli çalışmasını sağlayabilir.

Standart girdinin **&lt;** notasyonu ile kullanımı, yıllarca UNIX sistemler yönetmiş bir kişinin bile çok nadir karşılaştığı durumlar olabilir. Bu kitapta da özellikle **crontab** ve **netcat** bölümlerinde birkaç örneği dışında denk gelmemeniz olası. Ancak standart girdinin ne işe yaradığının anlaşılması, özellikle birazdan göreceğimiz **pipe** mekanizmasının anlaşılmasında belkemiği görevi görmektedir.

