# MongoDB Temel Yapılandırma

Bu bölümde MongoDB kurulumu sonrası yapılması gereken temel yapılandırma adımları, ana yapılandırma dosyası ve servis yönetimi anlatılacaktır.

## Yapılandırma Dosyası (`mongod.conf`)

MongoDB'nin ana yapılandırma dosyası genellikle `/etc/mongod.conf` konumunda bulunur. Bu dosya YAML formatındadır ve sunucunun davranışını kontrol eden çeşitli ayarları içerir.

Önemli bazı yapılandırma seçenekleri şunlardır:

*   **`storage.dbPath`**: Veritabanı dosyalarının saklanacağı dizin (varsayılan: `/var/lib/mongodb`).
*   **`systemLog.path`**: Log dosyasının konumu (varsayılan: `/var/log/mongodb/mongod.log`).
*   **`systemLog.destination`**: Logların nereye yazılacağı (`file` veya `syslog`).
*   **`net.port`**: MongoDB'nin dinleyeceği port (varsayılan: `27017`).
*   **`net.bindIp`**: MongoDB'nin hangi ağ arayüzlerini dinleyeceği. Güvenlik nedeniyle varsayılan olarak genellikle sadece `127.0.0.1` (localhost) olarak ayarlanmıştır. Dışarıdan erişime izin vermek için bu ayarı `0.0.0.0` veya belirli IP adresleri olarak değiştirmeniz gerekir (güvenlik duvarı ayarlarını da yapmayı unutmayın!).
*   **`security.authorization`**: Kimlik doğrulamanın etkinleştirilip etkinleştirilmeyeceği (`enabled` veya `disabled`). Varsayılan olarak devre dışıdır. Üretim ortamlarında mutlaka etkinleştirilmelidir.

Yapılandırma dosyasında yapılan değişikliklerden sonra MongoDB servisini yeniden başlatmak gerekir.

## Servis Yönetimi (systemd)

`systemd` kullanan sistemlerde MongoDB servisi (`mongod`) `systemctl` komutu ile yönetilir.

*   **Servis Durumunu Kontrol Etme:**
    ```bash
    sudo systemctl status mongod
    ```

*   **Servisi Başlatma:**
    ```bash
    sudo systemctl start mongod
    ```

*   **Servisi Durdurma:**
    ```bash
    sudo systemctl stop mongod
    ```

*   **Servisi Yeniden Başlatma:**
    Yapılandırma değişikliklerinden sonra genellikle bu komut kullanılır.
    ```bash
    sudo systemctl restart mongod
    ```

*   **Servisi Sistem Açılışında Etkinleştirme:**
    ```bash
    sudo systemctl enable mongod
    ```

*   **Servisi Sistem Açılışında Devre Dışı Bırakma:**
    ```bash
    sudo systemctl disable mongod
    ```
