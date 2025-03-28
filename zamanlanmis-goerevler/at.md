# `at` ile Tek Seferlik Görev Zamanlama

`cron` tekrarlayan görevler için kullanılırken, **`at`** komutu belirli komutların veya betiklerin gelecekte **belirli bir zamanda sadece bir kez** çalıştırılmasını sağlamak için kullanılır.

## `atd` Servisi

`at` komutu ile zamanlanan görevlerin çalıştırılabilmesi için sistemde `atd` (at daemon) servisinin çalışıyor olması gerekir. Çoğu dağıtımda varsayılan olarak kurulu ve etkin olabilir, ancak kontrol etmek gerekebilir:

```bash
# Servisin durumunu kontrol et
sudo systemctl status atd

# Gerekirse başlat ve etkinleştir
# sudo systemctl start atd
# sudo systemctl enable atd
```

## Görev Zamanlama

`at` komutuna çalıştırılacak zaman parametre olarak verilir. Zaman belirtildikten sonra, `at` komutu standart girdiden çalıştırılacak komutları okumaya başlar. Komutları girdikten sonra, genellikle yeni bir satırda `Ctrl+D` tuş kombinasyonuna basarak girdi sonlandırılır ve görev zamanlanır.

**Zaman Belirtme Formatları:**

`at` komutu oldukça esnek bir zaman belirtme formatını anlar:

*   **Kesin Zaman:**
    *   `HH:MM`: Bugün belirtilen saatte (eğer saat geçmişse yarın). Örn: `at 14:30`
    *   `HH:MM YYYY-MM-DD`: Belirtilen tarih ve saatte. Örn: `at 09:00 2025-12-25`
    *   `noon` (öğlen), `midnight` (gece yarısı), `teatime` (16:00) gibi anahtar kelimeler.
    *   `HH:MM <gün_adı>`: Gelecek belirtilen günde. Örn: `at 11:00 Sunday`
*   **Göreceli Zaman:**
    *   `now + <sayı> <birim>`: Şu andan itibaren belirtilen süre sonra. Birimler: `minutes`, `hours`, `days`, `weeks`. Örn: `at now + 1 hour`, `at now + 30 minutes`
    *   `HH:MM tomorrow`: Yarın belirtilen saatte.
    *   `HH:MM next <birim>`: Gelecek hafta/ay/yıl belirtilen saatte. Örn: `at 10:00 next week`

**Örnek Kullanım (İnteraktif):**

```bash
$ at 10:30 tomorrow 
warning: commands will be executed using /bin/sh
at> echo "Bu mesaj yarın sabah 10:30'da loglanacak" >> /home/user/at_test.log
at> echo "İkinci komut" >> /home/user/at_test.log
at> <Ctrl+D>  # Girdiyi bitir
job 1 at Mon Mar 31 10:30:00 2025 
```
Bu komutlar, belirtilen zamanda (`Mon Mar 31 10:30:00 2025`) `/bin/sh` kabuğu kullanılarak çalıştırılacaktır.

**Örnek Kullanım (Pipe ile):**

Tek bir komutu zamanlamak için `echo` ve pipe kullanılabilir:
```bash
echo "/usr/local/bin/gece_yarisi_betigi.sh" | at midnight
```

**Örnek Kullanım (Dosyadan):**

Çalıştırılacak komutları içeren bir dosyayı `-f` seçeneği ile belirtebilirsiniz:
```bash
# komutlar.txt dosyasının içeriği:
# echo "Dosyadan gelen görev"
# date

at -f komutlar.txt now + 5 minutes 
```

## Zamanlanmış Görevleri Yönetme

*   **Görevleri Listeleme (`atq` veya `at -l`):**
    Kullanıcının bekleyen `at` görevlerini listeler. Her görevin bir iş numarası (job ID) vardır.
    ```bash
    $ atq
    1   Mon Mar 31 10:30:00 2025 a user
    2   Tue Apr  1 00:00:00 2025 a user
    ```
*   **Görevi Silme (`atrm` veya `at -d`):**
    Belirtilen iş numarasına sahip görevi kuyruktan kaldırır.
    ```bash
    atrm 1
    # veya
    at -d 2 
    ```
*   **Görevin İçeriğini Görme (`at -c`):**
    Belirtilen iş numarasına sahip görevin çalıştıracağı komutları (ve ortam değişkenlerini) gösterir.
    ```bash
    at -c 1 
    ```

## Erişim Kontrolü

Hangi kullanıcıların `at` komutunu kullanabileceği `/etc/at.allow` ve `/etc/at.deny` dosyaları ile kontrol edilebilir:
*   Eğer `/etc/at.allow` dosyası varsa, sadece bu dosyada listelenen kullanıcılar `at` kullanabilir.
*   Eğer `/etc/at.allow` yoksa ama `/etc/at.deny` varsa, bu dosyada listelenen kullanıcılar *dışındaki* herkes `at` kullanabilir.
*   Eğer iki dosya da yoksa, genellikle sadece root kullanıcısı `at` kullanabilir (dağıtıma göre değişebilir).

`at` komutu, tek seferlik görevleri basitçe zamanlamak için kullanışlı bir araçtır. Tekrarlayan görevler için `cron` veya systemd timer'ları daha uygundur.
