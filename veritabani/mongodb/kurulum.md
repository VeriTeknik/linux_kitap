# MongoDB Kurulumu

Bu bölümde MongoDB'nin farklı Linux dağıtımlarına nasıl kurulacağı anlatılacaktır. MongoDB genellikle kendi resmi depoları aracılığıyla kurulur.

## RHEL Tabanlı Dağıtımlar (AlmaLinux, Rocky Linux, CentOS Stream)

MongoDB'nin resmi deposunu ekleyerek kurulum yapmak en güncel ve stabil yöntemi sağlar.

1.  **MongoDB Deposunu Ekleme:**
    MongoDB deposunu tanımlayan bir `.repo` dosyası oluşturun. Örneğin, `/etc/yum.repos.d/mongodb-org-7.0.repo` (MongoDB 7.0 için):
    ```ini
    [mongodb-org-7.0]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/7.0/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-7.0.asc
    ```
    *Not: `$releasever` sistem sürümünü (örn: 9) ifade eder. Güncel depo URL'si ve GPG anahtarı için [MongoDB RHEL Kurulum Kılavuzu](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-red-hat/) kontrol edilmelidir.*

2.  **MongoDB Paketlerini Kurma:**
    Depoyu ekledikten sonra MongoDB paketlerini kurun:
    ```bash
    sudo dnf install -y mongodb-org
    ```
    Bu komut genellikle `mongodb-org-server`, `mongodb-org-mongos`, `mongodb-org-database-tools-extra` gibi ilgili tüm paketleri kurar.

3.  **MongoDB Servisini Başlatma ve Etkinleştirme:**
    MongoDB servisini (`mongod`) başlatın ve sistem açılışında otomatik başlaması için etkinleştirin:
    ```bash
    sudo systemctl enable --now mongod
    ```

## Debian Tabanlı Dağıtımlar (Debian, Ubuntu)

Debian ve Ubuntu için de MongoDB'nin resmi deposunu kullanmak önerilir.

1.  **MongoDB İmza Anahtarını Ekleme:**
    Gerekli araçları kurun ve MongoDB GPG anahtarını ekleyin:
    ```bash
    sudo apt update
    sudo apt install gnupg curl
    curl -fsSL https://pgp.mongodb.com/server-7.0.asc | \
       sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
       --dearmor
    ```
    *Not: Güncel anahtar URL'si için [MongoDB Debian Kurulum Kılavuzu](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-debian/) veya [MongoDB Ubuntu Kurulum Kılavuzu](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/) kontrol edilmelidir.*

2.  **MongoDB Depo Listesini Oluşturma:**
    Sistem mimarinize ve sürümünüze uygun depo listesini oluşturun. Örneğin, Ubuntu 22.04 (Jammy) için:
    ```bash
    echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
    ```

3.  **Paket Listesini Güncelleme:**
    Yeni depoyu ekledikten sonra paket listesini güncelleyin:
    ```bash
    sudo apt update
    ```

4.  **MongoDB Paketlerini Kurma:**
    MongoDB paketlerini kurun:
    ```bash
    sudo apt install -y mongodb-org
    ```

5.  **MongoDB Servisini Başlatma ve Etkinleştirme:**
    MongoDB servisini (`mongod`) başlatın ve sistem açılışında otomatik başlaması için etkinleştirin:
    ```bash
    sudo systemctl enable --now mongod
    ```

Kurulum sonrası servis durumunu kontrol edebilirsiniz:
```bash
sudo systemctl status mongod
```
