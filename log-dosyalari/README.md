# Log Dosyaları (Günlük Kayıtları)

Log dosyaları (günlük kayıtları), Linux ve Unix benzeri sistemlerin işleyişini anlamak, sorunları teşhis etmek ve güvenlik olaylarını takip etmek için hayati öneme sahip bilgiler içerir. Sistemde çalışan servisler, çekirdek ve uygulamalar, önemli olayları, uyarıları ve hataları bu dosyalara kaydeder.

## Modern Günlükleme: `systemd-journald`

Modern Linux dağıtımlarının çoğu, `systemd` init sistemi ile birlikte gelen **`systemd-journald`** servisini merkezi günlükleme sistemi olarak kullanır. `journald`, günlükleri yapılandırılmış, indekslenmiş bir ikili (binary) formatta toplar ve saklar.

**`journald`'nin Avantajları:**
*   **Merkezi Toplama:** Çekirdek (`kmsg`), servislerin standart çıktı/hataları (stdout/stderr), syslog ve diğer kaynaklardan gelen logları tek bir yerde toplar.
*   **Yapılandırılmış Veri:** Loglar sadece metin değil, aynı zamanda PID, UID, GID, servis adı (unit), önyükleme ID'si gibi meta verilerle birlikte saklanır. Bu, filtrelemeyi ve analizi kolaylaştırır.
*   **İndeksleme:** Hızlı arama ve filtreleme imkanı sunar.
*   **Depolama:** Günlükler genellikle `/var/log/journal/` (kalıcı depolama etkinse) veya `/run/log/journal/` (geçici, sadece mevcut önyükleme için) altında saklanır. Depolama boyutu ve süresi yapılandırılabilir.

**`journalctl` Komutu:**
`journald` tarafından toplanan günlüklere erişmek ve sorgulamak için `journalctl` komutu kullanılır:
```bash
# Tüm günlükleri göster (en yeniden en eskiye, less ile sayfalanmış)
journalctl

# Sadece mevcut önyüklemedeki günlükleri göster
journalctl -b

# Sadece çekirdek mesajlarını göster (dmesg gibi)
journalctl -k

# Belirli bir servisin (unit) günlüklerini göster
journalctl -u sshd.service

# Belirli bir PID'ye ait günlükleri göster
journalctl _PID=1234

# Belirli bir zaman aralığındaki günlükleri göster
journalctl --since "yesterday"
journalctl --since "2023-10-26 10:00:00" --until "2023-10-26 11:00:00"

# Günlükleri canlı olarak takip et (-f: follow)
journalctl -f

# Hata (error) seviyesindeki ve daha kritik günlükleri göster (-p: priority)
journalctl -p err 
# Seviyeler: emerg (0), alert (1), crit (2), err (3), warning (4), notice (5), info (6), debug (7)

# Daha fazla örnek ve filtreleme seçeneği için: man journalctl
```

## Geleneksel Günlükleme: `syslog` ve `/var/log`

`journald`'nin yaygınlaşmasından önce, Linux sistemleri logları toplamak ve yönetmek için **syslog** protokolünü ve `syslogd`, `rsyslog`, `syslog-ng` gibi daemon'ları kullanırdı. Bu daemon'lar, farklı kaynaklardan gelen log mesajlarını alır, filtreler ve genellikle `/var/log/` dizini altındaki çeşitli metin dosyalarına yazardı.

Modern sistemlerde bile, `rsyslog` gibi bir syslog daemon'ı genellikle `journald` ile birlikte çalışır. `rsyslog`, `journald`'den logları okuyabilir ve bunları geleneksel `/var/log/` dosyalarına (örn. `/var/log/syslog`, `/var/log/auth.log`, `/var/log/messages`) yazabilir. Bu, eski araçlarla uyumluluk veya logların metin formatında saklanması gerektiğinde kullanışlıdır.

**Syslog Temelleri:**
Syslog mesajları genellikle iki ana bilgi içerir:
*   **Facility (Kaynak):** Mesajı üreten programın türünü belirtir (örn. `kern`, `user`, `mail`, `daemon`, `auth`, `syslog`, `lpr`, `news`, `cron`, `local0` - `local7`).
*   **Severity/Priority (Önem Derecesi):** Mesajın önemini belirtir (yukarıda `journalctl -p` kısmında listelenmiştir: `emerg`, `alert`, `crit`, `err`, `warning`, `notice`, `info`, `debug`).

`rsyslog` gibi daemon'lar, `/etc/rsyslog.conf` ve `/etc/rsyslog.d/` altındaki dosyalarda tanımlanan kurallara göre, hangi facility/severity kombinasyonuna sahip mesajların hangi dosyaya yazılacağını veya nereye (örn. uzak sunucu) gönderileceğini belirler.

**Yaygın Geleneksel Log Dosyaları (`/var/log/` altında):**
*   `/var/log/messages` veya `/var/log/syslog`: Genel sistem mesajları, birçok servisin varsayılan log hedefi (dağıtıma göre değişir). `journald` sonrası önemi azalmıştır.
*   `/var/log/auth.log` veya `/var/log/secure`: Kimlik doğrulama ile ilgili mesajlar (login, sudo, sshd vb.).
*   `/var/log/kern.log`: Çekirdek mesajları.
*   `/var/log/dmesg`: Sistem başlangıcındaki çekirdek halka arabelleği (ring buffer) mesajları. `dmesg` komutu ile de okunabilir.
*   `/var/log/boot.log`: Sistem başlangıç süreciyle ilgili mesajlar.
*   `/var/log/cron` veya `/var/log/syslog`: Zamanlanmış görev (cron) logları.
*   `/var/log/maillog` veya `/var/log/mail.log`: Posta sunucusu logları.
*   `/var/log/httpd/` veya `/var/log/apache2/`: Apache web sunucusu logları (`access_log`, `error_log`).
*   `/var/log/nginx/`: Nginx web sunucusu logları.
*   `/var/log/mysql/` veya `/var/log/mariadb/`: Veritabanı sunucusu logları.

