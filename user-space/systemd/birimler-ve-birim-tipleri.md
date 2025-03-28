# Birimler (Units) ve Birim Tipleri

`systemd`, yönettiği tüm kaynakları ve eylemleri **birim (unit)** adı verilen nesneler aracılığıyla tanımlar. Her birim, belirli bir türdeki bir kaynağı veya görevi temsil eder ve bir yapılandırma dosyası (unit file) ile tanımlanır. Bu dosyalar, sistemin nasıl başlatılacağını, servislerin nasıl çalışacağını, cihazların nasıl yönetileceğini ve diğer birçok sistem davranışını belirler.

## Birim Dosyalarının Konumları

Birim dosyaları genellikle aşağıdaki dizinlerde bulunur ve belirli bir öncelik sırasına göre okunur:

1.  **/etc/systemd/system/:** Sistem yöneticisi tarafından oluşturulan veya düzenlenen birim dosyaları. Buradaki dosyalar, diğer dizinlerdeki aynı isimli dosyaları geçersiz kılar (override eder) veya tamamlar. Özel servisler veya varsayılan davranışları değiştirmek için kullanılır.
2.  **/run/systemd/system/:** Çalışma zamanında (runtime) oluşturulan birim dosyaları. `/etc/`'deki dosyalara göre önceliklidir.
3.  **/usr/lib/systemd/system/:** Kurulan paketler tarafından sağlanan varsayılan birim dosyaları (vendor-supplied). Bu dosyalar doğrudan düzenlenmemelidir; değişiklik yapmak için dosya `/etc/systemd/system/` altına kopyalanmalı veya `systemctl edit <unit>` komutu kullanılmalıdır.

`systemd`, bir unit'i yüklerken bu dizinleri belirtilen sırayla kontrol eder ve bulduğu ilk eşleşmeyi kullanır.

## Yaygın Birim Tipleri

Birim dosyalarının adları, temsil ettikleri kaynağın türünü belirten bir uzantı ile biter. En yaygın birim tipleri şunlardır:

*   **`.service`:** Sistem servislerini (daemon'ları) tanımlar. En sık kullanılan birim tipidir. Bir servisin nasıl başlatılacağını, durdurulacağını, hangi kullanıcıyla çalışacağını, bağımlılıklarını ve diğer davranışlarını belirler. (Örn: `sshd.service`, `nginx.service`)
*   **`.socket`:** Bir ağ soketini, IPC soketini veya FIFO'yu temsil eder. Genellikle **soket aktivasyonu** için kullanılır; yani ilgili sokete bir bağlantı geldiğinde, systemd ilişkili `.service` birimini otomatik olarak başlatır. Bu, servisin sürekli çalışmasına gerek kalmadan kaynak tasarrufu sağlar. (Örn: `sshd.socket`)
*   **`.target`:** Diğer unit'leri gruplayan bir senkronizasyon noktasıdır. Belirli bir sistem durumunu veya hedefini temsil eder. SysVinit'teki runlevel'lara benzer bir işlev görür, ancak daha esnektir ve bağımlılıklara dayanır. (Örn: `multi-user.target`, `graphical.target`, `network.target`, `sysinit.target`)
*   **`.mount`:** Bir dosya sistemi bağlama noktasını (`/etc/fstab`'daki bir satıra benzer şekilde) tanımlar ve yönetir. systemd, `/etc/fstab` girdilerini de otomatik olarak `.mount` birimlerine dönüştürebilir. (Örn: `home.mount`)
*   **`.automount`:** Bir dosya sistemi bağlama noktası için otomatik bağlama (automount) işlevselliği sağlar. İlgili dizine erişildiğinde, systemd ilişkili `.mount` birimini otomatik olarak etkinleştirir. Ağ paylaşımları veya isteğe bağlı bağlanan aygıtlar için kullanışlıdır. (Örn: `media-usb.automount`)
*   **`.timer`:** Belirli zamanlarda veya periyodik olarak başka bir unit'i (genellikle bir `.service` unit'ini) etkinleştirmek için kullanılır. `cron` işlerine modern bir alternatiftir. (Örn: `backup.timer`)
*   **`.path`:** Belirli bir dosya veya dizindeki değişiklikleri izler ve bir değişiklik olduğunda (örn. dosya oluşturma, değiştirme) başka bir unit'i etkinleştirir. (Örn: `logwatch.path`)
*   **`.slice`:** Kaynak yönetimi için kullanılan bir gruplama mekanizmasıdır. Linux Kontrol Grupları (cgroups) ile ilişkilidir ve bir grup unit'in toplam kaynak kullanımını (CPU, bellek, I/O vb.) sınırlamak veya yönetmek için kullanılır. (Örn: `user.slice`, `system.slice`)
*   **`.scope`:** `slice`'a benzer, ancak systemd tarafından başlatılmayan, dışarıdan oluşturulan süreçleri (örn. kullanıcı oturumları, `sshd` tarafından başlatılan süreçler) gruplamak için kullanılır. Kaynak yönetimi ve izleme amacıyla kullanılır.
*   **`.device`:** Çekirdek tarafından algılanan ve `udev` tarafından systemd'ye bildirilen donanım aygıtlarını temsil eder. Genellikle otomatik olarak oluşturulur ve bağımlılıklar için kullanılır.

## Birimleri `systemctl` ile Yönetme

`systemctl` komutu sadece servisleri değil, tüm unit tiplerini yönetmek için kullanılır.

*   **Unit'leri Listeleme:**
    ```bash
    # Aktif olan tüm unit'leri listele
    systemctl list-units

    # Belirli bir tipteki unit'leri listele (örn. servisler)
    systemctl list-units --type=service

    # Tüm unit'leri (aktif olmayanlar dahil) listele
    systemctl list-units --all 
    ```
*   **Unit Dosyalarını Listeleme:**
    Sistemdeki tüm unit dosyalarını ve durumlarını (enabled, disabled, static, masked) listeler.
    ```bash
    systemctl list-unit-files
    ```
*   **Unit Dosyasının İçeriğini Görme:**
    ```bash
    systemctl cat sshd.service
    ```
*   **Unit'in Detaylı Özelliklerini Görme:**
    ```bash
    systemctl show sshd.service
    ```
*   **Bağımlılıkları Görme:**
    ```bash
    # Bir unit'in hangi unit'lere bağımlı olduğunu göster
    systemctl list-dependencies sshd.service

    # Bir unit'in hangi unit'ler tarafından istendiğini göster (ters bağımlılık)
    systemctl list-dependencies --reverse sshd.service 
    ```

Unit dosyaları ve tipleri, systemd'nin esnekliğinin ve gücünün temelini oluşturur. Bir servisin veya sistem davranışının nasıl çalıştığını anlamak için ilgili unit dosyalarını incelemek önemlidir.
