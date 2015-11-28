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

