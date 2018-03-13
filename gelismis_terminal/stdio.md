# Standart Girdi ve Çıktı Yönlendirme

GNU/Linux ve benzeri POSIX sistemlerin en güçlü olduğu yönlerden birisi, programların standard girdi ve çıktılarının yönlendirilmesidir. Özellikle birden fazla programın işlemlerini birbirlerine kolayca gönderebilme gücü sağlayan bu yöntem, aslında UNIX'in oluşturulduğu dönemdeki temel felsefeleri en iyi yansıtan özelliklerinden biridir.

1978'de yayımlanan The Bell System Technical Journal'ın UNIX Time-Sharing System dokümanının önsözünde David McIlroy, UNIX'in tasarlanması ve kullanılmasında temel alınan kritik noktaları 4 madde halinde listeler. Bu maddelerden ilki ilerleyen yıllarda çok popüler olmuştur. Maddenin tam hali şu şekildedir:

> Make each program do one thing well. To do a new job, build afresh rather than complicate old programs by adding new "features".

Bu maddede McIlroy, bir program yazılırken, bu programın \_bir şeyi iyi yapması gerektiğini** **\_vurgular. Yeni bir iş yapılacağı zaman, mevcut programa yeni "özellikler" eklemek yerine, yeni bir program yazılmasının daha doğru olduğunu savunur.

Önsözde paylaşılan maddelerden ikincisinin tam hali ise şu şekildedir:

> Expect the output of every program to become the input to another, as yet unknown, program. Don't clutter output with extraneous information. Avoid stringently columnar or binary input formats. Don't insist on interactive intput.

Bu madde başlı başına çok fazla şey söyler, ve bir süre GNU/Linux sistemler üzerinde yazılmış programları kullanan herkes, aslında bu maddelerin çoğu programda uygulandığını görecektir. Öncelikle bu bölümde bahsedeceğimiz girdi ve çıktıların yönlendirilmesi gerekliliği, maddenin ilk cümlesinde ifade edilmiştir. McIlroy, her programın çıktısının aynı zamanda girdi olarak tasarlanması gerektiğini, hatta ne iş yaptığını bilmediği bir programın girdisi olarak kullanılabileceğinin gözönünde bulundurulması gerektiğini belirtir. Çıktıların gereksiz bilgilerle kirletilmemesi gerektiğini, kolon bazlı girdilerin veya binary girdilerin dayatılmasından kaçınılması gerektiğini ifade eder. Ayrıca gerekmediğinde _etkileşimli girdiden_ kaçınılması gerektiğini belirtir. Çünkü etkileşimli girdi, bir programa standart girdi olarak veri göndermenin zorlaşması demektir.

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



