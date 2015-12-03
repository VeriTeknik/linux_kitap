# Sıkıştırılmış Dosyalar

## tar ball

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

## tar.gz

```bash
tar -cvzf tarball.tar.gz 03-debug.txt putty.log
```

```bash
tar -xvzf tarball.tar.gz
```

## tar.bz2

```bash
tar -cvjf tarball.tar.bz2 03-debug.txt putty.log
```

```bash
tar -xvjf tarball.tar.bz2
```