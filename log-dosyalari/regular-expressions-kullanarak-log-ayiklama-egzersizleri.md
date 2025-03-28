# Log Dosyalarını İnceleme ve Filtreleme

Log dosyaları, sistemin işleyişi hakkında değerli bilgiler içerir, ancak genellikle çok büyük olabilirler. İhtiyaç duyulan bilgiyi bulmak için logları etkili bir şekilde incelemek ve filtrelemek önemlidir. Bu bölümde, hem geleneksel metin tabanlı log dosyaları hem de `journald` günlükleri için yaygın kullanılan araçlar ve teknikler ele alınacaktır.

## Geleneksel Log Dosyaları (`/var/log`)

Bu dosyalar genellikle düz metin formatındadır ve standart komut satırı araçlarıyla işlenebilir.

**1. Canlı Takip ve Basit Filtreleme (`tail -f | grep`)**

Bir log dosyasını gerçek zamanlı olarak takip etmek ve belirli anahtar kelimeleri içeren satırları anında görmek için `tail -f` ve `grep` kombinasyonu sıkça kullanılır:

```bash
# /var/log/syslog dosyasını takip et ve içinde "error" geçen satırları göster (büyük/küçük harf duyarsız)
tail -f /var/log/syslog | grep -i "error"

# /var/log/nginx/access.log dosyasını takip et ve 404 hata kodlarını içeren satırları göster
tail -f /var/log/nginx/access.log | grep ' 404 ' 
```
*   `tail -f`: Dosyanın sonunu gösterir ve yeni eklenen satırları sürekli olarak ekrana basar.
*   `grep`: Standart girdiden gelen satırlar içinde belirtilen kalıbı arar.

**2. Regex ile Gelişmiş Filtreleme (`grep -E`)**

Daha karmaşık kalıpları eşleştirmek için `grep -E` (veya `egrep`) ile düzenli ifadeler (regular expressions) kullanılır:

```bash
# Apache access_log dosyasında belirli bir IP adresinden gelen istekleri bul
grep -E '^192\.168\.1\.10 ' /var/log/apache2/access.log

# auth.log dosyasında başarısız SSH login denemelerini bul
grep -E 'sshd\[[0-9]+\]: Failed password for' /var/log/auth.log

# access_log dosyasından sadece GET isteklerini içeren satırları çıkar
grep -E '^(\S+) (\S+) (\S+) \[.*\] "GET ' /var/log/nginx/access.log
```

**3. Alanlara Göre İşleme (`awk`)**

Log satırları genellikle boşluk veya başka bir ayırıcı ile ayrılmış alanlardan oluşur. `awk`, bu alanlara göre işlem yapmak için çok güçlü bir araçtır:

```bash
# access_log dosyasındaki her satırın ilk alanını (IP adresi) yazdır
tail -n 50 /var/log/nginx/access.log | awk '{print $1}'

# Belirli bir IP adresinden gelen isteklerin sayısını bul
awk '$1 == "192.168.1.10" { count++ } END { print count }' /var/log/nginx/access.log

# 404 hatası veren isteklerin URL'lerini (7. alan) yazdır
awk '$9 == "404" { print $7 }' /var/log/nginx/access.log | head
```

**4. Metin Değiştirme (`sed`)**

Loglar üzerinde basit metin değiştirme veya ayıklama işlemleri için `sed` kullanılabilir:

```bash
# syslog dosyasındaki tüm "WARN" kelimelerini "UYARI" ile değiştir (sadece ekrana basar)
sed 's/WARN/UYARI/g' /var/log/syslog | less

# access_log dosyasından tarih/saat bilgisini çıkar
sed -E 's/^([^ ]+) ([^ ]+) ([^ ]+) \[.*\] (.*)/\1 \4/' /var/log/nginx/access.log | head
```

## `systemd-journald` Günlükleri (`journalctl`)

`journald`, logları yapılandırılmış bir formatta sakladığı için `journalctl` komutu güçlü filtreleme yetenekleri sunar.

**Canlı Takip:**
```bash
# Tüm günlükleri canlı takip et
sudo journalctl -f

# Belirli bir servisin günlüklerini canlı takip et
sudo journalctl -f -u nginx.service
```

**Filtreleme:**

*   **Servise Göre (`-u`):**
    ```bash
    sudo journalctl -u sshd.service
    ```
*   **Önem Derecesine Göre (`-p`):**
    ```bash
    # Sadece hata ve daha kritik mesajları göster
    sudo journalctl -p err 

    # Uyarı ve daha kritik mesajları göster
    sudo journalctl -p warning..alert 
    ```
*   **Zamana Göre (`--since`, `--until`):**
    ```bash
    sudo journalctl --since "1 hour ago"
    sudo journalctl --since "09:00" --until "10:30"
    ```
*   **Çekirdek Mesajları (`-k`):**
    ```bash
    sudo journalctl -k
    ```
*   **Belirli Önyükleme (`-b`):**
    ```bash
    sudo journalctl -b    # Mevcut önyükleme
    sudo journalctl -b -1 # Bir önceki önyükleme
    sudo journalctl --list-boots # Tüm önyüklemeleri listele
    ```
*   **Meta Veri Alanlarına Göre:** `journald` logları birçok meta veri alanı ile saklar (`_PID`, `_UID`, `_EXE`, `_SYSTEMD_UNIT`, `SYSLOG_FACILITY` vb.). Bu alanlara göre filtreleme yapılabilir:
    ```bash
    # Belirli bir PID'ye ait loglar
    sudo journalctl _PID=12345

    # Belirli bir çalıştırılabilir dosyaya ait loglar
    sudo journalctl /usr/sbin/sshd

    # Belirli bir syslog facility koduna ait loglar
    sudo journalctl SYSLOG_FACILITY=10 # authpriv için
    ```
*   **Metin Arama (`grep` ile):** `journalctl` çıktısı metin tabanlı olduğu için `grep` ile de filtrelenebilir:
    ```bash
    sudo journalctl -u nginx.service | grep "denied"
    ```

Logları etkili bir şekilde incelemek, sistem yönetimi ve sorun gidermenin önemli bir parçasıdır. Hem geleneksel araçları hem de `journalctl`'i bilmek, farklı durumlarla başa çıkmanıza yardımcı olur.
