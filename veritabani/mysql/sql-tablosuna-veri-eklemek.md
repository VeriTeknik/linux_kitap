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

Şimdi bu tabloya `INSERT INTO` komutu ile veri ekleyelim.

**Tek Satır Ekleme:**

`AUTO_INCREMENT` ve `DEFAULT` değerleri olan sütunları (bizim örneğimizde `id`, `created_at`, `updated_at`) belirtmemize gerek yoktur, veritabanı bunları otomatik olarak halleder. Sadece değer girmek istediğimiz sütunları belirtmemiz yeterlidir:

```sql
INSERT INTO member (first_name, last_name, email, birthday) 
VALUES ('Yaşar', 'Celep', 'celep@veriteknik.com', '1996-12-23'); 
```

Başarılı olursa şöyle bir çıktı alınır:
```
Query OK, 1 row affected (0.02 sec)
```

**Birden Fazla Satır Ekleme:**

Tek bir `INSERT INTO` komutu ile birden fazla satır eklemek için `VALUES` kısmından sonra değer gruplarını virgülle ayırarak listeleyebilirsiniz:

```sql
INSERT INTO member (first_name, last_name, email, birthday) VALUES 
('Ahmet', 'Yılmaz', 'ahmet@example.com', '1990-05-15'),
('Ayşe', 'Kaya', 'ayse.kaya@example.net', NULL), -- Doğum tarihi boş olabilir
('Mehmet', 'Demir', 'mehmet@example.org', '1985-01-30');
```
```
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0
```

**Veriyi Kontrol Etme (`SELECT`):**

Eklenen verileri görmek için `SELECT * FROM <tablo_adı>;` komutunu kullanabiliriz. `*` sembolü "tüm sütunları" getir anlamına gelir.

```sql
SELECT * FROM member;
```

Örnek Çıktı:
```
+----+------------+-----------+----------------------+------------+---------------------+---------------------+
| id | first_name | last_name | email                | birthday   | created_at          | updated_at          |
+----+------------+-----------+----------------------+------------+---------------------+---------------------+
|  1 | Yaşar      | Celep     | celep@veriteknik.com | 1996-12-23 | 2025-03-28 06:18:00 | 2025-03-28 06:18:00 |
|  2 | Ahmet      | Yılmaz    | ahmet@example.com    | 1990-05-15 | 2025-03-28 06:18:30 | 2025-03-28 06:18:30 |
|  3 | Ayşe       | Kaya      | ayse.kaya@example.net| NULL       | 2025-03-28 06:18:30 | 2025-03-28 06:18:30 |
|  4 | Mehmet     | Demir     | mehmet@example.org   | 1985-01-30 | 2025-03-28 06:18:30 | 2025-03-28 06:18:30 |
+----+------------+-----------+----------------------+------------+---------------------+---------------------+
4 rows in set (0.00 sec)
```

Gördüğünüz gibi `id` sütunu otomatik olarak arttı ve `created_at`/`updated_at` sütunları otomatik olarak o anki zaman damgasıyla dolduruldu. `birthday` sütununa `NULL` değer girilebildi.
