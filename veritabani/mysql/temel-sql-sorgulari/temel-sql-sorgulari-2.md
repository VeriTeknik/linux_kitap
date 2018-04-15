# Birinci bölüm

## SELECT

Veritabanından kayıtları okurken bu komutu kullanıyoruz.

`SELECT * FROM authors;`

Bu komut sayesinde, authors tablosundaki bütün kayıtları okuyabildik.

Eğer sadece ad soyad kayıtlarını çekmek istiyorsak kolon isimlerini kullanmamız yeterli oluyor.

`SELECT first_name, last_name FROM authors;`

## WHERE

Farkettiyseniz SELECT'i kullanarak çok fazla veri alıyoruz. Fakat bu verilerin hepsini istemediğimiz zamanlar olabilir. 

Örneğin 

### LIKE

```
SELECT * FROM authors WHERE email LIKE '%.net';
```



```
 mysql> SELECT * FROM authors WHERE email LIKE "%.net";
+----+------------+-----------+---------------------------+------------+---------------------+
| id | first_name | last_name | email                     | birthdate  | added               |
+----+------------+-----------+---------------------------+------------+---------------------+
|  3 | Vern       | Rosenbaum | kozey.stone@example.net   | 2004-04-09 | 1980-04-30 17:40:07 |
|  5 | Leif       | Stracke   | kuhlman.sofia@example.net | 2006-07-02 | 1975-12-19 21:28:33 |
| 12 | Webster    | Weimann   | umarks@example.net        | 1989-12-17 | 2004-12-28 22:35:38 |
+----+------------+-----------+---------------------------+------------+---------------------+
3 rows in set (0.00 sec)
```

### CONTAINS





Diyelim ki elimizdeki tabloya bir kolon daha eklemek istiyoruz. Bunun için kullanacağımız komut ise şu oluyor;

`ALTER TABLE veritabanının_adı ADD COLUMN kolonun_adı int NOT NULL;`

