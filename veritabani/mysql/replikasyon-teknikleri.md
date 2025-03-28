# Replikasyon Teknikleri (MySQL/MariaDB)

Veritabanı replikasyonu, verilerin bir veritabanı sunucusundan (genellikle **kaynak (source)**, **birincil (primary)** veya eski adıyla **master** olarak adlandırılır) bir veya daha fazla başka veritabanı sunucusuna (**kopya (replica)** veya eski adıyla **slave**) kopyalanması işlemidir.

## Replikasyonun Amaçları

*   **Yüksek Erişilebilirlik (High Availability):** Kaynak sunucuda bir sorun olması durumunda, kopyalardan birinin onun yerini alarak (failover) hizmet kesintisini en aza indirmek.
*   **Okuma Ölçeklendirme (Read Scaling):** Yoğun okuma işlemleri olan uygulamalarda, okuma sorgularını kopyalara yönlendirerek kaynak sunucunun yükünü azaltmak. Yazma işlemleri hala kaynağa yapılır.
*   **Yedekleme:** Canlı kaynak sunucuyu etkilemeden, kopyalardan birinden güvenli bir şekilde yedek almak.
*   **Analiz/Raporlama:** Uzun süren analiz veya raporlama sorgularını kaynak sunucuyu yavaşlatmadan kopyalar üzerinde çalıştırmak.
*   **Coğrafi Dağıtım:** Veriyi farklı coğrafi konumlardaki kullanıcılara daha yakın tutmak.

## Standart Asenkron Replikasyon

MySQL ve MariaDB'de en yaygın kullanılan replikasyon türü **asenkron (asynchronous)** kaynak-kopya (source-replica / master-slave) replikasyonudur.

**Nasıl Çalışır?**

1.  **Binary Log (Binlog):** Kaynak sunucu, veritabanında yapılan tüm değişiklikleri (INSERT, UPDATE, DELETE vb. ifadeler veya satır değişiklikleri) **binary log** adı verilen özel bir log dosyasına yazar. Bu log, replikasyonun temelini oluşturur.
2.  **I/O Thread (Kopya):** Kopya sunucu, kaynak sunucuya bağlanır ve kaynak sunucunun binary log'undaki yeni olayları okumak için bir **I/O thread** başlatır.
3.  **Relay Log (Kopya):** Kopya sunucunun I/O thread'i, kaynaktan okuduğu olayları kendi yerel disindeki **relay log** dosyalarına yazar.
4.  **SQL Thread (Kopya):** Kopya sunucu, relay log'ları okuyup içindeki SQL olaylarını kendi veritabanı üzerinde yürütmek için bir **SQL thread** (veya modern sürümlerde birden fazla paralel thread) başlatır.

Bu süreç asenkrondur, yani kaynak sunucu bir işlemi tamamladıktan sonra kopyanın o işlemi uygulamasını beklemez. Bu nedenle, kopya sunucu genellikle kaynak sunucunun biraz gerisinde olabilir (**replica lag**).

**Binlog Formatları:**

*   **Statement-Based Replication (SBR):** Kaynak sunucu, çalıştırılan SQL ifadelerini (`INSERT`, `UPDATE` vb.) binlog'a yazar. Kopya bu ifadeleri tekrar çalıştırır. Basittir ancak bazı durumlarda (örn. `UUID()`, `NOW()` gibi deterministik olmayan fonksiyonlar, `LIMIT` içeren `UPDATE`/`DELETE`) tutarsızlıklara yol açabilir.
*   **Row-Based Replication (RBR):** Kaynak sunucu, SQL ifadelerini değil, değişen *satırların* önceki ve sonraki hallerini binlog'a yazar. Kopya bu satır değişikliklerini doğrudan uygular. Genellikle daha güvenilir ve tutarlıdır, çoğu modern sistem için **önerilen** formattır. Ancak binlog boyutu daha büyük olabilir.
*   **Mixed-Based Replication (MBR):** Varsayılan olarak SBR kullanır, ancak SBR için güvenli olmayan ifadelerle karşılaştığında otomatik olarak RBR'ye geçer.

