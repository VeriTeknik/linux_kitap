# Veritabanları

Veritabanları, yapılandırılmış bilgileri (verileri) depolamak, yönetmek, sorgulamak ve güncellemek için kullanılan organize sistemlerdir. Modern uygulamaların ve web sitelerinin temel taşlarından biridir.

## Neden Veritabanı Kullanılır?

*   **Veri Kalıcılığı:** Uygulama kapansa veya sistem yeniden başlasa bile verilerin saklanmasını sağlar.
*   **Veri Bütünlüğü:** Belirlenen kurallar (kısıtlamalar, veri tipleri) aracılığıyla verinin doğruluğunu ve tutarlılığını korur.
*   **Verimli Erişim:** Büyük miktarda veri içinden istenen bilgiye hızlı ve verimli bir şekilde erişmek için indeksleme ve sorgulama mekanizmaları sunar.
*   **Eşzamanlılık Kontrolü:** Birden fazla kullanıcının veya uygulamanın aynı anda veritabanına güvenli bir şekilde erişmesini ve değişiklik yapmasını yönetir.
*   **Güvenlik:** Kullanıcı yetkilendirme ve erişim kontrolü mekanizmaları ile verilere kimin erişebileceğini ve hangi işlemleri yapabileceğini sınırlar.
*   **Yedekleme ve Kurtarma:** Veri kaybına karşı yedekleme ve kurtarma mekanizmaları sunar.

## Veritabanı Türleri

Birçok farklı veritabanı modeli bulunmaktadır, ancak en yaygın olanlardan bazıları şunlardır:

*   **İlişkisel Veritabanları (Relational Databases - RDBMS):** Verileri satırlar ve sütunlardan oluşan tablolarda saklar. Tablolar arasında ilişkiler kurulabilir. SQL (Structured Query Language) sorgulama dili kullanılır. En yaygın kullanılan türdür. Örnekler: MySQL, MariaDB, PostgreSQL, SQLite, Oracle Database, Microsoft SQL Server.
*   **NoSQL Veritabanları:** İlişkisel modelin kısıtlamalarına alternatif olarak geliştirilmişlerdir. Farklı veri modelleri kullanırlar:
    *   **Belge Tabanlı (Document):** Verileri JSON/BSON benzeri belgelerde saklar (örn. MongoDB, Couchbase).
    *   **Anahtar-Değer (Key-Value):** Basit anahtar-değer çiftleri olarak saklar (örn. Redis, Memcached).
    *   **Sütun Ailesi (Column-Family):** Büyük veri setleri için optimize edilmiştir (örn. Cassandra, HBase).
    *   **Grafik (Graph):** Veriler arasındaki ilişkileri ön plana çıkarır (örn. Neo4j, ArangoDB).

## İlişkisel Veritabanı Nedir?

İlişkisel veritabanları, verileri önceden tanımlanmış bir şemaya göre yapılandırılmış tablolarda saklar.
*   **Tablolar (Tables):** Belirli bir türdeki verileri tutar (örn. 'kullanicilar', 'urunler', 'siparisler').
*   **Satırlar (Rows/Records):** Tablodaki her bir kaydı temsil eder (örn. belirli bir kullanıcı, belirli bir ürün).
*   **Sütunlar (Columns/Fields/Attributes):** Bir kaydın belirli bir özelliğini temsil eder (örn. 'kullanici_adi', 'email', 'urun_fiyati'). Her sütunun belirli bir veri tipi vardır (örn. INTEGER, VARCHAR, DATE).
*   **Anahtarlar (Keys):**
    *   **Birincil Anahtar (Primary Key):** Tablodaki her satırı benzersiz şekilde tanımlayan bir veya daha fazla sütun.
    *   **Yabancı Anahtar (Foreign Key):** Bir tablodaki sütunun, başka bir tablonun birincil anahtarına başvurarak tablolar arasında ilişki kurmasını sağlar.
*   **SQL (Structured Query Language):** İlişkisel veritabanlarında veri tanımlamak (CREATE TABLE), veri işlemek (INSERT, UPDATE, DELETE) ve veri sorgulamak (SELECT) için kullanılan standart dildir.

Bu kitapta, Linux ortamında yaygın olarak kullanılan ilişkisel veritabanlarından **MySQL** (ve onun topluluk tarafından geliştirilen popüler bir çatalı olan **MariaDB**) üzerine odaklanılacaktır. Ayrıca, güçlü ve açık kaynaklı bir alternatif olan **PostgreSQL** de sıklıkla tercih edilen bir seçenektir.

*   [MySQL / MariaDB](mysql/README.md)
