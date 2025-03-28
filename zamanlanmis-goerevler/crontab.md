# crontab ile Tekrarlayan Görevleri Zamanlama

`cron`, Unix benzeri işletim sistemlerinde belirli komutları veya betikleri (script) düzenli aralıklarla otomatik olarak çalıştırmak için kullanılan standart bir zamanlama daemon'ıdır (arka plan süreci). Çalıştırılacak görevler **crontab** (cron table) adı verilen dosyalarda tanımlanır.

## Crontab Dosyaları

Cron görevleri birkaç farklı yerde tanımlanabilir:

1.  **Kullanıcı Crontab'ları:** Her kullanıcının kendi zamanlanmış görevlerini tanımlayabileceği özel bir crontab dosyası vardır. Bu dosyalar genellikle `/var/spool/cron/crontabs/` (veya benzeri bir dizinde) saklanır ancak doğrudan düzenlenmezler. Kullanıcılar kendi crontab'larını yönetmek için `crontab` komutunu kullanır. Bu crontab'daki komutlar, o kullanıcının yetkileriyle çalışır.
2.  **Sistem Geneli Crontab (`/etc/crontab`):** Bu dosya, sistem genelindeki görevleri tanımlar. Kullanıcı crontab'larından farklı olarak, bu dosyada her satırda komutun hangi kullanıcı olarak çalıştırılacağı belirtilmelidir.
3.  **`/etc/cron.d/` Dizini:** Sistem yöneticileri veya paketler, zamanlanmış görev tanımlarını bu dizine ayrı dosyalar halinde koyabilirler. Bu dosyalardaki format genellikle `/etc/crontab` ile aynıdır (kullanıcı belirtilmesi gerekir).
4.  **Ön Tanımlı Zaman Aralıkları Dizini:** `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/` dizinlerine yerleştirilen çalıştırılabilir betikler, isimlerine uygun aralıklarla (`run-parts` komutu aracılığıyla, genellikle `/etc/crontab` içinden tetiklenerek) otomatik olarak çalıştırılır.

## Crontab Satır Formatı

