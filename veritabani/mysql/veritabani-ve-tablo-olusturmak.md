# Veritabanı ve Tablo Oluşturmak

MySQL veya MariaDB sunucusundaki veritabanlarını ve tabloları yönetmek için komut satırı istemcisi (`mysql` veya `mariadb`) kullanılır. Grafiksel araçlar da mevcut olsa da, temel işlemleri komut satırından yapmak önemlidir.

**MySQL/MariaDB Kabuğuna Bağlanma:**

Genellikle `root` kullanıcısı ile bağlanılır. Bağlantı yöntemi, kurulum sırasında belirlenen kimlik doğrulama yöntemine göre değişir:

*   **Şifre ile:**
    ```bash
    mysql -u root -p 
    ```
    Bu komut `root` kullanıcısının şifresini soracaktır. `-u` kullanıcı adını, `-p` şifre sorulacağını belirtir.
*   **Unix Soket Kimlik Doğrulaması ile (Yaygın):**
    Modern kurulumlarda `root` kullanıcısı genellikle şifre yerine sistemin `root` kullanıcısı ile eşleştirilir (örn. `auth_socket` veya `unix_socket` eklentisi ile). Bu durumda `sudo` kullanarak şifresiz bağlanılabilir:
    ```bash
    sudo mysql 
    # veya sudo mariadb
    ```

Başarıyla bağlandıktan sonra `mysql>` veya `MariaDB [(none)]>` gibi bir istem (prompt) görürsünüz.

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

> **Not:** MySQL/MariaDB kabuğundaki çoğu SQL komutu noktalı virgül (`;`) ile bitirilmelidir. `\g` de alternatif olarak kullanılabilir. `help;` veya `\h` komutu yardım bilgilerini gösterir. `exit` veya `quit` ile kabuktan çıkılır.

