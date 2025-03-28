# PostgreSQL Temel Kullanım

Bu bölümde PostgreSQL'e nasıl bağlanılacağı ve temel veritabanı/kullanıcı işlemlerinin nasıl yapılacağı anlatılacaktır.

## Bağlantı Kurma (`psql`)

PostgreSQL ile etkileşim kurmak için en yaygın kullanılan komut satırı aracı `psql`'dir.

*   **Varsayılan Kullanıcı ile Bağlanma:**
    Kurulumdan sonra genellikle `postgres` adında bir süper kullanıcı (superuser) oluşturulur. Bu kullanıcı ile veritabanı sunucusuna bağlanmak için genellikle `postgres` Linux kullanıcısı olmanız gerekir (peer authentication nedeniyle).
    ```bash
    sudo -u postgres psql
    ```
    Bu komut sizi `postgres` veritabanına `postgres` kullanıcısı olarak bağlar ve `psql` istemcisini açar.

*   **Belirli Bir Kullanıcı ve Veritabanı ile Bağlanma:**
    Farklı bir kullanıcı (`-U` veya `--username`) ve/veya farklı bir veritabanı (`-d` veya `--dbname`) ile bağlanabilirsiniz:
    ```bash
    psql -U kullanici_adi -d veritabani_adi
    ```
    Eğer ağ üzerinden bağlanıyorsanız, sunucu adresini (`-h` veya `--host`) ve portu (`-p` veya `--port`) da belirtmeniz gerekebilir:
    ```bash
    psql -h sunucu_adresi -p 5432 -U kullanici_adi -d veritabani_adi
    ```
    Bağlantı için parola gerekiyorsa (`pg_hba.conf` ayarına bağlı olarak), `psql` sizden parola isteyecektir.

## Temel Veritabanı İşlemleri

`psql` istemcisi içindeyken veya `createdb`, `dropdb` gibi komut satırı araçlarıyla veritabanı işlemleri yapabilirsiniz.

*   **Veritabanı Oluşturma:**
    ```sql
    -- psql içinde:
    CREATE DATABASE yeni_veritabani;

    -- Komut satırından (postgres kullanıcısı olarak):
    sudo -u postgres createdb yeni_veritabani
    ```

*   **Veritabanlarını Listeleme:**
    ```sql
    -- psql içinde:
    \l
    -- veya
    \list
    ```

*   **Veritabanı Silme:**
    **Dikkat:** Bu işlem geri alınamaz ve veritabanındaki tüm verileri siler!
    ```sql
    -- psql içinde (başka bir veritabanına bağlıyken):
    DROP DATABASE silinecek_veritabani;

    -- Komut satırından (postgres kullanıcısı olarak):
    sudo -u postgres dropdb silinecek_veritabani
    ```

## Temel Kullanıcı (Rol) İşlemleri

PostgreSQL'de kullanıcılar "rol" (role) olarak adlandırılır. Roller, giriş yapma yetkisine (LOGIN) veya başka rolleri içerme özelliğine sahip olabilir.

*   **Kullanıcı (Rol) Oluşturma:**
    ```sql
    -- psql içinde (postgres kullanıcısı olarak):
    CREATE ROLE yeni_kullanici WITH LOGIN PASSWORD 'guclu_bir_parola';
    -- veya daha fazla seçenekle:
    CREATE USER baska_kullanici WITH PASSWORD 'parola123' CREATEDB; -- Veritabanı oluşturma yetkisi verir
    ```

*   **Kullanıcıları (Rolleri) Listeleme:**
    ```sql
    -- psql içinde:
    \du
    ```

*   **Kullanıcı (Rol) Silme:**
    ```sql
    -- psql içinde:
    DROP ROLE silinecek_kullanici;
    -- veya
    DROP USER silinecek_kullanici;
    ```

## Yetkilendirme (Privileges)

Kullanıcıların veritabanı nesneleri (tablolar, şemalar vb.) üzerinde işlem yapabilmesi için yetkilendirilmesi gerekir.

*   **Bir Veritabanına Bağlanma Yetkisi Verme:**
    ```sql
    -- psql içinde:
    GRANT CONNECT ON DATABASE hedef_veritabani TO kullanici_adi;
    ```

*   **Bir Şemadaki Tüm Tablolar Üzerinde Temel Yetkileri Verme:**
    ```sql
    -- psql içinde (hedef_veritabani'na bağlıyken):
    GRANT USAGE ON SCHEMA public TO kullanici_adi; -- Şemayı kullanma yetkisi
    GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO kullanici_adi; -- Mevcut tablolar için yetki
    ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO kullanici_adi; -- Gelecekte oluşturulacak tablolar için yetki
    ```
    (Yetkiler (`SELECT`, `INSERT` vb.) ihtiyaca göre ayarlanmalıdır.)

## `psql` İstemcisi Komutları

`psql` içinde ters eğik çizgi (`\`) ile başlayan özel komutlar bulunur:

*   `\l` veya `\list`: Veritabanlarını listeler.
*   `\c veritabani_adi`: Belirtilen veritabanına bağlanır.
*   `\d [tablo_adi]`: Tabloları veya belirtilen tablonun yapısını listeler.
*   `\du`: Rolleri (kullanıcıları) listeler.
*   `\dt`: Mevcut şemadaki tabloları listeler.
*   `\dn`: Şemaları listeler.
*   `\?`: `psql` komutları hakkında yardım gösterir.
*   `\q`: `psql` istemcisinden çıkar.