Bir crontab dosyasındaki (kullanıcı crontab'ı veya `/etc/crontab`, `/etc/cron.d/` dosyaları) her satır bir zamanlanmış görevi temsil eder ve genellikle şu formatı takip eder:

```
# ┌───────────── dakika (0 - 59)
# │ ┌───────────── saat (0 - 23)
# │ │ ┌───────────── ayın günü (1 - 31)
# │ │ │ ┌───────────── ay (1 - 12) (veya Jan, Feb, Mar...)
# │ │ │ │ ┌───────────── haftanın günü (0 - 6) (Pazar=0 veya 7) (veya Sun, Mon...)
# │ │ │ │ │
# │ │ │ │ │
# * * * * * <kullanıcı_adı> <çalıştırılacak_komut>  <- Sadece /etc/crontab ve /etc/cron.d/ için kullanıcı adı gerekir
# * * * * * <çalıştırılacak_komut>              <- Kullanıcı crontab'ı için (crontab -e ile düzenlenen)
```

*   İlk beş alan zamanlamayı belirler.
*   `/etc/crontab` ve `/etc/cron.d/` dosyalarında altıncı alan komutun çalıştırılacağı kullanıcı adıdır. Kullanıcı crontab'larında bu alan bulunmaz, komut crontab sahibi kullanıcı olarak çalışır.
*   Geri kalan kısım ise çalıştırılacak komut veya betiktir.

**Özel Karakterler:**

*   **`*` (Yıldız):** İlgili alan için tüm olası değerleri ifade eder ("her"). Örneğin, dakika alanındaki `*` "her dakika" anlamına gelir.
*   **`,` (Virgül):** Değer listelerini ayırmak için kullanılır. Örneğin, saat alanındaki `8,12,18` "saat 8, 12 ve 18'de" anlamına gelir.
*   **`-` (Tire):** Değer aralıklarını belirtmek için kullanılır. Örneğin, haftanın günü alanındaki `1-5` "Pazartesiden Cumaya kadar" (Pazartesi=1) anlamına gelir.
*   **`/` (Bölü):** Adım değerlerini belirtmek için kullanılır. Örneğin, dakika alanındaki `*/15` "her 15 dakikada bir" (0, 15, 30, 45) anlamına gelir. Saat alanındaki `8-18/2` "saat 8'den 18'e kadar her 2 saatte bir" (8, 10, 12, 14, 16, 18) anlamına gelir.

**Özel Zaman Dizgeleri:** Bazı `cron` implementasyonları, yaygın zamanlamalar için özel dizgeleri destekler:

*   `@reboot`: Sistem başlangıcında bir kez çalıştırılır.
*   `@yearly` veya `@annually`: Yılda bir kez çalıştırılır (`0 0 1 1 *`).
*   `@monthly`: Ayda bir kez çalıştırılır (`0 0 1 * *`).
*   `@weekly`: Haftada bir kez çalıştırılır (`0 0 * * 0`).
*   `@daily` veya `@midnight`: Günde bir kez çalıştırılır (`0 0 * * *`).
*   `@hourly`: Saatte bir kez çalıştırılır (`0 * * * *`).

**Örnek Crontab Satırları:**

```bash
# Her gece 02:30'da yedekleme betiğini çalıştır
30 2 * * * /usr/local/bin/yedekle.sh

# Her Pazartesi sabah 05:00'te sistemi güncelle (apt örneği)
0 5 * * 1 apt update && apt upgrade -y

# Her 15 dakikada bir disk kullanımını logla
*/15 * * * * df -h >> /var/log/disk_kullanimi.log

# Her ayın 1'inde saat 04:05'te root olarak bir betik çalıştır (/etc/cron.d/ veya /etc/crontab içinde)
5 4 1 * * root /opt/aylik_rapor.sh

# Sistem her başladığında bir kontrol yap
@reboot /usr/local/bin/baslangic_kontrolu.sh
```

## `crontab` Komutu

Kullanıcılar kendi crontab dosyalarını yönetmek için `crontab` komutunu kullanır:

*   **`crontab -e`**: Mevcut kullanıcının crontab dosyasını düzenlemek için varsayılan metin düzenleyiciyi açar. İlk kez çalıştırıldığında genellikle hangi düzenleyiciyi kullanmak istediğinizi sorar. Dosyayı kaydedip çıktığınızda yeni zamanlanmış görevler aktif olur.
*   **`crontab -l`**: Mevcut kullanıcının crontab dosyasının içeriğini listeler.
*   **`crontab -r`**: Mevcut kullanıcının crontab dosyasını **komple siler**. **Dikkatli kullanılmalıdır!** Yanlışlıkla silmeye karşı genellikle onay istemez.
*   **`crontab -u <kullanıcı_adı> -e|-l|-r`**: (Sadece root tarafından) Belirtilen kullanıcının crontab dosyasını yönetmek için kullanılır.

## Ortam Değişkenleri ve Çıktı Yönetimi

*   **Ortam:** Cron görevleri, normal bir kullanıcı kabuğuna göre çok daha kısıtlı bir ortam değişkeni seti ile çalışır. Özellikle `PATH` değişkeni sınırlı olabilir. Bu nedenle, cron görevlerinde çalıştırılan komutların veya betiklerin içinde kullanılan diğer komutların tam yollarını belirtmek (örn. `/usr/bin/python3` yerine sadece `python3` yazmamak) genellikle daha güvenilirdir. Crontab dosyasının başına `PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin` gibi bir satır ekleyerek PATH'i ayarlamak da mümkündür.
*   **Çıktı:** Varsayılan olarak, bir cron görevinin standart çıktısı (stdout) veya standart hatası (stderr) olursa, bu çıktı crontab sahibi kullanıcıya e-posta olarak gönderilir (eğer sistemde bir posta transfer ajanı - MTA - yapılandırılmışsa).
    *   Çıktıyı bir dosyaya yönlendirmek için standart kabuk yönlendirmeleri kullanılır:
        ```bash
        * * * * * /path/to/command > /var/log/myjob.log 2>&1 
        ```
        (`>` üzerine yazar, `>>` sona ekler, `2>&1` stderr'i stdout'a yönlendirir).
    *   Tüm çıktıyı engellemek için `/dev/null`'a yönlendirilebilir:
        ```bash
        * * * * * /path/to/command > /dev/null 2>&1
        ```
    *   E-postanın gönderileceği adresi değiştirmek için crontab dosyasının başına `MAILTO` değişkeni eklenebilir:
        ```bash
        MAILTO="admin@example.com"
        * * * * * /path/to/command 
        ```
    *   E-posta gönderimini tamamen engellemek için `MAILTO` boş bırakılabilir:
        ```bash
        MAILTO=""
        * * * * * /path/to/command 
        ```

`cron`, sistem bakımı, yedekleme, raporlama gibi birçok görevi otomatikleştirmek için vazgeçilmez bir araçtır.
