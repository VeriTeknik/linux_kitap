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

    mysql> INSERT INTO `member` (`id`,`first_name`,`last_name`,`e_mail`,`birthday`,`reg_date`) VALUES(NULL,"Yasar","Celep","celep@veriteknik.com","1996-12-23",NULL);
    Query OK, 1 row affected (0.01 sec)



Hadi, tablomuzu kontrol edelim.

```
mysql> SELECT * from member;
+----+------------+-----------+----------------------+------------+---------------------+
| id | first_name | last_name | e_mail               | birthday   | reg_date            |
+----+------------+-----------+----------------------+------------+---------------------+
|  1 | Yasar      | Celep     | celep@veriteknik.com | 1996-12-23 | 2018-02-27 13:05:57 |
+----+------------+-----------+----------------------+------------+---------------------+
1 row in set (0.00 sec)
```



