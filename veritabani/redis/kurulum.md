# Redis Kurulumu

Bu bölümde Redis'in farklı Linux dağıtımlarına nasıl kurulacağı anlatılacaktır. Redis genellikle dağıtımların kendi depolarından veya resmi Redis paket deposundan kurulabilir.

## RHEL Tabanlı Dağıtımlar (AlmaLinux, Rocky Linux, CentOS Stream)

RHEL tabanlı sistemlerde Redis genellikle EPEL (Extra Packages for Enterprise Linux) deposundan veya Remi deposundan kurulur. Alternatif olarak, Redis'in resmi paketleri de kullanılabilir.

**Yöntem 1: EPEL Deposundan Kurulum**

1.  **EPEL Deposunu Ekleme (Eğer kurulu değilse):**
    ```bash
    # RHEL 9 Tabanlı (Alma/Rocky 9)
    sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm -y

    # RHEL 8 Tabanlı (Alma/Rocky 8)
    sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y
    ```

2.  **Redis Paketini Kurma:**
    ```bash
    sudo dnf install redis -y
    ```

**Yöntem 2: Remi Deposundan Kurulum (Daha güncel sürümler için)**

1.  **Remi Deposunu Ekleme:**
    EPEL deposu gereklidir. Ardından Remi deposunu ekleyin:
    ```bash
    # RHEL 9 Tabanlı
    sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y

    # RHEL 8 Tabanlı
    sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-8.rpm -y
    ```

2.  **Redis Modülünü Etkinleştirme ve Kurma:**
    Mevcut Redis modüllerini listeleyin ve istediğiniz sürümü etkinleştirip kurun (örneğin, redis:remi-7.2):
    ```bash
    sudo dnf module list redis
    sudo dnf module enable redis:remi-7.2 -y
    sudo dnf install redis -y
    ```

**Yöntem 3: Resmi Redis Paket Deposu**

1.  **Depoyu Ekleme:**
    [Redis Resmi Paket Deposu Talimatları](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/install-redis-on-linux/) takip edilerek depo eklenebilir. Genellikle bir GPG anahtarı eklemeyi ve bir `.repo` dosyası oluşturmayı içerir.

2.  **Redis Paketini Kurma:**
    Depo eklendikten sonra:
    ```bash
    sudo dnf install redis-stack-server -y
    # Veya sadece OSS sürümü için:
    # sudo dnf install redis -y
    ```

**Kurulum Sonrası (Tüm Yöntemler):**

*   **Redis Servisini Başlatma ve Etkinleştirme:**
    ```bash
    sudo systemctl enable --now redis
    ```

## Debian Tabanlı Dağıtımlar (Debian, Ubuntu)

Debian ve Ubuntu'da Redis genellikle dağıtımın kendi deposundan veya Redis'in resmi `apt` deposundan kurulur.

**Yöntem 1: Dağıtım Deposundan Kurulum**

1.  **Paket Listesini Güncelleme:**
    ```bash
    sudo apt update
    ```
2.  **Redis Sunucusunu Kurma:**
    ```bash
    sudo apt install redis-server -y
    ```

**Yöntem 2: Resmi Redis APT Deposu (Daha güncel sürümler için)**

1.  **Gerekli Paketleri Kurma ve GPG Anahtarını Ekleme:**
    ```bash
    sudo apt install curl gpg lsb-release
    curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
    ```

2.  **Depoyu Ekleme:**
    ```bash
    echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list
    ```

3.  **Paket Listesini Güncelleme ve Kurulum:**
    ```bash
    sudo apt update
    sudo apt install redis -y
    # Veya Redis Stack için:
    # sudo apt install redis-stack-server -y
    ```

**Kurulum Sonrası (Tüm Yöntemler):**

*   **Redis Servisini Başlatma ve Etkinleştirme:**
    Dağıtım deposundan kurulan `redis-server` paketi genellikle servisi otomatik başlatır ve etkinleştirir. Resmi depodan kurulan `redis` paketi için manuel olarak başlatmak gerekebilir.
    ```bash
    sudo systemctl enable --now redis-server # Dağıtım paketi için (genellikle)
    # veya
    sudo systemctl enable --now redis # Resmi depo paketi için (genellikle)
    ```
    Servis adını doğrulamak için `systemctl status redis*` kullanılabilir.
