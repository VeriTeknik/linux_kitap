# Redis Temel Yapılandırma

Bu bölümde Redis kurulumu sonrası yapılması gereken temel yapılandırma adımları, ana yapılandırma dosyası ve servis yönetimi anlatılacaktır.

## Yapılandırma Dosyası (`redis.conf`)

Redis'in ana yapılandırma dosyası genellikle `/etc/redis/redis.conf` (dağıtım paketleri veya resmi Redis paketleri) veya `/etc/redis.conf` (kaynaktan derleme) konumunda bulunur. Bu dosya, sunucunun davranışını belirleyen birçok direktif içerir.

Önemli bazı yapılandırma direktifleri şunlardır:

*   **`bind`**: Redis'in hangi ağ arayüzlerini dinleyeceğini belirtir. Varsayılan olarak genellikle `127.0.0.1` (sadece localhost) olarak ayarlanmıştır. Dışarıdan erişime izin vermek için `0.0.0.0` veya belirli bir IP adresi olarak değiştirilmelidir. Güvenlik duvarı ayarları da buna göre yapılmalıdır.
*   **`port`**: Redis'in dinleyeceği port (varsayılan: `6379`).
*   **`requirepass`**: İstemcilerin bağlanmadan önce `AUTH` komutu ile göndermesi gereken parolayı belirler. Güvenlik için mutlaka ayarlanmalıdır.
*   **`daemonize`**: Redis'in arkaplanda bir daemon olarak çalışıp çalışmayacağını belirler (`yes` veya `no`). `systemd` ile yönetilen servisler için genellikle `no` olarak ayarlanır, çünkü `systemd` süreci kendisi yönetir.
*   **`pidfile`**: Daemon olarak çalışıyorsa process ID'sinin yazılacağı dosya yolu.
*   **`logfile`**: Log mesajlarının yazılacağı dosya yolu (eğer `daemonize yes` ise veya loglama dosyaya yönlendirilmişse).
*   **`databases`**: Kullanılabilir veritabanı sayısı (varsayılan: `16`). Redis'te veritabanları 0'dan başlayan numaralarla temsil edilir.
*   **`dir`**: Kalıcılık (persistence) için RDB veya AOF dosyalarının saklanacağı dizin (genellikle `/var/lib/redis`).
*   **`dbfilename`**: RDB kalıcılık dosyasının adı (varsayılan: `dump.rdb`).
*   **`appendonly`**: AOF (Append Only File) kalıcılığının etkin olup olmadığını belirler (`yes` veya `no`).

Yapılandırma dosyasında yapılan değişikliklerden sonra Redis servisini yeniden başlatmak gerekir.

## Servis Yönetimi (systemd)

`systemd` kullanan sistemlerde Redis servisi genellikle `redis` veya `redis-server` adıyla `systemctl` komutu ile yönetilir. Servis adı kuruluma göre değişebilir.

*   **Servis Durumunu Kontrol Etme:**
    ```bash
    sudo systemctl status redis
    # veya
    sudo systemctl status redis-server
    ```

*   **Servisi Başlatma:**
    ```bash
    sudo systemctl start redis
    # veya
    sudo systemctl start redis-server
    ```

*   **Servisi Durdurma:**
    ```bash
    sudo systemctl stop redis
    # veya
    sudo systemctl stop redis-server
    ```

*   **Servisi Yeniden Başlatma:**
    Yapılandırma değişikliklerinden sonra genellikle bu komut kullanılır.
    ```bash
    sudo systemctl restart redis
    # veya
    sudo systemctl restart redis-server
    ```

*   **Servisi Sistem Açılışında Etkinleştirme:**
    ```bash
    sudo systemctl enable redis
    # veya
    sudo systemctl enable redis-server
    ```

*   **Servisi Sistem Açılışında Devre Dışı Bırakma:**
    ```bash
    sudo systemctl disable redis
    # veya
    sudo systemctl disable redis-server
    ```
Doğru servis adını bulmak için `systemctl list-units | grep redis` gibi komutlar kullanılabilir.
