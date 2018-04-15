# MySQL servisini çalıştırmak

MySQL servisini kontrol etmek için,

`sudo systemctl status mysql` komutunu kullanabiliriz.

```bash
[celep@veriteknik ~]$ sudo systemctl status mysql
● mysqld.service - MySQL Community Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2018-02-27 10:59:37 UTC; 1min 13s ago
  Process: 3816 ExecStartPost=/usr/bin/mysql-systemd-start post (code=exited, status=0/SUCCESS)
  Process: 3753 ExecStartPre=/usr/bin/mysql-systemd-start pre (code=exited, status=0/SUCCESS)
 Main PID: 3815 (mysqld_safe)
   CGroup: /system.slice/mysqld.service
           ├─3815 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
           └─3980 /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mysqld.log --pid-file=/var/run/mysqld/mysq...

Feb 27 10:59:34 veriteknik mysql-systemd-start[3753]: which will also give you the option of removing the test
Feb 27 10:59:34 veriteknik mysql-systemd-start[3753]: databases and anonymous user created by default.  This is
Feb 27 10:59:34 veriteknik mysql-systemd-start[3753]: strongly recommended for production servers.
Feb 27 10:59:34 veriteknik mysql-systemd-start[3753]: See the manual for more instructions.
Feb 27 10:59:34 veriteknik mysql-systemd-start[3753]: Please report any problems at http://bugs.mysql.com/
Feb 27 10:59:34 veriteknik mysql-systemd-start[3753]: The latest information about MySQL is available on the web at
Feb 27 10:59:34 veriteknik mysql-systemd-start[3753]: http://www.mysql.com
Feb 27 10:59:35 veriteknik mysqld_safe[3815]: 180227 10:59:35 mysqld_safe Logging to '/var/log/mysqld.log'.
Feb 27 10:59:35 veriteknik mysqld_safe[3815]: 180227 10:59:35 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
Feb 27 10:59:37 veriteknik systemd[1]: Started MySQL Community Server.
```

Gördüğünüz gibi sağlık bir şekilde çalışıyor. Eğer çalışmıyor olsaydı, `sudo systemctl start mysql` komutunu kullanarak başlatacaktık.

Eğer daha detaylı bilgi almak istiyorsanız `mysqladmin -u root -p version` komutunu kullanabilirsiniz.

```bash
[celep@veriteknik ~]$ mysqladmin -u root -p version
Enter password: 
mysqladmin  Ver 8.42 Distrib 5.6.39, for Linux on x86_64
Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          5.6.39
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/lib/mysql/mysql.sock
Uptime:                 7 min 0 sec

Threads: 1  Questions: 4  Slow queries: 0  Opens: 67  Flush tables: 1  Open tables: 60  Queries per second avg: 0.009
```

Şu an MySQL servisimiz çalışır halde fakat bu haliyle canlı sunucularında kullanılabilir halde değil. Güvenlik zafiyetine yer vermemek adına diğer bütün kurulumlarda olduğu gibi sistem sıkılaştırması yapmanızı ve bunun için de OWASP'ın hazırladığı MySQL sıkılaştırma dökümanını kullanmanızı tavsiye ediyoruz.

OWASP Backend Security Project MySQL Hardening,

[https://www.owasp.org/index.php/OWASP\_Backend\_Security\_Project\_MySQL\_Hardening](https://www.owasp.org/index.php/OWASP_Backend_Security_Project_MySQL_Hardening "OWASP Backend Security Project MySQL Hardening")