Binlog formatı, kaynak sunucunun yapılandırmasında (`binlog_format` değişkeni) ayarlanır.

**Replikasyon Konumlandırma:**

Kopyanın, kaynağın binlog'unda hangi noktadan itibaren okumaya başlayacağını bilmesi gerekir. İki ana yöntem vardır:

*   **Klasik (Binlog Dosyası + Pozisyon):** Kopya, belirli bir binlog dosyasının (`mysql-bin.000123`) belirli bir bayt pozisyonundan (`154`) okumaya başlar. Kaynak sunucuda `SHOW MASTER STATUS;` komutu ile mevcut dosya ve pozisyon görülebilir.
*   **GTID (Global Transaction Identifiers):** Modern MySQL (5.6+) ve MariaDB (10.0+) sürümlerinde **önerilen** yöntemdir. Her bir işlem (transaction) küresel olarak benzersiz bir kimlik (GTID) alır. Kopya, hangi GTID'leri zaten uyguladığını takip eder ve kaynaktan sadece eksik olanları ister. Bu, failover ve topoloji değişikliklerini çok daha kolay hale getirir. Yapılandırmada `gtid_mode=ON` ve `enforce_gtid_consistency=ON` ayarları gereklidir.

## Temel Asenkron Replikasyon Kurulum Adımları (Kavramsal)

Aşağıdaki adımlar genel bir fikir vermek içindir, detaylar sürüme ve yapılandırmaya göre değişebilir. GTID kullanıldığı varsayılmıştır.

1.  **Kaynak Sunucu Yapılandırması (`my.cnf` veya `my.ini`):**
    *   Benzersiz bir `server-id` ayarlayın (örn. `server-id = 1`).
    *   Binary log'u etkinleştirin (`log-bin = mysql-bin`).
    *   (Önerilen) GTID'yi etkinleştirin (`gtid_mode = ON`, `enforce_gtid_consistency = ON`).
    *   (Önerilen) RBR kullanın (`binlog_format = ROW`).
    *   Servisi yeniden başlatın.
2.  **Replikasyon Kullanıcısı Oluşturma (Kaynakta):**
    Kopyanın kaynağa bağlanmak için kullanacağı bir kullanıcı oluşturun ve gerekli replikasyon yetkilerini verin:
    ```sql
    CREATE USER 'replica_user'@'<kopya_sunucu_ip>' IDENTIFIED BY 'guclu_bir_sifre';
    GRANT REPLICATION SLAVE ON *.* TO 'replica_user'@'<kopya_sunucu_ip>';
    FLUSH PRIVILEGES;
    ```
3.  **Kaynak Veritabanının Anlık Görüntüsünü Alma:**
    Tutarlı bir başlangıç noktası için kaynağın yedeğini alın. `mysqldump` kullanılıyorsa, GTID bilgilerini de içeren bir yedek almak önemlidir:
    ```bash
    mysqldump -u root -p --all-databases --single-transaction --routines --triggers --events --master-data=2 --gtid > kaynak_yedek.sql 
    # veya mariadb-dump kullanın
    ```
    `--master-data=2` (veya `--dump-slave=2`) ve `--gtid` seçenekleri, yedek dosyasına o anki GTID bilgisini (`SET @@GLOBAL.GTID_PURGED=...`) ekler.
4.  **Kopya Sunucu Yapılandırması (`my.cnf` veya `my.ini`):**
    *   Kaynaktan farklı, benzersiz bir `server-id` ayarlayın (örn. `server-id = 2`).
    *   (Önerilen) GTID'yi etkinleştirin (`gtid_mode = ON`, `enforce_gtid_consistency = ON`).
    *   (Önerilen) Kopyanın sadece okunur olmasını sağlayın (`read_only = 1` veya `super_read_only = 1`).
    *   Relay log ayarlarını yapın (örn. `relay-log = kopya-relay-bin`).
    *   (Önerilen) Kopyanın kendi değişikliklerini de binlog'a yazmasını sağlayın (`log-bin = kopya-bin`, `log_slave_updates = 1`) - bu, zincirleme replikasyon veya kolay failover için gereklidir.
    *   Servisi yeniden başlatın.
