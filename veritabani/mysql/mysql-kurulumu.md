# CentOS'a MMySQL Kurulumu

CentOS repolarında MySQL bulunmuyor. Bu yüzden RPM paketini MySQL'den indirmemiz gerek.

`https://dev.mysql.com/downloads/repo/yum`

Bu adrese giriş yaparak, en güncel versiyonun ne olduğunu kontrol edelim.

CentOS 7'ye kurulum yapacağımızdan RedHat 7 için olanı kullanabiliriz. Kurulum yapmamız gereken paketin altında son paketin adı açık olarak gözüküyor, `mysql-57-community-release-el7-11.noarch.rpm` bunu kopyalayarak `https://dev.mysql.com/get/`  bu adresin sonuna ekleyebilir veya download kısmına tıklayarak, açılan sayfada "No thanks, just start my download." linkine farenin sağ tuşuyla tıklayıp "Copy Link Location"\(Bağlantı adresini kopyala\)'a tıklarsanız güncel RPM paketinin adresini kopyalamış olursunuz.

```
yum install wget
wget https://dev.mysql.com/get/mysql-57-community-release-el7-11.noarch.rpm
sudo rpm -ivh mysql-57-community-release-el7-11.noarch.rpm 
sudo yum install mysql-server
```

Bütün sorulara y ile cevap veriyoruz.

MySQL servisini çalıştıralım.

`sudo systemctl start mysqld`

Kontrol etmek için de  `sudo systemctl status mysqld` komutunu kullanmamız gerekiyor.

```
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2018-03-12 07:57:56 UTC; 1min 34s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 9008 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 8931 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 9013 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─9013 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

Mar 12 07:57:51 veriteknik systemd[1]: Starting MySQL Server...
Mar 12 07:57:56 veriteknik systemd[1]: Started MySQL Server.
```

MySQL artık çalışıyor. MySQL bizim için geçici bir parola üretti. Bunu okumak için

```
grep 'temporary password' /var/log/mysqld.log
```

Bu komutu çalıştırmamız yeterli olacak.

```
2018-03-12T07:57:52.832878Z 1 [Note] A temporary password is generated for celep@localhost: sf/alCzh9ibk
```

Bu parolayı şimdilik kaydetmemiz gerekiyor, az sonra lazım olacak.

