# MySQL ve MariaDB

MySQL, dünyanın en popüler açık kaynaklı ilişkisel veritabanı yönetim sistemlerinden (RDBMS) biridir. Özellikle web uygulamalarıyla birlikte yaygın olarak kullanılır. Oracle Corporation tarafından geliştirilmektedir.

MariaDB, MySQL'in orijinal geliştiricileri tarafından oluşturulan, topluluk tarafından geliştirilen bir çatalıdır (fork). MySQL ile yüksek düzeyde uyumluluk sunmayı hedeflerken, bazı ek özellikler ve farklı depolama motorları da içerir. Birçok Linux dağıtımı, Oracle'ın MySQL'i satın almasından sonra varsayılan "MySQL" paketi olarak MariaDB'yi sunmaya başlamıştır.

Bu bölümde, hem MySQL hem de MariaDB için geçerli olan temel kavramlar, kurulum, yönetim ve kullanım konuları ele alınacaktır. Komutlar ve yapılandırma genellikle iki sistem arasında çok benzerdir veya aynıdır.

**İncelenecek Konular:**

*   [MySQL/MariaDB Kurulumu](mysql-kurulumu.md): Paket yöneticileri ile kurulum ve ilk güvenlik ayarları.
*   [MySQL/MariaDB Servisini Çalıştırmak](mysql-servisini-calistirmak.md): `systemctl` ile servis yönetimi.
*   [Veritabanı ve Tablo Oluşturmak](veritabani-ve-tablo-olusturmak.md): Temel DDL (Data Definition Language) komutları (`CREATE DATABASE`, `CREATE TABLE`).
*   [SQL Tablosuna Veri Eklemek](sql-tablosuna-veri-eklemek.md): Temel DML (Data Manipulation Language) komutu (`INSERT INTO`).
*   [Temel SQL Sorgularına Giriş](temel-sql-sorgularina-giris/README.md): `SELECT` komutu ile veri sorgulama.
*   [mysqldump ile Yedekleme](mysqldump-ile-yedekleme.md): Veritabanlarını yedeklemek için `mysqldump` kullanımı.
*   [Yedekleri İçeri Alma](yedekleri-iceri-alma.md): `mysql` istemcisi ile yedekleri geri yükleme.
*   [Replikasyon Teknikleri](replikasyon-teknikleri.md): Veritabanı kopyalama (master-slave) yöntemlerine genel bakış.