Bu dosyalar genellikle düz metin formatındadır ve `cat`, `less`, `tail`, `grep`, `awk`, `sed` gibi standart araçlarla incelenebilir. `tail -f <dosya>` komutu, bir log dosyasını canlı olarak takip etmek için sıkça kullanılır.

## Log Rotasyonu (`logrotate`)

Log dosyaları zamanla çok büyüyebilir ve disk alanını doldurabilir. `logrotate` aracı, log dosyalarını düzenli aralıklarla (günlük, haftalık, aylık) otomatik olarak arşivlemek, sıkıştırmak ve eskiyenleri silmek için kullanılır.

*   **Yapılandırma:** Ana yapılandırma dosyası `/etc/logrotate.conf`'tur. Uygulamaya özel kurallar ise genellikle `/etc/logrotate.d/` dizini altındaki dosyalarda tanımlanır.
*   **Çalışma:** `logrotate` genellikle bir cron işi tarafından (örn. `/etc/cron.daily/logrotate`) günde bir kez çalıştırılır.
*   **Örnek Yapılandırma (`/etc/logrotate.d/apache2`):**
    ```
    /var/log/apache2/*.log {
        daily          # Günlük olarak rotate et
        missingok      # Log dosyası yoksa hata verme
        rotate 14      # 14 gün boyunca eski logları sakla
        compress       # Eski logları gzip ile sıkıştır
        delaycompress  # Bir sonraki rotasyonda sıkıştır (servisin yeniden başlatılmasına zaman tanır)
        notifempty     # Log dosyası boşsa rotate etme
        create 640 root adm # Yeni log dosyasını belirtilen izin/sahip/grup ile oluştur
        sharedscripts  # postrotate/prerotate betiklerini tüm loglar için bir kez çalıştır
        postrotate
            # Apache'yi yeniden yükle (logları tekrar açması için)
            if /etc/init.d/apache2 status > /dev/null ; then \
                /etc/init.d/apache2 reload > /dev/null; \
            fi;
            # veya systemd için: systemctl reload apache2.service
        endscript
        prerotate
            # Rotasyondan önce yapılacaklar (varsa)
        endscript
    }
    ```
*   **Test Etme:** Bir yapılandırma dosyasının nasıl çalışacağını test etmek için `-d` (debug) seçeneği kullanılır:
    ```bash
    sudo logrotate -d /etc/logrotate.d/apache2
    ```
*   **Zorla Çalıştırma:** Rotasyonu hemen tetiklemek için `-f` (force) seçeneği kullanılır:
    ```bash
    sudo logrotate -f /etc/logrotate.conf
    ```

Etkili log yönetimi, hem `journald`'nin modern yeteneklerini hem de `logrotate` gibi geleneksel araçları anlamayı gerektirir.

## Uygulama Logları ve Özel Rotasyon

Sistem loglarının yanı sıra, çalıştırdığınız uygulamaların (web sunucuları, veritabanları, özel uygulamalar vb.) kendi log dosyaları olacaktır. Bu logların konumları uygulamaya göre değişir, ancak genellikle `/var/log/` altında kendi alt dizinlerinde (örn. `/var/log/nginx/`) veya uygulamanın kendi dizininde bulunurlar.

Web hosting gibi ortamlarda, her kullanıcı veya sanal ana bilgisayar (virtual host) için ayrı log dosyaları tutmak yaygındır. Bu loglar genellikle kullanıcının ev dizini altında özel bir `logs` klasöründe saklanabilir (örn. `/home/kullanici_adi/logs/`).

Bu özel log dosyalarının da düzenli olarak rotate edilmesi gerekir. `logrotate` bunun için de kullanılabilir. `/etc/logrotate.d/` altına özel bir yapılandırma dosyası ekleyerek bu logları yönetebilirsiniz.

**Örnek: Kullanıcı Loglarını Rotate Etme (`/etc/logrotate.d/virtualhosts`)**

Aşağıdaki örnek, `/home/` altındaki tüm kullanıcıların `logs` dizinlerindeki `.log` uzantılı dosyaları günlük olarak rotate eder, 7 gün saklar ve sıkıştırır:

```
/home/*/logs/*.log {
    daily          # Günlük rotate et
    rotate 7       # 7 eski log dosyası sakla
    missingok      # Log dosyası yoksa hata verme
    compress       # Sıkıştır
    delaycompress  # Bir sonraki rotasyonda sıkıştır
    notifempty     # Boşsa rotate etme
    create 0640 kullanici_grubu www-data # Yeni log dosyasını uygun izin/sahip/grup ile oluştur (kullanici_grubu yerine uygun grup adı yazılmalı)
    sharedscripts  # Betikleri bir kez çalıştır
    postrotate
        # İlgili servise logları yeniden açması için sinyal gönder (gerekirse)
        # Örneğin Apache için:
        # if systemctl is-active apache2.service > /dev/null ; then
        #    systemctl reload apache2.service > /dev/null
        # fi
    endscript
}
```
Bu yapılandırmayı test etmek için `sudo logrotate -d /etc/logrotate.d/virtualhosts` komutunu kullanabilirsiniz. Uygulamanızın log dosyalarını doğru şekilde yeniden açması için `postrotate` betiğini uygun şekilde ayarlamak önemlidir.
