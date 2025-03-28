# Birinci Bölüm: Temel SELECT Sorguları

`SELECT` komutu, veritabanı tablolarından veri çekmek için kullanılır. En temel SQL komutlarından biridir ve çeşitli yan tümceler (clauses) ile birlikte kullanılarak veriyi filtrelemek, sıralamak ve sınırlamak mümkündür.

Bu bölümde, önceki adımlarda oluşturduğumuz `company` veritabanındaki `member` tablosunu kullanarak temel `SELECT` sorgularını inceleyeceğiz.

Örnek `member` tablosu içeriği:
```
+----+------------+-----------+----------------------+------------+---------------------+---------------------+
| id | first_name | last_name | email                | birthday   | created_at          | updated_at          |
+----+------------+-----------+----------------------+------------+---------------------+---------------------+
|  1 | Yaşar      | Celep     | celep@veriteknik.com | 1996-12-23 | 2025-03-28 06:18:00 | 2025-03-28 06:18:00 |
|  2 | Ahmet      | Yılmaz    | ahmet@example.com    | 1990-05-15 | 2025-03-28 06:18:30 | 2025-03-28 06:18:30 |
|  3 | Ayşe       | Kaya      | ayse.kaya@example.net| NULL       | 2025-03-28 06:18:30 | 2025-03-28 06:18:30 |
|  4 | Mehmet     | Demir     | mehmet@example.org   | 1985-01-30 | 2025-03-28 06:18:30 | 2025-03-28 06:18:30 |
+----+------------+-----------+----------------------+------------+---------------------+---------------------+
```

## Tüm Sütunları Seçme (`SELECT *`)

Bir tablodaki tüm sütunları ve tüm satırları getirmek için `SELECT * FROM <tablo_adı>;` kullanılır:
```sql
SELECT * FROM member; 
```
Bu komut yukarıdaki örnek tablonun tamamını döndürür.

## Belirli Sütunları Seçme

Sadece istediğiniz sütunları getirmek için, sütun adlarını virgülle ayırarak belirtin:
```sql
SELECT first_name, last_name, email FROM member;
```
Örnek Çıktı:
```
+------------+-----------+----------------------+
| first_name | last_name | email                |
+------------+-----------+----------------------+
| Yaşar      | Celep     | celep@veriteknik.com |
| Ahmet      | Yılmaz    | ahmet@example.com    |
| Ayşe       | Kaya      | ayse.kaya@example.net|
| Mehmet     | Demir     | mehmet@example.org   |
+------------+-----------+----------------------+
```

## Satırları Filtreleme (`WHERE`)

Belirli koşullara uyan satırları seçmek için `WHERE` yan tümcesi kullanılır. Çeşitli karşılaştırma ve mantıksal operatörler kullanılabilir:

*   **Eşitlik (`=`):**
    ```sql
    SELECT * FROM member WHERE first_name = 'Ahmet';
    ```
*   **Eşit Değil (`!=` veya `<>`):**
    ```sql
    SELECT email FROM member WHERE first_name != 'Ayşe';
    ```
*   **Karşılaştırma (`>`, `<`, `>=`, `<=`):**
    ```sql
    SELECT first_name, last_name FROM member WHERE birthday < '1995-01-01';
    ```
*   **Desen Eşleştirme (`LIKE`):** Metin içinde kalıp aramak için kullanılır.
    *   `%`: Sıfır veya daha fazla karakterle eşleşir.
    *   `_`: Tek bir karakterle eşleşir.
    ```sql
    -- E-postası '.com' ile bitenler
    SELECT * FROM member WHERE email LIKE '%.com'; 
    -- Adı 'A' ile başlayanlar
    SELECT * FROM member WHERE first_name LIKE 'A%'; 
    -- Soyadının ikinci harfi 'e' olanlar
    SELECT * FROM member WHERE last_name LIKE '_e%'; 
    ```
*   **Liste İçinde (`IN`):** Belirtilen değerlerden herhangi birine sahip olanları seçer.
    ```sql
    SELECT * FROM member WHERE id IN (1, 3);
    ```
*   **Aralık (`BETWEEN ... AND ...`):** Belirtilen aralıktaki değerlere sahip olanları seçer (sınırlar dahil).
    ```sql
    SELECT * FROM member WHERE birthday BETWEEN '1990-01-01' AND '1999-12-31';
    ```
*   **Boş Değer Kontrolü (`IS NULL`, `IS NOT NULL`):**
    ```sql
    -- Doğum tarihi girilmemiş olanlar
    SELECT * FROM member WHERE birthday IS NULL; 
    -- E-postası girilmiş olanlar
    SELECT * FROM member WHERE email IS NOT NULL; 
    ```
*   **Koşulları Birleştirme (`AND`, `OR`):**
    ```sql
    -- Adı 'Ahmet' VE soyadı 'Yılmaz' olanlar
    SELECT * FROM member WHERE first_name = 'Ahmet' AND last_name = 'Yılmaz'; 
    -- ID'si 1 VEYA 4 olanlar
    SELECT * FROM member WHERE id = 1 OR id = 4; 
    -- 1990'dan önce doğmuş VE e-postası '.org' ile bitenler
    SELECT * FROM member WHERE birthday < '1990-01-01' AND email LIKE '%.org'; 
    ```

## Sonuçları Sıralama (`ORDER BY`)

Sonuçları belirli bir veya daha fazla sütuna göre sıralamak için `ORDER BY` kullanılır.

*   **Artan Sıralama (`ASC` - Varsayılan):**
    ```sql
    SELECT * FROM member ORDER BY last_name ASC;
    -- veya sadece:
    SELECT * FROM member ORDER BY last_name;
    ```
*   **Azalan Sıralama (`DESC`):**
    ```sql
    SELECT * FROM member ORDER BY birthday DESC;
    ```
*   **Birden Fazla Sütuna Göre Sıralama:**
    ```sql
    -- Önce soyadına göre artan, aynı soyadına sahip olanları ada göre artan sırala
    SELECT * FROM member ORDER BY last_name ASC, first_name ASC; 
    ```

## Sonuç Sayısını Sınırlama (`LIMIT`)

Döndürülecek satır sayısını sınırlamak için `LIMIT` kullanılır. Genellikle `ORDER BY` ile birlikte kullanılarak en yüksek/düşük veya en yeni/eski kayıtları almak için kullanılır.

*   **İlk N Kaydı Alma:**
    ```sql
    -- Eklenen ilk 2 üyeyi getir (ID'ye göre sıralı varsayarsak)
    SELECT * FROM member ORDER BY id ASC LIMIT 2; 
    ```
*   **Belirli Bir Aralıktaki Kayıtları Alma (`LIMIT offset, count`):**
    Sayfalama gibi işlemler için kullanılır. `offset` atlanacak satır sayısını, `count` ise getirilecek satır sayısını belirtir (ilk satırın offset'i 0'dır).
    ```sql
    -- 3. ve 4. üyeleri getir (2 satır atla, 2 satır getir)
    SELECT * FROM member ORDER BY id ASC LIMIT 2, 2; 
    ```

Bu temel `SELECT` komutları ve yan tümceleri, veritabanlarından bilgi almanın temelini oluşturur. Daha karmaşık sorgular için `JOIN`, gruplama (`GROUP BY`), fonksiyonlar gibi ileri konular bulunmaktadır.
