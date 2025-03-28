# Servislerin Yönetimi (System V)

System V init (SysVinit) sisteminde, arka planda çalışan servisler (daemon'lar) genellikle `/etc/init.d/` (veya RHEL tabanlı sistemlerde `/etc/rc.d/init.d/`) dizininde bulunan kabuk betikleri (shell scripts) aracılığıyla yönetilir.

## Init Betikleri

Bu betikler, genellikle aşağıdaki gibi standart argümanları kabul eder:
*   `start`: Servisi başlatır.
*   `stop`: Servisi durdurur.
*   `restart`: Servisi durdurup yeniden başlatır.
*   `reload`: Servisin yapılandırma dosyalarını yeniden yüklemesini sağlar (eğer destekliyorsa).
*   `status`: Servisin çalışıp çalışmadığını kontrol eder.

Betikler doğrudan çalıştırılabilir (root yetkisiyle):
```bash
sudo /etc/init.d/apache2 start
sudo /etc/init.d/ssh status
```

## `service` Komutu

Çoğu SysVinit sisteminde, init betiklerini daha kolay yönetmek için bir `service` komutu bulunur. Bu komut, betiğin tam yolunu yazma ihtiyacını ortadan kaldırır:
```bash
sudo service apache2 start
sudo service ssh status
sudo service networking restart
```

## Servisleri Başlangıçta Etkinleştirme/Devre Dışı Bırakma

Bir servisin sistem açıldığında belirli bir çalışma seviyesinde (runlevel) otomatik olarak başlayıp başlamayacağı, `/etc/rc<runlevel>.d/` dizinlerindeki sembolik linkler aracılığıyla kontrol edilir. Bu linkleri yönetmek için dağıtıma özgü araçlar kullanılır:

*   **RHEL/CentOS Tabanlı Sistemler (`chkconfig`):**
    ```bash
    # httpd servisini 2, 3, 4, 5 runlevel'larında etkinleştir
    sudo chkconfig httpd on 

    # httpd servisini tüm runlevel'larda devre dışı bırak
    sudo chkconfig httpd off

    # Servislerin durumunu listele
    sudo chkconfig --list 
    ```
*   **Debian/Ubuntu Tabanlı Sistemler (`update-rc.d`):**
    ```bash
    # apache2 servisini varsayılan runlevel'larda etkinleştir
    sudo update-rc.d apache2 enable

    # apache2 servisini varsayılan runlevel'larda devre dışı bırak
    sudo update-rc.d apache2 disable

    # Belirli runlevel'lar için manuel link yönetimi (daha az yaygın)
    # sudo update-rc.d apache2 defaults # Varsayılan linkleri oluşturur
    # sudo update-rc.d apache2 remove  # Tüm linkleri kaldırır
    ```
    (Not: Eski Debian/Ubuntu sürümlerinde `sysv-rc-conf` gibi metin tabanlı araçlar da bulunabilir.)

SysVinit servis yönetimi, betiklere ve sembolik linklere dayanır. Modern systemd sistemlerindeki `systemctl` komutu, servis yönetimini daha merkezi ve standart hale getirmiştir.
