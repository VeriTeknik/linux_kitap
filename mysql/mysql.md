# MySQL

MySQL,  ilişkisel veritabanı yönetim sistemidir. GPLv2 ile lisanslanmıştır.



CentOS 7'de MySQL repo bulunmamaktadır, bunun yerine MariaDB kullanılmaktadır ancak MariaDB de Mysql ile aynı olsa da en son sürümü 5.2'dir.

CentOS 7'de mysql'i indirebilmek için MySQL sitesinden repo rpm kurulumunu indirmeniz gerekmektedir.

```bash
rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
yum install mysql-server
```

MariaDB ile devam etmek isterseniz, yukarıdakiler yerine şunu yazabilirsiniz:

```bash
yum install mariadb mariadb-server
```

```bash
systemctl enable mysql.service
systemctl start mysql.service
```

MySQL'i compile etmeyiniz, compile edilen MySQL'in rpm paketlerinden daha yavaş olduğu izlenmektedir, rpm paketleri optimizedir.

Yükleme tamamlanınca mysql\_secure\_installation komutunu çalıştırınız:

```bash
mysql_secure_installation 
/usr/bin/mysql_secure_installation: line 379: find_mysql_client: command not found

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): BOŞ BIRAKINIZ
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] Y
New password: parola123
Re-enter new password:parola123 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] Y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] Y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] Y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] Y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```



