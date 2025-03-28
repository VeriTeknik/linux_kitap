# Bootloader (Önyükleyici) Nedir?

**Bootloader** (Önyükleyici), bilgisayarın donanım başlatma sürecini tamamlayan firmware (BIOS veya UEFI) ile işletim sistemi çekirdeği (kernel) arasında köprü görevi gören kritik bir yazılımdır. Firmware, donanımı test edip başlattıktan sonra, kontrolü önyükleyiciye devreder. Önyükleyicinin temel görevi, seçilen işletim sistemi çekirdeğini ve başlangıç için gerekli diğer dosyaları (genellikle initramfs) belleğe yüklemek ve ardından kontrolü çekirdeğe vermektir.

## Önyükleyicinin Görevleri

1.  **İşletim Sistemi Seçimi:** Eğer sistemde birden fazla işletim sistemi veya aynı işletim sisteminin farklı çekirdek sürümleri kuruluysa, önyükleyici genellikle kullanıcıya bir menü sunarak hangisinin başlatılacağını seçme imkanı tanır.
2.  **Çekirdek ve Initramfs Yükleme:** Seçilen işletim sistemine ait çekirdek imajını (`vmlinuz-...` gibi) ve başlangıç RAM dosya sistemini (`initramfs-...` veya `initrd-...`) depolama aygıtından (genellikle `/boot` dizininden) okuyarak belleğe (RAM) yükler.
3.  **Çekirdek Parametrelerini Geçirme:** Çekirdeğin başlangıç davranışını etkileyen parametreleri (örneğin, kök dosya sisteminin konumu `root=`, salt okunur başlama `ro`, grafik mod ayarları, donanım seçenekleri vb.) çekirdeğe iletir. Bu parametreler genellikle önyükleyici menüsünden geçici olarak düzenlenebilir veya yapılandırma dosyasında kalıcı olarak ayarlanabilir.
4.  **Kontrolü Çekirdeğe Devretme:** Gerekli dosyalar belleğe yüklendikten ve parametreler hazırlandıktan sonra, önyükleyici işlemciyi çekirdeğin başlangıç noktasına yönlendirerek kontrolü tamamen işletim sistemi çekirdeğine bırakır.

## Yaygın Önyükleyiciler

*   **GRUB (GRand Unified Bootloader):** Linux dünyasındaki en yaygın önyükleyicidir. Özellikle **GRUB 2**, günümüzdeki çoğu dağıtım tarafından kullanılır. Hem BIOS hem de UEFI sistemleri destekler, esnek bir yapılandırmaya sahiptir ve birçok dosya sistemini okuyabilir. Yapılandırması genellikle `/boot/grub/grub.cfg` dosyasında bulunur, ancak bu dosya genellikle doğrudan düzenlenmez; bunun yerine `/etc/default/grub` dosyası ve `/etc/grub.d/` dizinindeki betikler düzenlenip `update-grub` veya `grub-mkconfig` komutları çalıştırılır.
*   **systemd-boot (eski adıyla gummiboot):** Sadece **UEFI** sistemler için tasarlanmış, GRUB'a göre daha basit bir önyükleyicidir. Yapılandırması EFI Sistem Bölümü (ESP) içindeki metin dosyalarıyla (`/boot/loader/loader.conf`, `/boot/loader/entries/*.conf`) yapılır. Kurulumu ve yönetimi genellikle daha basittir.
*   **LILO (LInux LOader):** Eski Linux sistemlerinde yaygın olan, ancak günümüzde büyük ölçüde GRUB tarafından yerini almış, daha basit bir önyükleyicidir. Genellikle MBR üzerine kurulur ve yapılandırması `/etc/lilo.conf` dosyasındadır.
*   **SYSLINUX/EXTLINUX:** Özellikle CD/DVD veya USB belleklerden önyükleme yapmak için kullanılan bir önyükleyici ailesidir. `EXTLINUX`, ext2/3/4 veya btrfs gibi dosya sistemlerinden önyükleme yapmak için kullanılır.

Önyükleyici, sistemin doğru bir şekilde başlayabilmesi için hayati bir bileşendir. Yapılandırmasında yapılan hatalar sistemin açılmamasına neden olabilir.
