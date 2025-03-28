# Örnek systemd Servis Birimi Oluşturma

Kendi yazdığınız bir uygulamayı veya paket yöneticisiyle kurulduğunda otomatik olarak bir servis birimi (unit) oluşturmayan bir yazılımı systemd ile yönetmek için özel bir `.service` birim dosyası oluşturabilirsiniz.

## Birim Dosyasının Konumu

Sistem yöneticisi tarafından oluşturulan özel birim dosyaları genellikle `/etc/systemd/system/` dizinine yerleştirilir. Dosya adı, servisin adıyla aynı olmalı ve `.service` uzantısıyla bitmelidir (örn. `/etc/systemd/system/benimuygulamam.service`).

## Örnek `.service` Dosyası

Aşağıda, `/opt/benimuygulamam/app.py` adresindeki bir Python uygulamasını `uygulama_user` kullanıcısı olarak çalıştıran basit bir servis birimi örneği verilmiştir:

```ini
[Unit]
Description=Benim Özel Uygulama Servisim
After=network.target # Ağ bağlantısı hazır olduktan sonra başlasın

[Service]
# Çalıştırılacak Kullanıcı ve Grup
User=uygulama_user
Group=uygulama_group

# Çalışma Dizini
WorkingDirectory=/opt/benimuygulamam/

# Çalıştırılacak Ana Komut
ExecStart=/usr/bin/python3 /opt/benimuygulamam/app.py --config /etc/benimuygulamam.conf

# Yeniden Başlatma Politikası (örn. hata durumunda)
Restart=on-failure 
# Diğer seçenekler: no, always, on-success, on-abnormal, on-abort

# Servis Tipi (daemon olmayan basit süreçler için 'simple' yeterlidir)
Type=simple 

# (İsteğe Bağlı) Başlamadan önce çalıştırılacak komut
# ExecStartPre=/opt/benimuygulamam/hazirlik_betigi.sh

# (İsteğe Bağlı) Durdurulduktan sonra çalıştırılacak komut
# ExecStopPost=/opt/benimuygulamam/temizlik_betigi.sh

[Install]
WantedBy=multi-user.target # Hangi hedef (target) etkinleştirildiğinde bu servisin de etkinleştirileceğini belirtir
                           # multi-user.target genellikle metin tabanlı çok kullanıcılı sistemler için kullanılır
```

**Açıklamalar:**

*   **`[Unit]` Bölümü:**
    *   `Description`: Servisin ne işe yaradığını açıklayan kısa bir metin. `systemctl status` çıktısında görünür.
    *   `After`: Bu servisin başlamadan önce hangi diğer unit'lerin (servisler, target'lar vb.) başlamış olması gerektiğini belirtir. Bağımlılıkları tanımlar. `network.target` ağın hazır olduğunu, `syslog.target` günlüklemenin hazır olduğunu belirtmek için sıkça kullanılır. `Wants` veya `Requires` gibi başka bağımlılık türleri de vardır.
*   **`[Service]` Bölümü:**
    *   `User`, `Group`: Servisin hangi kullanıcı ve grup kimliğiyle çalıştırılacağını belirtir. Güvenlik açısından root olmayan özel bir kullanıcı oluşturmak genellikle iyi bir pratiktir.
    *   `WorkingDirectory`: `ExecStart` komutunun çalıştırılacağı dizini belirtir.
    *   `ExecStart`: Servisi başlatmak için çalıştırılacak ana komutu ve argümanlarını belirtir. Komutun tam yolu yazılmalıdır.
    *   `Restart`: Servis beklenmedik bir şekilde durduğunda systemd'nin onu yeniden başlatıp başlatmayacağını ve hangi koşullarda başlatacağını belirler.
    *   `Type`: Servisin başlangıç davranışını belirtir. `simple` (varsayılan), `forking` (eski daemon'lar için), `oneshot` (tek seferlik görevler için), `notify` (servis hazır olduğunda systemd'ye bildirim gönderir) gibi tipler bulunur.
    *   `ExecStartPre`, `ExecStartPost`, `ExecStop`, `ExecStopPost`: Servis yaşam döngüsünün farklı aşamalarında çalıştırılacak ek komutları tanımlar.
*   **`[Install]` Bölümü:**
    *   `WantedBy`: `systemctl enable` komutu çalıştırıldığında, bu servisin hangi target birimi için etkinleştirileceğini (yani hangi target başlatıldığında bu servisin de başlatılacağını) belirtir. Çoğu ağ servisi için `multi-user.target` uygun bir değerdir. Grafiksel uygulamalarla ilgili servisler `graphical.target` kullanabilir.

## Birim Dosyasını Kullanma

1.  Yukarıdaki içeriği `/etc/systemd/system/benimuygulamam.service` gibi bir dosyaya kaydedin.
2.  Dosya izinlerini ayarlayın (genellikle `644` yeterlidir): `sudo chmod 644 /etc/systemd/system/benimuygulamam.service`.
3.  systemd'nin yeni veya değiştirilmiş birim dosyasını tanıması için daemon'u yeniden yükleyin:
    ```bash
    sudo systemctl daemon-reload
    ```
4.  Servisi başlangıçta çalışacak şekilde etkinleştirin ve hemen başlatın:
    ```bash
    sudo systemctl enable --now benimuygulamam.service
    ```
5.  Servisin durumunu kontrol edin:
    ```bash
    systemctl status benimuygulamam.service
    ```

Artık özel uygulamanız bir systemd servisi olarak yönetilebilir. Detaylı bilgi ve tüm seçenekler için `systemd.unit(5)`, `systemd.service(5)`, `systemd.exec(5)` gibi man sayfalarına bakabilirsiniz.
