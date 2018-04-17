# SQL Tablosuna Veri Eklemek

Bundan önceki bölümde tablomuzu oluşturmuştuk.

```
mysql> SHOW TABLES;
+-------------------+
| Tables_in_company |
+-------------------+
| member            |
+-------------------+
1 row in set (0.00 sec)
```

Şimdi bu tabloya veri ekleyelim.

``INSERT INTO `member` (`id`,`first_name`,`last_name`,`e_mail`,`birthday`,`reg_date`) VALUES(NULL,"Yasar","Celep","celep@veriteknik.com","1996-12-23",NULL);``

Şöyle bir çıktı aldık;

`Query OK, 1 row affected (0.02 sec)`

Kontrol edelim.

`SHOW * FROM member;`

Bu komutun meali ise şu,  "member" tablosunda bulunan bütün kayıtları bana getir. Kullandığımız asterisk\(yıldız\) sembolü burada "ne varsa" demek.

```
+----+------------+-----------+----------------------+------------+---------------------+
| id | first_name | last_name | e_mail               | birthday   | reg_date            |
+----+------------+-----------+----------------------+------------+---------------------+
|  1 | Yasar      | Celep     | celep@veriteknik.com | 1996-12-23 | 2018-02-27 13:05:57 |
+----+------------+-----------+----------------------+------------+---------------------+
1 rows in set (0.00 sec)
```

Gördüğünüz üzere kayıt tarihini ve id'i otomatik olarak kendisi bastı.

