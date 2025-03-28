# Çekirdek Parametreleri (Kernel Parameters)

Çekirdek parametreleri (kernel command-line parameters), önyükleyici (bootloader) tarafından Linux çekirdeğine, açılış sırasında davranışını değiştirmek veya donanım hakkında bilgi vermek amacıyla iletilen metin tabanlı seçeneklerdir. Bu parametreler, çekirdeğin donanımı nasıl algılayacağından, kök dosya sisteminin nerede bulunacağına ve sistemin hangi başlangıç modunda açılacağına kadar birçok şeyi etkileyebilir.

## Parametreler Nasıl İletilir?

Çekirdek parametreleri, önyükleyici (GRUB, systemd-boot vb.) tarafından çekirdek belleğe yüklendikten hemen sonra, çekirdek çalıştırılmadan önce ona iletilir.

*   **Geçici Olarak Düzenleme:** Çoğu önyükleyici, açılış sırasında bir menü sunar. Bu menüde genellikle belirli bir önyükleme girdisini düzenleme seçeneği bulunur (GRUB'da genellikle 'e' tuşu). Bu sayede, sadece o anki açılış için geçerli olacak şekilde parametreleri ekleyebilir, silebilir veya değiştirebilirsiniz. Bu, sorun giderme veya test amaçlı çok kullanışlıdır.
*   **Kalıcı Olarak Düzenleme:** Parametreleri her açılışta geçerli olacak şekilde ayarlamak için önyükleyicinin yapılandırma dosyasını düzenlemek gerekir:
    *   **GRUB 2:** `/etc/default/grub` dosyasındaki `GRUB_CMDLINE_LINUX` veya `GRUB_CMDLINE_LINUX_DEFAULT` satırları düzenlenir. Değişiklik sonrası, yeni yapılandırmanın ana GRUB dosyasına (`/boot/grub/grub.cfg`) yazılması için `sudo update-grub` (Debian/Ubuntu) veya `sudo grub-mkconfig -o /boot/grub/grub.cfg` (Fedora/RHEL/Arch vb.) komutu çalıştırılmalıdır.
    *   **systemd-boot:** EFI Sistem Bölümü (ESP) içindeki `/boot/loader/entries/` dizinindeki ilgili `.conf` dosyasındaki `options` satırı düzenlenir.

## Mevcut Parametreleri Görme

Sistem çalışırken, o anki açılışta çekirdeğe hangi parametrelerin iletildiğini görmek için `/proc/cmdline` dosyası okunabilir:
```bash
cat /proc/cmdline
```
Örnek Çıktı:
```
BOOT_IMAGE=/boot/vmlinuz-6.1.0-18-amd64 root=UUID=xxxx-xxxx ro quiet splash
```

## Yaygın Çekirdek Parametreleri

Aşağıda sıkça kullanılan bazı çekirdek parametreleri ve açıklamaları verilmiştir:

*   **`root=`**: Kök dosya sisteminin (`/`) bulunduğu aygıtı belirtir. Farklı formatlarda belirtilebilir:
    *   Aygıt adı: `root=/dev/sda2` (Disk sırası değişebileceği için daha az güvenilir)
    *   UUID: `root=UUID=abcdef12-3456-7890-abcd-ef1234567890` (En güvenilir yöntem)
    *   LABEL: `root=LABEL=ArchRoot` (Bölüme etiket verilmişse)
*   **`ro`**: Kök dosya sistemini başlangıçta **salt okunur (read-only)** olarak bağlar. Başlatma sürecinin ilerleyen adımlarında genellikle `rw` (read-write) olarak yeniden bağlanır. Dosya sistemi kontrolü (fsck) için önemlidir.
*   **`rw`**: Kök dosya sistemini başlangıçta **okunur-yazılır (read-write)** olarak bağlar. Genellikle sorun giderme dışında önerilmez.
*   **`init=`**: `/sbin/init` yerine çalıştırılacak ilk kullanıcı alanı programını belirtir. Örneğin, `init=/bin/bash` komutu, sistem servislerini başlatmadan doğrudan bir root kabuğu açar (kurtarma modu için kullanılır).
*   **`quiet`**: Açılış sırasında çekirdek tarafından ekrana yazdırılan mesajların çoğunu gizler. Daha "sessiz" bir açılış sağlar.
*   **`splash`**: Grafiksel bir açılış ekranını (splash screen) etkinleştirir (eğer kurulu ve yapılandırılmışsa, örn. Plymouth). Genellikle `quiet` ile birlikte kullanılır.
*   **`nomodeset`**: Çekirdeğin grafik kartı için mod ayarlamasını (Kernel Mode Setting - KMS) devre dışı bırakır. Bazı grafik kartı sürücüsü sorunlarında veya uyumsuzluklarında geçici bir çözüm olarak kullanılabilir, ancak genellikle grafik performansını düşürür.
*   **`single` veya `1` veya `s`**: Sistemi tek kullanıcı (single-user) modunda başlatır. Genellikle kurtarma veya bakım işlemleri için kullanılır. systemd sistemlerde `systemd.unit=rescue.target` ile benzer bir işlev görülebilir.
*   **`systemd.unit=`**: systemd init sistemi için varsayılan hedef (target) yerine belirli bir hedefi başlatmasını söyler. Kurtarma için kullanışlıdır:
    *   `systemd.unit=rescue.target`: Tek kullanıcı moduna benzer, temel sistem ve kök dosya sistemi bağlanır.
    *   `systemd.unit=emergency.target`: Daha da temel bir mod, sadece kök dosya sistemi salt okunur bağlanır ve acil durum kabuğu açılır.
*   **`mem=`**: Çekirdeğin kullanabileceği maksimum RAM miktarını sınırlar (örn. `mem=4G`). Donanım testi veya uyumluluk için kullanılabilir.
*   **`console=`**: Çekirdek mesajlarının gönderileceği konsol aygıtını ve seçeneklerini belirtir (örn. seri konsol için `console=ttyS0,115200n8`).

## Parametre Referansı

Çekirdek tarafından kabul edilen tüm parametrelerin tam listesi ve açıklamaları oldukça uzundur. Genellikle çekirdek kaynak kodu ile birlikte gelen `Documentation/admin-guide/kernel-parameters.txt` dosyasında veya [Kernel.org web sitesinde](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html) bulunabilir. Belirli bir donanım veya alt sistem için parametreler, ilgili belgelerde de yer alabilir.