5.  **Yedeği Kopyaya Geri Yükleme:**
    Kaynak sunucudan alınan yedek dosyasını kopya sunucuya aktarın ve `mysql` istemcisi ile geri yükleyin:
    ```bash
    mysql -u root -p < kaynak_yedek.sql
    ```
6.  **Kopyayı Kaynağa Bağlama (`CHANGE MASTER TO` / `CHANGE REPLICATION SOURCE TO`):**
    Kopya sunucuda, kaynağa nasıl bağlanacağını ve hangi noktadan başlayacağını belirtin (GTID kullanılıyorsa dosya/pozisyon yerine `MASTER_AUTO_POSITION=1` veya `SOURCE_AUTO_POSITION=1` kullanılır):
    ```sql
    -- MySQL 8.0.23+ / MariaDB 10.5.1+
    CHANGE REPLICATION SOURCE TO
      SOURCE_HOST='<kaynak_sunucu_ip>',
      SOURCE_USER='replica_user',
      SOURCE_PASSWORD='guclu_bir_sifre',
      SOURCE_AUTO_POSITION=1; -- GTID kullan

    -- Eski Sürümler
    -- CHANGE MASTER TO 
    --   MASTER_HOST='<kaynak_sunucu_ip>',
    --   MASTER_USER='replica_user',
    --   MASTER_PASSWORD='guclu_bir_sifre',
    --   MASTER_AUTO_POSITION=1; -- GTID kullan
    ```
7.  **Replikasyonu Başlatma:**
    Kopya sunucuda replikasyon thread'lerini başlatın:
    ```sql
    -- MySQL 8.0.22+ / MariaDB 10.5.1+
    START REPLICA;

    -- Eski Sürümler
    -- START SLAVE;
    ```
8.  **Replikasyon Durumunu Kontrol Etme:**
    Kopya sunucuda replikasyonun durumunu kontrol edin. `Slave_IO_Running` (veya `Replica_IO_Running`) ve `Slave_SQL_Running` (veya `Replica_SQL_Running`) durumlarının `Yes` olması ve `Seconds_Behind_Master` (veya `Seconds_Behind_Source`) değerinin düşük olması beklenir.
    ```sql
    -- MySQL 8.0.22+ / MariaDB 10.5.1+
    SHOW REPLICA STATUS\G

    -- Eski Sürümler
    -- SHOW SLAVE STATUS\G 
    ```
    (`\G` çıktıyı dikey formatta gösterir).

## Diğer Replikasyon Türleri

*   **Yarı Senkron (Semi-Synchronous) Replikasyon:** Kaynak, işlemi tamamlamadan önce en az bir kopyanın olayı kendi relay log'una yazdığını teyit eder. Asenkrona göre veri kaybı riskini azaltır ancak performansı biraz düşürebilir.
*   **Grup Replikasyonu (Group Replication - MySQL):** Birden fazla sunucunun aktif olarak birbirini güncellediği, çakışma tespiti ve otomatik failover sağlayan daha gelişmiş bir topolojidir.
*   **Galera Cluster (MariaDB/Percona):** Neredeyse senkron, çoklu kaynak (multi-primary) replikasyon sağlayan, otomatik node ekleme/çıkarma ve failover özelliklerine sahip bir kümeleme çözümüdür.

Replikasyon, veritabanı sistemlerinin ölçeklenebilirliğini ve dayanıklılığını artırmak için güçlü bir araçtır, ancak dikkatli planlama ve yapılandırma gerektirir.
