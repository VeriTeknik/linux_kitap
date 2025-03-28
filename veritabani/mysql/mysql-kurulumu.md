# MySQL / MariaDB Kurulumu

MySQL veya onun popüler topluluk çatalı olan MariaDB'yi Linux sisteminize kurmak için genellikle dağıtımınızın paket yöneticisini kullanabilirsiniz. Çoğu modern dağıtım (Debian, Ubuntu, Fedora, RHEL 8+, CentOS Stream 8+, AlmaLinux, Rocky Linux) varsayılan olarak MariaDB'yi sunar. Oracle'ın resmi MySQL Community Server sürümünü kurmak isterseniz, genellikle önce Oracle'ın kendi yazılım deposunu sisteme eklemeniz gerekir.

Bu bölümde her iki veritabanı sunucusunun yaygın dağıtımlardaki temel kurulum adımları ve ilk güvenlik yapılandırması ele alınacaktır.

## 1. MariaDB Kurulumu (Yaygın Dağıtım Depolarından)

MariaDB genellikle dağıtımın ana depolarında bulunur ve kurulumu basittir.

*   **RHEL Tabanlı (dnf):**
    ```bash
    # MariaDB sunucusunu ve istemcisini kur
    sudo dnf install mariadb-server -y

    # Servisi başlat ve sistem başlangıcında etkinleştir
    sudo systemctl start mariadb
    sudo systemctl enable mariadb

    # İlk güvenlik ayarlarını yap (root şifresi belirle, test db kaldır vb.)
    sudo mariadb-secure-installation 
    ```
*   **Debian Tabanlı (apt):**
    ```bash
    # MariaDB sunucusunu ve istemcisini kur
    sudo apt update
    sudo apt install mariadb-server -y

    # Servisin otomatik başlamış ve etkinleştirilmiş olması gerekir, kontrol et:
    # sudo systemctl status mariadb

    # İlk güvenlik ayarlarını yap
    sudo mariadb-secure-installation
    ```

**`mariadb-secure-installation` Betiği:**
Bu betik, kurulumdan sonra çalıştırılması **şiddetle tavsiye edilen** interaktif bir güvenlik yapılandırma aracıdır. Size aşağıdaki adımlarda yol gösterir:
*   Mevcut root şifresini sorma (ilk kurulumda genellikle boştur, Enter ile geçilir).
*   Root kullanıcısı için bir şifre belirleme veya Unix soket kimlik doğrulamasını kullanma seçeneği sunma.
*   Anonim kullanıcıları kaldırma.
*   Root kullanıcısının uzaktan bağlanmasını engelleme (önerilir).
*   Test veritabanını ve erişimini kaldırma.
*   Yetki tablolarını yeniden yükleme.

Genellikle tüm sorulara 'Y' (Evet) yanıtı vermek iyi bir başlangıç noktasıdır.

## 2. MySQL Community Server Kurulumu (Oracle Deposundan)

Oracle'ın resmi MySQL sürümünü kurmak için genellikle önce MySQL APT veya YUM deposunu sisteme eklemeniz gerekir.

1.  **MySQL Depo RPM/DEB Paketini İndirme:**
    MySQL Community İndirme sayfasından (`https://dev.mysql.com/downloads/repo/`) sisteminize uygun depo yapılandırma paketini (.rpm veya .deb) indirin. Örneğin:
    ```bash
    # RHEL/CentOS/Fedora için (örnek, güncel sürümü kontrol edin)
    wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 

    # Debian/Ubuntu için (örnek, güncel sürümü kontrol edin)
    wget https://dev.mysql.com/get/mysql-apt-config_0.8.29-1_all.deb
    ```
2.  **Depo Paketini Kurma:**
    ```bash
    # RHEL/CentOS/Fedora
    sudo rpm -ivh mysql80-community-release-el9-1.noarch.rpm

    # Debian/Ubuntu
    sudo dpkg -i mysql-apt-config_0.8.29-1_all.deb
    # (Kurulum sırasında hangi MySQL sürümünü istediğinizi soran bir ekran çıkabilir)
    sudo apt update # Depo eklendikten sonra listeyi güncelle
    ```
3.  **MySQL Server Kurulumu:**
    ```bash
    # RHEL/CentOS/Fedora
    sudo dnf install mysql-community-server -y

    # Debian/Ubuntu
    sudo apt install mysql-server -y 
    # (Kurulum sırasında root şifresi belirlemeniz istenebilir)
    ```
4.  **Servisi Başlatma ve Etkinleştirme:**
    ```bash
    sudo systemctl start mysqld # Servis adı genellikle mysqld'dir
    sudo systemctl enable mysqld
    ```
5.  **İlk Güvenlik Ayarları (`mysql_secure_installation`):**
    MySQL kurulumundan sonra bu betiği çalıştırmak **çok önemlidir**.
    ```bash
    sudo mysql_secure_installation
    ```
    Bu betik, `mariadb-secure-installation`'a benzer şekilde çalışır:
    *   Validate Password Component'i etkinleştirme seçeneği sunar (güçlü şifre politikası).
    *   Root şifresini belirlemenizi veya değiştirmenizi ister. (MySQL 5.7+ sürümlerinde kurulum sırasında geçici bir şifre `/var/log/mysqld.log` dosyasına yazılmış olabilir, ilk girişte bu sorulabilir).
    *   Anonim kullanıcıları kaldırır.
    *   Uzaktan root girişini engeller.
    *   Test veritabanını kaldırır.
    *   Yetki tablolarını yeniden yükler.

Kurulum tamamlandıktan ve güvenlik ayarları yapıldıktan sonra, veritabanı sunucunuz kullanıma hazırdır. Bir sonraki adım genellikle [servisi yönetmek](mysql-servisini-calistirmak.md) ve [veritabanları/tablolar oluşturmaktır](veritabani-ve-tablo-olusturmak.md).
