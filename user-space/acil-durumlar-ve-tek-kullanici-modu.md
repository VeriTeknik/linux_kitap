# Acil Durumlar ve Tek Kullanıcı Modu

Bazen bir Linux sistemini normal şekilde başlatmak mümkün olmayabilir veya sistem üzerinde bakım yapmak için normal çalışma moduna girmeden müdahale etmek gerekebilir. Bu gibi durumlar için çeşitli kurtarma modları mevcuttur. En yaygın olanı **tek kullanıcı modu (single-user mode)**'dur.

## Neden Kurtarma Moduna İhtiyaç Duyulur?

*   Root şifresini unutmak ve sıfırlamak.
*   Bozulmuş bir dosya sistemini kontrol etmek ve onarmak (`fsck`).
*   Sistemin açılmasını engelleyen hatalı bir yapılandırma dosyasını düzeltmek (örn. `/etc/fstab`, `/etc/ssh/sshd_config`).
*   Normal açılışı engelleyen bir servisi devre dışı bırakmak.
*   Donanım veya sürücü sorunlarını teşhis etmek.

## Kurtarma Moduna Nasıl Girilir?

Kurtarma moduna girmek için genellikle önyükleyici (bootloader) menüsüne müdahale etmek ve çekirdek parametrelerini geçici olarak düzenlemek gerekir.

1.  **Önyükleyici Menüsünü Açma:** Sistemi başlatırken önyükleyici menüsünü (genellikle GRUB) görüntülemek için doğru tuşa basın. Bu tuş genellikle BIOS/UEFI ekranından hemen sonra `Shift` (özellikle Ubuntu'da) veya `ESC` olabilir. Bazen menü varsayılan olarak görünür.
2.  **Önyükleme Girdisini Düzenleme:** Menüde, normalde başlattığınız Linux girdisini seçin (üzerine gelin) ve düzenleme moduna girmek için belirtilen tuşa basın (GRUB'da genellikle `e` tuşudur).
3.  **Çekirdek Parametresini Ekleme/Değiştirme:** Ekranda önyükleme komutları görünecektir. `linux` veya `linuxefi` ile başlayan satırı bulun. Bu satırın sonuna (genellikle `ro quiet splash` gibi parametrelerden sonra) bir boşluk bırakarak aşağıdaki parametrelerden uygun olanı ekleyin:

    *   **SysVinit Sistemler İçin:**
        *   `single` veya `1`: Sistemi runlevel 1'e (tek kullanıcı modu) başlatır.
    *   **systemd Sistemler İçin (Önerilenler):**
        *   `systemd.unit=rescue.target`: Tek kullanıcı moduna benzer. Temel sistemi başlatır, dosya sistemlerini (genellikle okunur-yazılır) bağlar ve bir kurtarma kabuğu (root shell) sunar. Ağ genellikle aktif olmaz.
        *   `systemd.unit=emergency.target`: Daha da minimal bir moddur. Sadece kök dosya sistemini salt okunur (read-only) olarak bağlar ve bir acil durum kabuğu sunar. Dosya sistemi onarımı gibi işlemler için kullanışlıdır.
    *   **Doğrudan Kabuk (En Temel):**
        *   `init=/bin/bash` (veya `/bin/sh`): Init sistemini tamamen atlar ve doğrudan bir root kabuğu başlatır. Kök dosya sistemi genellikle salt okunur olarak bağlanır ve diğer dosya sistemleri bağlanmaz. Çok temel kurtarma işlemleri için kullanılır.

4.  **Sistemi Başlatma:** Parametreyi ekledikten sonra, sistemi bu parametrelerle başlatmak için belirtilen tuş kombinasyonuna basın (GRUB'da genellikle `Ctrl+X` veya `F10`).

## Kurtarma Modunda Çalışma

*   **Root Kabuğu:** Genellikle doğrudan bir root kabuğu (#) açılır veya root şifresi istenir.
*   **Dosya Sistemi Erişimi:**
    *   `rescue.target` modunda dosya sistemleri genellikle okunur-yazılır (`rw`) olarak bağlanır.
    *   `emergency.target` veya `init=/bin/bash` modlarında kök dosya sistemi genellikle salt okunur (`ro`) olarak bağlanır. Dosyalarda değişiklik yapmak için önce okunur-yazılır olarak yeniden bağlamanız gerekir:
        ```bash
        mount -o remount,rw /
        ```
    *   Diğer dosya sistemlerini (`/home`, `/var` vb.) manuel olarak bağlamanız gerekebilir:
        ```bash
        mount /home
        mount -a # /etc/fstab içindeki diğerlerini bağlamayı dener
        ```
*   **Ağ:** `rescue.target` ve `emergency.target` modlarında ağ genellikle aktif değildir. Gerekirse manuel olarak yapılandırıp başlatmanız gerekebilir.
*   **Yapılacak İşlemler:** Gerekli bakım veya onarım işlemlerini yapın (şifre sıfırlama, `fsck`, yapılandırma dosyası düzenleme vb.).

## Kurtarma Modundan Çıkma

İşlemlerinizi tamamladıktan sonra:

*   Eğer `init=/bin/bash` kullandıysanız, sistemi güvenli bir şekilde yeniden başlatmak zordur. Genellikle `sync` komutunu çalıştırıp ardından `reboot -f` (zorla yeniden başlatma) veya makineyi fiziksel olarak resetlemek gerekebilir.
*   `rescue.target` veya `emergency.target` modlarında (veya SysVinit runlevel 1'de), genellikle `exit` yazarak veya `Ctrl+D` tuşlarına basarak normal başlatma sürecine devam edebilir veya `reboot` ya da `systemctl reboot` komutuyla sistemi yeniden başlatabilirsiniz.

Tek kullanıcı modu ve diğer kurtarma modları, sistem yöneticileri için önemli sorun giderme araçlarıdır.