```bash
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

Şu an MySQL'in içinde varsayılan sistem veritabanlarını görüyoruz (`information_schema`, `mysql`, `performance_schema`, `sys`). Kendi veritabanımızı oluşturalım.

**Veritabanı Oluşturma (`CREATE DATABASE`):**

`company` adında yeni bir veritabanı oluşturalım. Türkçe karakterleri ve modern Unicode desteğini düzgün sağlamak için genellikle `utf8mb4` karakter seti ve uygun bir collation (örn. `utf8mb4_general_ci` veya `utf8mb4_unicode_ci`) belirtmek iyi bir pratiktir.

```sql
CREATE DATABASE company 
CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;
```
```
Query OK, 1 row affected (0.00 sec)
```
Komut başarıyla çalıştı. `SHOW DATABASES;` komutuyla `company` veritabanının eklendiğini görebilirsiniz.

**Veritabanını Seçme (`USE`):**

İşlemlerimizi bu veritabanı üzerinde yapmak için onu seçmemiz gerekir:
```sql
USE company;
```

```
mysql> USE company;
Database changed
```

Veritabanımıza bağlandık. İçerisindeki tabloları görüntülemek için SHOW TABLES; komutunu çalıştıralım.

```bash
mysql> SHOW TABLES;
Empty set (0.00 sec)
```

Gördüğünüz gibi içinde herhangi bir tablo yok. Şimdi `member` adında bir tablo oluşturalım.

**Tablo Oluşturma (`CREATE TABLE`):**

`CREATE TABLE` komutu ile tablonun adı ve içereceği sütunlar (kolonlar) tanımlanır. Her sütun için bir ad, bir veri tipi ve isteğe bağlı kısıtlamalar (constraints) belirtilir.

```sql
CREATE TABLE member (
    id INT NOT NULL AUTO_INCREMENT,         -- Otomatik artan tam sayı ID
    first_name VARCHAR(50) NOT NULL,        -- İsim (Boş olamaz)
    last_name VARCHAR(50) NOT NULL,         -- Soyisim (Boş olamaz)
    email VARCHAR(100) UNIQUE,              -- E-posta (Benzersiz olmalı)
    birthday DATE NULL,                     -- Doğum tarihi (Boş olabilir)
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, -- Kayıt oluşturma zamanı (varsayılan: şu an)
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, -- Kayıt güncelleme zamanı
    PRIMARY KEY (id)                        -- 'id' sütununu birincil anahtar yap
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci; 
```

**Açıklamalar:**

*   `id INT NOT NULL AUTO_INCREMENT`: `id` adında, tam sayı (`INT`), boş olamayan (`NOT NULL`), her yeni kayıtta otomatik olarak artan (`AUTO_INCREMENT`) bir sütun. Genellikle birincil anahtar olarak kullanılır.
*   `first_name VARCHAR(50) NOT NULL`: En fazla 50 karakter uzunluğunda metin (`VARCHAR`), boş olamaz.
*   `email VARCHAR(100) UNIQUE`: En fazla 100 karakter uzunluğunda metin, bu sütundaki değerler benzersiz (`UNIQUE`) olmalıdır (aynı e-posta iki kez girilemez).
*   `birthday DATE NULL`: Tarih (`YYYY-MM-DD` formatında), boş (`NULL`) olabilir.
*   `created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP`: Zaman damgası (`YYYY-MM-DD HH:MM:SS` formatında). Kayıt eklendiğinde varsayılan olarak o anki zaman damgasını alır (`DEFAULT CURRENT_TIMESTAMP`).
*   `updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP`: Zaman damgası. Kayıt eklendiğinde varsayılan olarak o anki zamanı alır ve kayıt her güncellendiğinde otomatik olarak o anki zaman damgasıyla güncellenir (`ON UPDATE CURRENT_TIMESTAMP`).
*   `PRIMARY KEY (id)`: `id` sütununu tablonun birincil anahtarı olarak tanımlar. Birincil anahtar, tablodaki her satırı benzersiz şekilde tanımlar ve genellikle `NOT NULL` olmalıdır.
*   `ENGINE=InnoDB`: Kullanılacak depolama motorunu belirtir. `InnoDB` varsayılan ve en yaygın kullanılan motordur; ACID uyumluluğu (transactions), satır seviyesinde kilitleme ve yabancı anahtar (foreign key) desteği sunar. Eski `MyISAM` motoru genellikle özel durumlar dışında tercih edilmez.
*   `DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci`: Tablo için varsayılan karakter setini ve karşılaştırma (collation) kurallarını belirler. `utf8mb4` modern Unicode karakterlerini (emoji dahil) destekler ve genellikle önerilir.

```
Query OK, 0 rows affected (0.03 sec)
```

Tablomuz başarıyla oluşturuldu. `SHOW TABLES;` komutu artık `member` tablosunu listelemelidir.

**Tablo Yapısını Görme (`DESCRIBE` veya `SHOW COLUMNS`):**

Oluşturulan tablonun sütunlarını ve özelliklerini görmek için `DESCRIBE` veya `SHOW COLUMNS FROM` komutları kullanılır:
```sql
DESCRIBE member;
-- veya
SHOW COLUMNS FROM member;
```

```
mysql> DESCRIBE member;
+------------+--------------+------+-----+---------------------+-------------------------------+
| Field      | Type         | Null | Key | Default             | Extra                         |
+------------+--------------+------+-----+---------------------+-------------------------------+
| id         | int          | NO   | PRI | NULL                | auto_increment                |
| first_name | varchar(50)  | NO   |     | NULL                |                               |
| last_name  | varchar(50)  | NO   |     | NULL                |                               |
| email      | varchar(100) | YES  | UNI | NULL                |                               |
| birthday   | date         | YES  |     | NULL                |                               |
| created_at | timestamp    | YES  |     | CURRENT_TIMESTAMP   |                               |
| updated_at | timestamp    | YES  |     | CURRENT_TIMESTAMP   | on update CURRENT_TIMESTAMP |
+------------+--------------+------+-----+---------------------+-------------------------------+
7 rows in set (0.00 sec)

```

Sıradaki bölümde tablomuza veri ekleyeceğiz.
