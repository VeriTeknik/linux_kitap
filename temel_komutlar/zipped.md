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

### gzip

```bash
gzip putty.log
```

Sıkıştırılmamış dosyayı siler.


```bash
gunzip putty.log.gz
```

```bash
gzip -d putty.log.gz
```

```bash
gzip -l putty.log.gz
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

