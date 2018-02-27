# MySQL Veritabanı Oluşturmak

MySQL üzerindeki veritabanlarını yönetmek için bir çok yönetim aracı olsa da biz kabuk üzerinden yöneteceğiz. Yönetim araçlarının çoğu da yazacağımız komutları bizim yerimize kabuktan yazmak dışında bir işlem yapmıyor.

`mysql -u root -p` komutuyla MySQL kabuğuna bağlanalım.

-u parametresi ile kullanıcımızı, -p parametresi ile de parola ile giriş yapacağımızı belirttik.

```bash
[celep@veriteknik ~]$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 21
Server version: 5.6.39 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

Şimdi varolan veritabanlarını listelemek için `SHOW DATABASES;` komutunu kullanalım. 

> MySQL'deki her komut noktalı virgül ile biter.

```bash
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+                                                                                                                                                                                                                                                         
| information_schema |                                                                                                                                                                                                                                                         
| mysql              |                                                                                                                                                                                                                                                         
| performance_schema |                                                                                                                                                                                                                                                         
+--------------------+                                                                                                                                                                                                                                                         
3 rows in set (0.00 sec)                                                                                                                                                                                                                                                       
```

Şu an MySQL'in içinde 3 veritabanını görebiliyoruz. Biz, company adındaki kendi veritabanımızı oluşturacağız. Bunun için `CREATE DATABASE company;` komutunu kullanmamız gerekiyor.

```
mysql> CREATE DATABASE company;
Query OK, 1 row affected (0.00 sec)
```

Kodumuz başarıyla çalıştı. `SHOW DATABASES;` komutunu kullanarak kontrol edebilirsiniz. İşlemlerimizi bu veritabanı üzerinden yapacağımız için veritabanımıza bağlanalım. Bunun için `USE company;` komutunu kullanacağız.

```
mysql> USE company;
Database changed
```

Veritabanımıza bağlandık. İçerisindeki tabloları görüntülemek için SHOW TABLES; komutunu çalıştıralım.

```bash
mysql> SHOW TABLES;
Empty set (0.00 sec)

```

Gördüğünüz gibi içinde herhangi bir tablo yok. Tablo oluşturmak için `CREATE TABLE member(id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, first_name VARCHAR(20), last_name VARCHAR(20), e_mail VARCHAR(50), birthday date, reg_date TIMESTAMP);` komutunu kullanacağız. Çalıştırmadan önce bu komutu inceleyelim. Herhangi bir komutu çalıştırmadan önce okumalısınız, asla kopyala yapıştır yaparak komut çalıştırmayın.

`CREATE TABLE person(`  person adında bir tablo oluşturacağımızı belirttik. Parantez içerisinde de kolonları ve kolonların özelliklerini belirtmemizi bekliyorlar.

`id INT NOT NULL PRIMARY KEY AUTO_INSCREMENT,` bu satırda şunu şunu şunu yaptık.

`first_name VARCHAR(20), last_name VARCHAR(20), e_mail VARCHAR(50),` burada en fazla 20 karakter olabilecek isim ve soyisim kolonlarını, en fazla 50 karakter olabilecek e-posta kolonuna ve

`birthday date,` MySQL, bu kolona girilecek verinin YYYY-MM-DD formatında olmasını bekliyor.

`reg_Date TIMESTAMP);`  kullanıcının kayıt olduğu tarihi zaman damgasıyla tabloya kaydediyoruz.MySQL sunucu saatine göre, her satırda yıl-ay-gün saat-dakika-saniye olarak bu veriyi otomatik olarak bu bilgiyi kaydediyor.

```bash
mysql> CREATE TABLE member(id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
     > first_name VARCHAR(20), last_name VARCHAR(20), e_mail VARCHAR(50),
     > birthday date, reg_date TIMESTAMP);
Query OK, 0 rows affected (0.03 sec)
```

Tablomuzu başarıyla oluşturduk.

`DESCRIBE member;` komutunu kullanarak tablomuzun içindeki kolonları görebiliriz.

```
mysql> DESCRIBE member;
+------------+-------------+------+-----+-------------------+-----------------------------+
| Field      | Type        | Null | Key | Default           | Extra                       |
+------------+-------------+------+-----+-------------------+-----------------------------+
| id         | int(11)     | NO   | PRI | NULL              | auto_increment              |
| first_name | varchar(20) | YES  |     | NULL              |                             |
| last_name  | varchar(20) | YES  |     | NULL              |                             |
| e_mail     | varchar(50) | YES  |     | NULL              |                             |
| birthday   | date        | YES  |     | NULL              |                             |
| reg_date   | timestamp   | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
+------------+-------------+------+-----+-------------------+-----------------------------+
6 rows in set (0.01 sec)
```





