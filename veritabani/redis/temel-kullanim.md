# Redis Temel Kullanım

Bu bölümde Redis'e nasıl bağlanılacağı ve temel anahtar-değer işlemlerinin (veri ekleme, okuma, silme vb.) nasıl yapılacağı anlatılacaktır.

## Bağlantı Kurma (`redis-cli`)

Redis ile etkileşim kurmak için kullanılan standart komut satırı aracı `redis-cli`'dir.

*   **Yerel Sunucuya Bağlanma:**
    Eğer Redis sunucusu varsayılan ayarlarla (`127.0.0.1` port `6379`) yerelde çalışıyorsa ve parola koruması yoksa, doğrudan `redis-cli` komutu ile bağlanabilirsiniz:
    ```bash
    redis-cli
    ```
    Bu komut sizi interaktif `redis-cli` kabuğuna düşürür.

*   **Belirli Bir Sunucuya ve Porta Bağlanma:**
    Farklı bir sunucu (`-h`) veya port (`-p`) belirtmek için:
    ```bash
    redis-cli -h sunucu_adresi -p port_numarasi
    ```

*   **Parola ile Bağlanma:**
    Eğer `redis.conf` dosyasında `requirepass` ile bir parola ayarlanmışsa, bağlanırken `-a` seçeneği ile parolayı belirtebilirsiniz:
    ```bash
    redis-cli -h sunucu_adresi -p port_numarasi -a 'parolaniz'
    ```
    Alternatif olarak, `redis-cli` ile bağlandıktan sonra `AUTH` komutunu kullanabilirsiniz:
    ```bash
    redis-cli -h sunucu_adresi -p port_numarasi
    127.0.0.1:6379> AUTH parolaniz
    OK
    ```

*   **Belirli Bir Veritabanına Bağlanma:**
    Redis birden fazla veritabanını destekler (varsayılan 16, 0'dan başlar). Bağlanırken `-n` seçeneği ile veritabanı numarasını belirtebilirsiniz:
    ```bash
    redis-cli -n 5
    ```
    Bağlandıktan sonra `SELECT` komutu ile de veritabanı değiştirebilirsiniz:
    ```bash
    127.0.0.1:6379> SELECT 5
    OK
    127.0.0.1:6379[5]>
    ```

## Temel Redis Komutları (`redis-cli` içinde)

`redis-cli` kabuğu içindeyken Redis komutlarını doğrudan yazabilirsiniz.

*   **Bağlantıyı Test Etme:**
    Sunucunun ayakta olup olmadığını kontrol eder.
    ```
    PING
    PONG
    ```

*   **Anahtar Ayarlama (Set):**
    Bir anahtara bir değer atar. Eğer anahtar zaten varsa, değeri üzerine yazılır.
    ```
    SET kullanici:1:isim "Ahmet"
    OK
    ```

*   **Anahtar Değeri Alma (Get):**
    Belirtilen anahtarın değerini döndürür. Anahtar yoksa `(nil)` döner.
    ```
    GET kullanici:1:isim
    "Ahmet"
    ```

*   **Anahtar Silme (Delete):**
    Belirtilen anahtarı ve değerini siler.
    ```
    DEL kullanici:1:isim
    (integer) 1  # Silinen anahtar sayısı
    ```

*   **Anahtarın Varlığını Kontrol Etme (Exists):**
    Anahtarın var olup olmadığını kontrol eder. Varsa `1`, yoksa `0` döner.
    ```
    EXISTS kullanici:1:isim
    (integer) 0
    ```

*   **Anahtarları Listeleme (Keys):**
    Belirtilen desene uyan anahtarları listeler. **Dikkat:** Büyük veritabanlarında `KEYS *` gibi genel sorgular performansı olumsuz etkileyebilir, üretim ortamlarında dikkatli kullanılmalıdır.
    ```
    SET site:url "example.com"
    OK
    SET site:tema "dark"
    OK
    KEYS site:*
    1) "site:tema"
    2) "site:url"
    ```

*   **Mevcut Veritabanındaki Tüm Anahtarları Silme (FlushDB):**
    **Dikkat:** Bu işlem geri alınamaz ve mevcut veritabanındaki tüm anahtarları siler!
    ```
    FLUSHDB
    OK
    ```

*   **Tüm Veritabanlarındaki Tüm Anahtarları Silme (FlushAll):**
    **Dikkat:** Bu işlem geri alınamaz ve sunucudaki tüm veritabanlarındaki tüm anahtarları siler!
    ```
    FLUSHALL
    OK
    ```

*   **Veritabanı Seçme (Select):**
    Farklı bir veritabanına geçiş yapar (0-15 varsayılan).
    ```
    SELECT 1
    OK
    ```

*   **Çıkış:**
    `redis-cli` kabuğundan çıkar.
    ```
    QUIT
    ```
    veya `exit` yazıp Enter'a basmak.

Redis, string'lerin yanı sıra listeler, hash'ler, set'ler ve sorted set'ler gibi daha karmaşık veri yapılarını da destekler ve bunlar için özel komutlara sahiptir (örn: `LPUSH`, `HSET`, `SADD`, `ZADD`).
