# `mysqldump` ile Yedekleme

`mysqldump`, MySQL ve MariaDB veritabanlarının mantıksal yedeklerini (logical backup) almak için kullanılan standart bir komut satırı aracıdır. Oluşturduğu yedek dosyası, veritabanını ve/veya tabloları yeniden oluşturmak için gerekli SQL ifadelerini (`CREATE DATABASE`, `CREATE TABLE`, `INSERT INTO` vb.) içeren bir metin dosyasıdır.

Bu yöntem, fiziksel yedeklemeye (veritabanı dosyalarını doğrudan kopyalama) göre genellikle daha esnektir (farklı sürümler veya mimariler arasında taşınabilir), ancak büyük veritabanları için yedekleme ve geri yükleme işlemi daha uzun sürebilir.

## Temel Kullanım

`mysqldump` komutu genellikle şu formatta kullanılır:
`mysqldump [seçenekler] <veritabanı_adı> [tablo_adları...] > yedek_dosyasi.sql`

*   **Tek Bir Veritabanını Yedekleme:**
    ```bash
    mysqldump -u <kullanıcı> -p <veritabanı_adı> > yedek.sql
    ```
    Komut, belirtilen kullanıcının şifresini soracaktır. `<veritabanı_adı>` yerine yedeklenecek veritabanının adını yazın. Çıktı (`yedek.sql` dosyası) tüm `CREATE TABLE` ve `INSERT INTO` ifadelerini içerecektir.

*   **Birden Fazla Veritabanını Yedekleme:**
    `--databases` seçeneği ile birden fazla veritabanı belirtilebilir. Bu seçenek kullanıldığında, yedek dosyası `CREATE DATABASE IF NOT EXISTS` ve `USE <veritabanı_adı>` ifadelerini de içerir.
    ```bash
    mysqldump -u <kullanıcı> -p --databases <db1_adı> <db2_adı> > coklu_yedek.sql
    ```

*   **Tüm Veritabanlarını Yedekleme:**
    Sunucudaki tüm veritabanlarını yedeklemek için `--all-databases` seçeneği kullanılır.
    ```bash
    mysqldump -u root -p --all-databases > tum_yedekler.sql
    ```
    Bu komut genellikle `root` veya tüm veritabanlarına erişim yetkisi olan bir kullanıcı ile çalıştırılır.

*   **Sadece Belirli Tabloları Yedekleme:**
    Veritabanı adından sonra boşlukla ayırarak sadece belirli tablo adları yazılabilir:
    ```bash
    mysqldump -u <kullanıcı> -p <veritabanı_adı> tablo1 tablo3 > tablolar_yedek.sql
    ```

## Yaygın Kullanılan Seçenekler

*   **`-u <kullanıcı>`, `-p`**: Veritabanına bağlanmak için kullanıcı adı ve şifre istemi. Şifreyi doğrudan komut satırında `-p<şifre>` şeklinde (boşluksuz) yazmak mümkündür ancak güvenlik açısından önerilmez (komut geçmişinde görünebilir).
*   **`--host=<host_adı>`, `--port=<port_no>`**: Eğer veritabanı sunucusu yerel makinede değilse veya standart portu (3306) kullanmıyorsa bağlantı bilgilerini belirtir.
*   **`--result-file=<dosya_adı>`**: Çıktıyı `>` yönlendirmesi yerine doğrudan belirtilen dosyaya yazar.
*   **`--single-transaction`**: **(InnoDB tabloları için önerilir)** Yedekleme işlemi başlamadan önce bir veritabanı anlık görüntüsü (snapshot) oluşturur ve tüm yedeklemeyi bu anlık görüntü üzerinden tek bir işlem (transaction) içinde yapar. Bu, yedekleme sırasında tabloların kilitlenmesini engeller ve tutarlı bir yedek alınmasını sağlar. MyISAM gibi transactional olmayan tablolar için etkisi yoktur. `--lock-tables` ile birlikte kullanılmamalıdır.
*   **`--quick` (-q)**: Büyük tablolar için önerilir. `mysqldump`'ın tüm tablo içeriğini belleğe almasını engeller, bunun yerine satır satır okuyup yazar.
*   **`--routines` (-R)**: Saklı yordamları (stored procedures) ve fonksiyonları (functions) yedek dosyasına dahil eder.
*   **`--triggers`**: Tablolara ait tetikleyicileri (triggers) yedek dosyasına dahil eder. (Genellikle varsayılan olarak dahildir).
*   **`--events` (-E)**: Zamanlanmış olayları (scheduled events) yedek dosyasına dahil eder.
*   **`--no-data` (-d)**: Sadece veritabanı ve tablo yapılarını (schema) yedekler, verileri (`INSERT` ifadelerini) dahil etmez. Boş bir veritabanı yapısı oluşturmak için kullanışlıdır.
*   **`--databases`**: Yedeklenen veritabanları için `CREATE DATABASE` ifadelerini ekler. Birden fazla veritabanı yedeklerken kullanışlıdır.
*   **`--all-databases`**: Tüm veritabanları için `CREATE DATABASE` ifadelerini ekler.
*   **`--add-drop-database`**: `--all-databases` veya `--databases` ile kullanıldığında, `CREATE DATABASE` ifadesinden önce `DROP DATABASE IF EXISTS` ifadesini ekler. Geri yüklerken mevcut veritabanının silinmesini sağlar (dikkatli kullanılmalıdır!).
*   **`--add-drop-table`**: Her `CREATE TABLE` ifadesinden önce `DROP TABLE IF EXISTS` ifadesini ekler. Geri yüklerken mevcut tabloların silinmesini sağlar.

## Yedekleri Sıkıştırma

`mysqldump` çıktısı genellikle büyük olabilen bir metin dosyasıdır. Disk alanı kazanmak için çıktıyı doğrudan `gzip`, `zstd` gibi bir sıkıştırma aracına pipe ile göndermek yaygın bir pratiktir:

```bash
# gzip ile sıkıştırma
mysqldump -u root -p --single-transaction --all-databases | gzip > tum_yedekler.sql.gz

# zstd ile sıkıştırma (daha modern ve hızlı)
mysqldump -u root -p --single-transaction --all-databases | zstd > tum_yedekler.sql.zst
```

## Yedekleri Geri Yükleme

`mysqldump` ile alınan `.sql` (veya sıkıştırılmış) dosyaları geri yüklemek için `mysql` (veya `mariadb`) komut satırı istemcisi kullanılır. Bu işlem [Yedekleri İçeri Alma](yedekleri-iceri-alma.md) bölümünde anlatılmaktadır.

`mysqldump`, MySQL/MariaDB veritabanlarını yedeklemek için esnek ve güçlü bir araçtır. Özellikle `--single-transaction` gibi seçenekler, canlı sistemlerde tutarlı yedekler almak için önemlidir.
