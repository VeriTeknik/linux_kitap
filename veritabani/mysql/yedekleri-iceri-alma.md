# Yedekleri İçeri Alma (Restore)

`mysqldump` ile oluşturulan mantıksal yedek dosyaları (`.sql`), içerdikleri SQL komutlarının `mysql` (veya `mariadb`) komut satırı istemcisi aracılığıyla çalıştırılmasıyla geri yüklenir.

## Geri Yükleme Yöntemleri

**1. Komut Satırı Yönlendirmesi (`<`) (Önerilen)**

Bu en yaygın ve genellikle en verimli yöntemdir. Yedek dosyası, `mysql` istemcisinin standart girdisine yönlendirilir.

*   **Tek Bir Veritabanını Geri Yükleme:**
    Geri yükleme yapmadan önce hedef veritabanının genellikle mevcut olması gerekir (eğer yedek dosyası `CREATE DATABASE` ifadesini içermiyorsa).
    ```bash
    # Önce hedef veritabanını oluştur (varsa bu adım atlanabilir)
    mysql -u root -p -e "CREATE DATABASE IF NOT EXISTS <hedef_veritabani_adı> CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"

    # Yedek dosyasını içeri aktar
    mysql -u <kullanıcı> -p <hedef_veritabani_adı> < yedek.sql 
    ```
    Komutlar sizden ilgili kullanıcının şifresini isteyecektir.

*   **Tüm Veritabanlarını Geri Yükleme:**
    Eğer yedek dosyası `--all-databases` veya `--databases` seçeneği ile oluşturulmuşsa ve `CREATE DATABASE` ifadelerini içeriyorsa, komut satırında veritabanı adı belirtilmez:
    ```bash
    mysql -u root -p < tum_yedekler.sql
    ```

**2. `SOURCE` Komutu (MySQL/MariaDB Kabuğu İçinden)**

`mysql` veya `mariadb` kabuğu içindeyken `SOURCE` komutu ile bir `.sql` dosyası çalıştırılabilir. Özellikle interaktif oturumlarda veya daha küçük dosyalar için kullanışlıdır.

```bash
# MySQL/MariaDB kabuğuna bağlan
mysql -u <kullanıcı> -p

# Gerekirse hedef veritabanını oluştur ve seç
mysql> CREATE DATABASE IF NOT EXISTS <hedef_veritabani_adı>;
mysql> USE <hedef_veritabani_adı>;

# SOURCE komutu ile yedek dosyasını çalıştır (tam dosya yolu gerekir)
mysql> SOURCE /tam/dosya/yolu/yedek.sql; 

# Kabuktan çık
mysql> quit;
```

## Sıkıştırılmış Yedekleri Geri Yükleme

Eğer yedek dosyası `gzip` veya `zstd` gibi bir araçla sıkıştırılmışsa, dosyayı önce açıp sonra `mysql`'e yönlendirmek yerine, açma işlemini ve geri yüklemeyi tek bir komutla birleştirebilirsiniz:

*   **gzip (`.gz`):**
    ```bash
    gunzip < yedek.sql.gz | mysql -u <kullanıcı> -p <hedef_veritabani_adı>
    # veya zcat kullanın:
    # zcat yedek.sql.gz | mysql -u <kullanıcı> -p <hedef_veritabani_adı>
    ```

*   **zstd (`.zst`):**
    ```bash
    zstdcat yedek.sql.zst | mysql -u <kullanıcı> -p <hedef_veritabani_adı>
    ```

## Dikkat Edilmesi Gerekenler

*   **Veri Üzerine Yazma:** Eğer hedef veritabanı veya tablolar zaten mevcutsa ve yedek dosyası `DROP DATABASE` veya `DROP TABLE` ifadelerini içeriyorsa (genellikle `--add-drop-database` veya `--add-drop-table` seçenekleriyle oluşturulmuşsa), geri yükleme işlemi mevcut verilerin **silinmesine** neden olur. Geri yükleme yapmadan önce neyin üzerine yazılacağından emin olun!
*   **Karakter Seti:** Yedek dosyasının ve hedef veritabanının karakter setlerinin uyumlu olması önemlidir, aksi takdirde özellikle Türkçe karakterlerde sorunlar yaşanabilir. `mysqldump` ve `mysql` istemcisinde `--default-character-set=utf8mb4` gibi seçenekler kullanılabilir.
*   **Büyük Dosyalar:** Çok büyük yedek dosyalarını geri yüklemek uzun sürebilir. `mysql` istemcisinin zaman aşımına uğramaması veya ağ bağlantısının kopmaması önemlidir. Gerekirse `screen` veya `tmux` gibi araçlar içinde çalıştırmak faydalı olabilir.
*   **Yetkiler:** Geri yükleme işlemini yapan kullanıcının hedef veritabanı üzerinde gerekli yetkilere (CREATE, INSERT, DROP vb.) sahip olması gerekir. Genellikle `root` veya yetkilendirilmiş özel bir kullanıcı kullanılır.

Yedekleme ve geri yükleme işlemleri, veritabanı yönetiminin kritik parçalarıdır ve düzenli olarak test edilmelidir.
