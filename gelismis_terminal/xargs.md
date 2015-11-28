# xargs

xargs komutu ile bir komuttan gelen standart çıktıyı başka bir komuta argüman olarak yönlendirebilirsiniz.

Örneğin bir dizindeki (ve alt dizinlerindeki) Python dosyalarının kaçar satırdan oluştuğunu görmek istersek

```bash
eaydin@dixon ~/devel/gkmv1 $ find . -name "*.py" | xargs wc -l
   697 ./cardread.py
    83 ./checkcronjobs.py
   637 ./GKM/commands.py
    91 ./GKM/communication.py
    47 ./GKM/crc8dallas.py
    33 ./GKM/database.py
   165 ./GKM/ethernet.py
    47 ./GKM/log.py
    62 ./GKM/messaging.py
    43 ./GKM/queue.py
    61 ./GKM/readconfig.py
    86 ./GKM/serialupdate.py
   132 ./GKM/wiegand.py
     0 ./GKM/__init__.py
   152 ./listener.py
    23 ./resetfoodgkm.py
    53 ./tools/gkm-watchdog.py
   867 ./tools/kontrol.py
  3279 total
  ```
  
  Gördüğünüz gibi burada sondaki satır **total** değerini veriyor, Bu aslında ```wc``` programının bir özelliği, en sonda kendisine argüman olarak verilen bütün dosyaların satır sayılarının toplamını da verir. Örneğin şöyle bir kullanım yapsaydık da çalışacaktı, ama alt dizinleri aramayacaktı.
  
  ```bash
  eaydin@dixon ~/devel/gkmv1 $ wc -l *.py
  697 cardread.py
   83 checkcronjobs.py
  152 listener.py
   23 resetfoodgkm.py
  955 total
  ```
  
  İçindeki boşluk karakteri geçen dosya isimlerinin kullanılabilmesi için **find** programı **-print0** argümanı ile çalıştırılmalı ve **xargs** programı ile **-0** argümanı kullanılmalıdır.
  
  ```bash
  find . -name "*.py" -print0 | xargs -0 wc -l
  ```
  
  ## Argümanların Farklı Yere Yerleştirilmesi
  
  Farkındaysanız xargs'ın yaptığı, standart girdiden gelenleri ilgili komutun sonuna yerleştirmek oldu. Ancak bazı durumlarda gelenleri sona değil, başka bir yere yerleştirmek isteyebiliriz. Aşağıdaki örnek açıklayıcı olacaktır.
  
```bash
[root@test etc]# find /etc -maxdepth 1 -name "*.conf" -print0 | xargs -0 -I % echo Bulunan % dosyası
Bulunan /etc/libuser.conf dosyası
Bulunan /etc/host.conf dosyası
Bulunan /etc/yum.conf dosyası
Bulunan /etc/sudo.conf dosyası
Bulunan /etc/dracut.conf dosyası
Bulunan /etc/nsswitch.conf dosyası
Bulunan /etc/sudo-ldap.conf dosyası
Bulunan /etc/krb5.conf dosyası
Bulunan /etc/logrotate.conf dosyası
Bulunan /etc/cgrules.conf dosyası
Bulunan /etc/libaudit.conf dosyası
Bulunan /etc/resolv.conf dosyası
Bulunan /etc/ntp.conf dosyası
Bulunan /etc/mdadm.conf dosyası
Bulunan /etc/cgconfig.conf dosyası
Bulunan /etc/mke2fs.conf dosyası
Bulunan /etc/ld.so.conf dosyası
Bulunan /etc/rsyslog.conf dosyası
Bulunan /etc/gai.conf dosyası
Bulunan /etc/cgsnapshot_blacklist.conf dosyası
Bulunan /etc/sysctl.conf dosyası
Bulunan /etc/sestatus.conf dosyası
Bulunan /etc/grub.conf dosyası
```

Yukarıda **-I** argümanından sonra **%** kullandığımız için, komut içerisinde **%** görünen yere standart girdiden gelen konulacaktı. Örneğin **%** yerine **gelen** yazabilirdik.


```bash
[root@test etc]# find /etc -maxdepth 1 -name "*.conf" -print0 | xargs -0 -I gelen echo Bulunan gelen dosyası
```

## Argümanları Gruplandırmak

Bazı programlar bütün argümanları aldığında işe yaramazlar çünkü sınırlı sayıda argüman ile çalışırlar. Örneğin **diff** programı sadece iki dosya ismini argüman olarak alır ve bunları karşılaştırır. Öyleyse onlarca dosyayı diff ile kullanmak için, xargs'e argümanları gruplandırmasını söylememiz gerekir.

```bash
eaydin@dixon ~/calisma $ ls *.txt
test2.txt  test3.txt  test4.txt  test.txt
eaydin@dixon ~/calisma $ diff *.txt
diff: extra operand 'test4.txt'
diff: Try 'diff --help' for more information.
eaydin@dixon ~/calisma $ ls *.txt | xargs -L 2 diff
2a3,4
> ucuncu satir
> dorduncu satir
4,5d3
< dorduncu satir
< besinci satir
```

Yukarıdaki işlemin yaptığı, gelen ls çıktısını 2'li gruplar halinde diff programına sunmak olduk. Böylece diff önce test2.txt ve test3.txt dosyalarını karşılaştırıyor, ardından test4.txt ve test.txt dosyalarını karşılaştırıyor.

Benzer şekilde sadece çıktıyı gruplandırmak isteseydik, **-n** argümanını kullanabilirdik.

```bash
eaydin@dixon ~/calisma $ echo {0..9} | xargs -n 3
0 1 2
3 4 5
6 7 8
9
```

## Maksimum Argüman Sayısı

Kabuk ortamınızda kullanabileceğiniz maksimum argüman sayısı sisteminize göre değişiklik gösterebilir. Aşağıdaki komutu yazarak öğrenebilirsiniz.

```bash
eaydin@dixon ~/calisma $ getconf ARG_MAX
2097152
```

Öte yandan xargs kendi limitlerine sahiptir. Bu limit normalde 4096 olarak belirlenmiştir ancak parametre kullanımıyla aşılabilir. **-s** parametersiyle bu limiti dilediğiniz sayıya (sistem limitleriniz dahilinde olmak şartıyla) çekebilirsiniz. Böylece, örneğin çok sayıda dosyanın bulunduğu dizinleri tararken limitlere takılmazsınız.

```bash
find / -path "/mnt/backup" -prune -o -iname "*.jpg" -o -iname "*.jpeg" -print0 | xargs -0 -s 2000000 -I % cp % /mnt/backup
```

Yukarıdaki komut, ```/mnt/backup``` dizini hariç bütün dizinlerde **jpg** ve **jpeg** uzantılı (büyük/küçük harf duyarsız) dosyaları ```/mnt/backup``` dizinine kopyalar.