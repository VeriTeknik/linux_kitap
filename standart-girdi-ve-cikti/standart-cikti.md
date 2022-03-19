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

