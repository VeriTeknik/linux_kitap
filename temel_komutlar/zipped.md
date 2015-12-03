# Sıkıştırılmış Dosyalar

## tar Dosyaları

### tar ball

```bash
tar -cvf tarball.tar 03-debug.txt putty.log
```

```bash
tar -xvf tarball.tar
```

```bash
tar -xvf tarball.tar putty.log
```

```bash
eaydin@dixon ~/calisma/zip $ tar -tvf tarball.tar 
-rwxrwxr-- eaydin/eaydin 20728203 2015-11-20 16:33 03-debug.txt
-rwxrwxr-- eaydin/eaydin   112548 2015-11-20 17:49 putty.log
```

Dosya izinlerini korumak (preserve) için

```bash
tar -cvfp tarball.tar *.log
```

Mevcut tar dosyasına başka dosya eklemek için (append)

```bash
tar -rf tarball.tar 03-debug.txt
```

Sıkıştırılmış dosyalara (tar.gz, tar.bz2) dosya ekleyemezsiniz.

### tar.gz

```bash
tar -cvzf tarball.tar.gz 03-debug.txt putty.log
```

```bash
tar -xvzf tarball.tar.gz
```

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

### zcat
### zless / zmore
### zgrep / zegrep
### zdiff