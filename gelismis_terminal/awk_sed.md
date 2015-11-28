# awk ve sed

GNU/Linux üzerinde **awk** ve **sed** ikilisi, standart girdi/çıktı işlemlerinde sıkça kullanılan, metin değişikliğine yarayan programlardır.

Her ne kadar her ikisi için de kompleks programlar yazılabiliyor olsa da, bu bölümde ana hatlarıyla nasıl kullanıldıklarını ve en sık işinize yarayacak yönlerini inceleyeceğiz.

## awk

awk çoğunlukla tablarla ayrılmış verilerin belirli sütununu almakta veya sırasını değiştirmekte kullanılır. Örneğin ```ls -l``` çıktısını ayıklamak için aşağıdaki yöntem kullanılabilir.

```bash
eaydin@dixon ~/calisma $ ls -l *.txt
-rw-r--r-- 1 eaydin eaydin 33 Nov 28 14:56 test2.txt
-rw-r--r-- 1 eaydin eaydin 55 Nov 28 13:49 test3.txt
-rw-r--r-- 1 eaydin eaydin 69 Nov 28 13:49 test4.txt
-rw-r--r-- 1 eaydin eaydin 33 Nov 28 15:01 test.txt
-rw-r--r-- 1 eaydin eaydin 33 Nov 28 15:00 yedek-test2.txt
-rw-r--r-- 1 eaydin eaydin 40 Nov 28 15:00 yedek-test.txt
eaydin@dixon ~/calisma $ ls -l *.txt | awk '{print "Dosya Adı: "$9" Boyut: "$5" kB"}'
Dosya Adı: test2.txt Boyut: 33 kB
Dosya Adı: test3.txt Boyut: 55 kB
Dosya Adı: test4.txt Boyut: 69 kB
Dosya Adı: test.txt Boyut: 33 kB
Dosya Adı: yedek-test2.txt Boyut: 33 kB
Dosya Adı: yedek-test.txt Boyut: 40 kB
```

Ayracı boşluk karateri haricinde bir karakter olarak tanımlamak için **-F** argümanı kullanılır.

```bash
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd
saned:x:113:123::/home/saned:/bin/false
speech-dispatcher:x:114:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/sh
eaydin:x:1000:1000:Emre,,,:/home/eaydin:/bin/bash
nvidia-persistenced:x:115:124:NVIDIA Persistence Daemon,,,:/:/sbin/nologin
sshd:x:116:65534::/var/run/sshd:/usr/sbin/nologin
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd | awk -F":" '{print "Kullanıcı: "$1}'
Kullanıcı: saned
Kullanıcı: speech-dispatcher
Kullanıcı: eaydin
Kullanıcı: nvidia-persistenced
Kullanıcı: sshd
```

Sitem üzerinde tanımlanmış değişkenleri alıp işleyebiliriz de.

```bash
eaydin@dixon ~/calisma $ X=0
eaydin@dixon ~/calisma $ echo $X
0
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd | \
awk -F":" -v val=$X '{print val " Kullanıcı: "$1}'
0 Kullanıcı: saned
0 Kullanıcı: speech-dispatcher
0 Kullanıcı: eaydin
0 Kullanıcı: nvidia-persistenced
0 Kullanıcı: sshd
eaydin@dixon ~/calisma $ tail -n 5 /etc/passwd | \
awk -F":" -v val=$X '{print val+1 " Kullanıcı: "$1;val=val+1}'
1 Kullanıcı: saned
2 Kullanıcı: speech-dispatcher
3 Kullanıcı: eaydin
4 Kullanıcı: nvidia-persistenced
5 Kullanıcı: sshd
```