# Sıkıştırılmış Dosyalar

## tar Dosyaları

TAR dosyaları, birden fazla dosyanın tek bir dosya haline getirilmesine yarar. Eskiden daha yaygın kullanılan kasetler, özellikle dosyaların arşivlenmesi için sıralı okuma/yazma işlemi yapmaktadır. Bu tip cihazlara dosyaları aralarında boşluk olmadan, sıralı bir biçimde okuyup yazabilmek için dosyaları bir araya getirip tek bir dosya haline getirmek gerekmektedir. tar programı TAR dosyası oluşturarak bu işi yapar. İsmi bu yüzden **T**ape **AR**chive sözcüklerinin kısaltmasından gelmektedir. Oluşturulan dosyalara genellikle _tarball_ denilir. Burada ufak bir kelime oyunu yapılmaktadır. Tarball aslında denizde veya okyanusta katılaşarak topaklaşan petrol kalıntısına denilir. Bu tip topaklaşmalar sırasında petrol, civarındaki başka parçacıkları da bir araya getirdiği için bu benzetme yapılmıştır.

TAR dosyaları, birden fazla dosyayı bir araya getirmek için kullanılır, ancak sıkıştırma gerçekleştirmezler. tar programı UNIX'in 7. versiyonunda geliştirilmiştir ve ilerileyen yıllarda tar dosyalarının yapısı standartlaştırılmıştır. Her ne kadar tar dosyaları sıkışmış dosyalar olmasa da, tar programı oluşturduğu dosyayı daha sonra sıkıştırabilecek, veya sıkıştırılmış tar dosyalarını açabilecek yeteneklere sahiptir.

### tarball

Basit bir tarball dosyası aşağıdaki gibi oluşturulabilir.

```bash
tar -cvf tarball.tar 03-debug.txt putty.log
```

Burada `03-debug.txt` ve `putty.log` dosyalarını birleştirip, `tarball.tar` isimli bir dosya oluşturulmaktadır. `-c` parametresi _create_'in kısaltması olarak, bir tar dosyası oluşturulacağını belirtmektedir. `-v` parametresi, hemen her GNU/Linux programında olduğu gibi _verbose_ anlamına gelir, programın yaptığı işlem hakkında bilgi vermesini sağlar. `-f` parametresi ise kendisinden sonra dosya isminin belirtileceği anlamıan gelmektedir, _file_ sözcüğünün kısaltmasıdır.

```bash
tar -xvf tarball.tar
```

Yukarıdaki örnekteyse, _create_ yerine _extract_'in kısaltması olan `-x` parametresi kullanılmıştır. Tahmin edeceğiniz üzere burada da sadece `tarball.tar` dosyası "açılmaktadır".

```bash
tar -xvf tarball.tar putty.log
```

Eğer `tarball.tar` dosyası içinden bütün dosyaları değil de, sadece `putty.log` dosyasını çıkarmak istersek, yukarıdaki gibi çıkartılacak dosyayı parametre olarak sağlayabiliriz.

Yukarıdaki gibi, bir tarball içinden sadece belirli bir dosyayı çıkarmak istiyorsak, ilgili tarball'un içeriğini bilmemiz gerekir. Bunun için _list_'in kısaltması olarak kullanılan `-t` parametresini aşağıdaki gibi kullanmak yeterli olacaktır.

```bash
eaydin@dixon ~/calisma/zip $ tar -tvf tarball.tar 
-rwxrwxr-- eaydin/eaydin 20728203 2015-11-20 16:33 03-debug.txt
-rwxrwxr-- eaydin/eaydin   112548 2015-11-20 17:49 putty.log
```

Oluşturulan tarball dosyalarında, dosyaların izinleri korunmaz. Mevcut sistemdeki izinleri korumasını istersek, _preserve permissions_'ın kısaltması olan `-p` parametresini de eklemek gerekecektir.

```bash
tar -cvfp tarball.tar *.log
```

Mevcut tar dosyasına başka dosya eklemek için, _append_ anlamına gelen `-r` parametresi kullanılır.

