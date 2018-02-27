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

    INSERT INTO `member` (`id`,`first_name`,`last_name`,`e_mail`,`birthday`,`reg_date`) VALUES(NULL,"Yasar","Celep","celep@veriteknik.com","1996-12-23",NULL);
    INSERT INTO `member` (`id`,`first_name`,`last_name`,`e_mail`,`birthday`,`reg_date`) VALUES(NULL,"Zelis","Yildiz","ceku@riseup.net","1997-02-01",NULL); 
    Query OK, 2 row affected (0.01 sec)

Hadi, tablomuzu kontrol edelim.

```
mysql> SELECT * from member;
+----+------------+-----------+----------------------+------------+---------------------+
| id | first_name | last_name | e_mail               | birthday   | reg_date            |
+----+------------+-----------+----------------------+------------+---------------------+
|  1 | Yasar      | Celep     | celep@veriteknik.com | 1996-12-23 | 2018-02-27 13:05:57 |
|  2 | Zelis      | Yildiz    | ceku@riseup.net      | 1997-02-01 | 2018-02-27 18:11:59 |
+----+------------+-----------+----------------------+------------+---------------------+
2 rows in set (0.00 sec)
```

Gördüğünüz üzere kayıt tarihini ve id'i otomatik olarak kendisi bastı.

