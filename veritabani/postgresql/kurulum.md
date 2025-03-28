# PostgreSQL Kurulumu

Bu bölümde PostgreSQL'in farklı Linux dağıtımlarına nasıl kurulacağı anlatılacaktır. PostgreSQL, birçok dağıtımın kendi paket depolarında bulunur, ancak genellikle en güncel sürümü PostgreSQL Global Development Group (PGDG) tarafından sağlanan resmi depolardan kurmak tercih edilir.

## RHEL Tabanlı Dağıtımlar (AlmaLinux, Rocky Linux, CentOS Stream)

RHEL tabanlı sistemlerde PostgreSQL kurmak için genellikle PGDG deposunu eklemek en iyi yöntemdir.

1.  **PGDG Deposunu Ekleme:**
    Sisteminizdeki RHEL sürümüne uygun depo RPM'ini [PostgreSQL Yum Repository](https://www.postgresql.org/download/linux/redhat/) sayfasından bulup kurun. Örneğin, AlmaLinux 9 için:
    ```bash
    sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-9-x86_64/pgdg-redhat-repo-latest.noarch.rpm
    ```

2.  **Varsayılan Modülü Devre Dışı Bırakma (Gerekirse):**
    Dağıtımın kendi deposundaki PostgreSQL modülü çakışma yaratabilir. Bunu devre dışı bırakın:
    ```bash
    sudo dnf -qy module disable postgresql
    ```

3.  **PostgreSQL Sunucusunu Kurma:**
    İstediğiniz PostgreSQL sürümünü (örneğin, 16) kurun:
    ```bash
    sudo dnf install -y postgresql16-server
    ```
    (Paket adları sürüme göre değişir: `postgresql15-server`, `postgresql14-server` vb.)

4.  **Veritabanı Kümesini Başlatma (Initialize):**
    Kurulumdan sonra veritabanı depolama alanını başlatmanız gerekir:
    ```bash
    sudo /usr/pgsql-16/bin/postgresql-16-setup initdb
    ```
    (Yol ve komut sürüm numarasına göre değişir.)

5.  **PostgreSQL Servisini Başlatma ve Etkinleştirme:**
    Servisi başlatın ve sistem açılışında otomatik başlaması için etkinleştirin:
    ```bash
    sudo systemctl enable --now postgresql-16
    ```
    (Servis adı sürüm numarasına göre değişir.)

## Debian Tabanlı Dağıtımlar (Debian, Ubuntu)

Debian ve Ubuntu için de PGDG deposunu kullanmak güncel sürümlere erişim sağlar.

1.  **PGDG Deposunu Ekleme:**
    [PostgreSQL Apt Repository](https://www.postgresql.org/download/linux/debian/) veya [PostgreSQL Apt Repository (Ubuntu)](https://www.postgresql.org/download/linux/ubuntu/) sayfalarındaki adımları takip ederek sisteminize uygun depoyu ekleyin. Genellikle şu adımları içerir:
    *   PGDG imza anahtarını ekleme:
        ```bash
        sudo apt install curl ca-certificates gnupg
        curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/apt.postgresql.org.gpg >/dev/null
        ```
    *   Depo tanım dosyasını oluşturma (Örnek Ubuntu 22.04 Jammy için):
        ```bash
        sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
        ```

2.  **Paket Listesini Güncelleme:**
    ```bash
    sudo apt update
    ```

3.  **PostgreSQL Sunucusunu Kurma:**
    İstediğiniz PostgreSQL sürümünü (örneğin, 16) kurun:
    ```bash
    sudo apt install -y postgresql-16
    ```
    (Paket adları sürüme göre değişir: `postgresql-15`, `postgresql-14` vb.)

Kurulum tamamlandığında, Debian/Ubuntu sistemlerinde genellikle servis otomatik olarak başlatılır ve etkinleştirilir. Veritabanı kümesi de (`main` adıyla) otomatik olarak oluşturulur ve başlatılır. Servis durumunu kontrol edebilirsiniz:
```bash
sudo systemctl status postgresql@16-main
```
(Servis adı sürüm ve küme adına göre değişebilir.)