```bash
tar -rf tarball.tar 03-debug.txt
```

Unutulmaması gereken nokta, tarball dosyasına yeni bir dosya eklemek için, ilgili tarball'un sıkıştırılmış olmaması gerekir. Sıkıştırılmış dosyalara \(tar.gz, tar.bz2\) sonradan dosya ekleyemezsiniz, sıkıştırma algoritmalarının doğası gereği, dosyayı yeniden oluşturmanız gerekir.

### tar.gz

Oluşturulacak tarball dosyasının sıkıştırılmış bir dosya olacağını belirtmek için, daha önce kullandığımız standart parametreler arasına, _zip_'in kısaltması olarak `-z` eklenmesi gerekir.

```bash
tar -cvzf tarball.tar.gz 03-debug.txt putty.log
```

Benzer şekilde sıkıştırılmış bir dosyayı açacağımız zaman da `-z` ile tar programına açacağı dosyanın sıkıştırılmış bir dosya olduğunu belirtmek gerekir.

```bash
tar -xvzf tarball.tar.gz
```

Bu parametre ile kullanılan algoritma, GNU Zip algoritması olduğu için, aslında arka planda birazdan göreceğimiz `gzip` programını kullanır. Bu yüzden genellikle GNU Zip ile sıkıştırılmış tarball dosyalarına `.tar.gz` uzantısı verilir.

### tar.bz2

```bash
tar -cvjf tarball.tar.bz2 03-debug.txt putty.log
```

```bash
tar -xvjf tarball.tar.bz2
```

## Sıkıştırılmış Dosyalar

### gzip

```bash
gzip putty.log
```

Sıkıştırılmamış dosyayı siler.

Veya

```bash
gzip -c putty.log > putty.log.gz
```

```bash
gunzip putty.log.gz
```

```bash
gzip -d putty.log.gz
```

### bz2

```bash
bzip2 putty.log
```

Sıkıştırılmamış dosyayı siler.

```bash
bunzip putty.log.bz2
```

```bash
bzip2 -d putty.log.bz2
```

tar, gz, bz2 dosyaları için kullanabileceğiniz bir cheat-sheet [şurada](http://www.cyberciti.biz/howto/question/general/compress-file-unix-linux-cheat-sheet.php) mevcut.

### zip ve unzip

```bash
zip log-dosyalari.zip *
```

```bash
eaydin@dixon ~/calisma/zip $ unzip -l log-dosyalari.zip 
Archive:  log-dosyalari.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
 20728203  2015-11-20 16:33   03-debug.txt
   112548  2015-11-20 17:49   putty.log
---------                     -------
 20840751                     2 files
```

```bash
 eaydin@dixon ~/calisma/zip $ unzip log-dosyalari.zip -d yeni-dizin
Archive:  log-dosyalari.zip
  inflating: yeni-dizin/03-debug.txt  
  inflating: yeni-dizin/putty.log
```

### rar ve unrar

```bash
rar a arsiv.rar *.log
```

```bash
eaydin@dixon ~/calisma/zip $ unrar l arsiv.rar 

UNRAR 5.00 beta 8 freeware      Copyright (c) 1993-2013 Alexander Roshal

Archive: arsiv.rar
Details: RAR 4

 Attributes      Size    Date   Time   Name
----------- ---------  -------- -----  ----
 -rwxr-xr--  20728203  03-12-15 10:51  debug.log   
 -rwxr-xr--    112548  20-11-15 17:49  putty.log   
----------- ---------  -------- -----  ----
             20840751                  2
```

```bash
unrar e arsiv.rar
```

## Z Komutları

GNU/Linux sistemlerde özellikle log dosyalarının sıkıştırılarak saklanması yaygın tekniklerdir. Sıkıştırılmış bu dosyalar içinde arama yapmak gerekebilir. Bunun için sıkça kullanılan komutların **z** ile bağlayan versiyonları geliştirilmiştir.

`zcat`, `zless`, `zmore`, `zgrep`, `zegrep`, `zdiff` komutlarını bu tip dosyalar üzerinde çalışmak için kullanabilirsiniz.

