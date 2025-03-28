# PostgreSQL Temel Yapılandırma

Bu bölümde PostgreSQL kurulumu sonrası yapılması gereken temel yapılandırma adımları, önemli yapılandırma dosyaları ve servis yönetimi anlatılacaktır.

## Yapılandırma Dosyaları

PostgreSQL'in ana yapılandırma dosyaları genellikle veritabanı kümesinin (data directory) içinde bulunur. Bu dizinin konumu dağıtıma ve kuruluma göre değişebilir, ancak genellikle şuralarda bulunur:

*   **RHEL Tabanlı:** `/var/lib/pgsql/<sürüm>/data/` (Örn: `/var/lib/pgsql/16/data/`)
*   **Debian Tabanlı:** `/etc/postgresql/<sürüm>/<küme_adı>/` (Örn: `/etc/postgresql/16/main/`)

Bu dizin içindeki en önemli iki yapılandırma dosyası şunlardır:

1.  **`postgresql.conf`**: Sunucunun ana yapılandırma dosyasıdır. Dinlenecek ağ arayüzleri (`listen_addresses`), port (`port`), bellek ayarları (`shared_buffers`, `work_mem`), loglama ayarları (`log_destination`, `logging_collector`), bağlantı limitleri (`max_connections`) gibi birçok parametreyi içerir. Değişikliklerin çoğu bu dosyada yapılır ve genellikle servisin yeniden başlatılmasını veya yeniden yüklenmesini (`reload`) gerektirir.

2.  **`pg_hba.conf`** (Host-Based Authentication): İstemci kimlik doğrulama kurallarını tanımlar. Hangi kullanıcıların, hangi veritabanlarına, hangi IP adreslerinden (veya soketlerden) ve hangi kimlik doğrulama yöntemleriyle (örn: `trust`, `peer`, `scram-sha-256`, `md5`) bağlanabileceğini belirler. Bu dosyadaki değişiklikler genellikle servisin yeniden yüklenmesi (`reload`) ile aktif hale gelir.
    *   **Önemli Not:** Varsayılan olarak, PostgreSQL genellikle sadece yerel Unix soketi üzerinden `peer` veya `trust` kimlik doğrulamasına izin verir. Ağ üzerinden parola ile bağlanmak için `pg_hba.conf` dosyasını düzenlemeniz ve `listen_addresses` ayarını `postgresql.conf` içinde değiştirmeniz gerekir.

## Servis Yönetimi (systemd)

`systemd` kullanan modern Linux dağıtımlarında PostgreSQL servisini yönetmek için `systemctl` komutu kullanılır. Servis adı genellikle sürüm numarasını içerir.

*   **Servis Durumunu Kontrol Etme:**
    ```bash
    # RHEL Tabanlı (Örn: Sürüm 16)
    sudo systemctl status postgresql-16

    # Debian Tabanlı (Örn: Sürüm 16, Küme 'main')
    sudo systemctl status postgresql@16-main
    ```

*   **Servisi Başlatma:**
    ```bash
    # RHEL Tabanlı
    sudo systemctl start postgresql-16

    # Debian Tabanlı
    sudo systemctl start postgresql@16-main
    ```

*   **Servisi Durdurma:**
    ```bash
    # RHEL Tabanlı
    sudo systemctl stop postgresql-16

    # Debian Tabanlı
    sudo systemctl stop postgresql@16-main
    ```

*   **Servisi Yeniden Başlatma:**
    ```bash
    # RHEL Tabanlı
    sudo systemctl restart postgresql-16

    # Debian Tabanlı
    sudo systemctl restart postgresql@16-main
    ```

*   **Yapılandırmayı Yeniden Yükleme (Reload):**
    `postgresql.conf` veya `pg_hba.conf` dosyalarında yapılan bazı değişiklikleri servisi tamamen durdurup başlatmadan uygulamak için kullanılır.
    ```bash
    # RHEL Tabanlı
    sudo systemctl reload postgresql-16

    # Debian Tabanlı
    sudo systemctl reload postgresql@16-main
    ```

*   **Servisi Sistem Açılışında Etkinleştirme:**
    ```bash
    # RHEL Tabanlı
    sudo systemctl enable postgresql-16

    # Debian Tabanlı
    sudo systemctl enable postgresql@16-main
    ```

*   **Servisi Sistem Açılışında Devre Dışı Bırakma:**
    ```bash
    # RHEL Tabanlı
    sudo systemctl disable postgresql-16

    # Debian Tabanlı
    sudo systemctl disable postgresql@16-main
    ```

Doğru servis adını bulmak için `systemctl list-units | grep postgres` gibi komutlar kullanılabilir.
