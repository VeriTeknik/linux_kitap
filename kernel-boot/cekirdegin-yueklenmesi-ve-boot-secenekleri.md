# Çekirdeğin Yüklenmesi ve Boot Seçenekleri

Önyükleyici (bootloader), firmware'den kontrolü aldıktan sonraki ana görevi, işletim sistemi çekirdeğini (kernel) ve başlangıç için gerekli olan başlangıç RAM dosya sistemini (initramfs) belleğe yüklemektir.

## Çekirdek İmajı (Kernel Image)

*   **Konum:** Linux çekirdeği, genellikle `/boot` dizini altında sıkıştırılmış bir dosya olarak bulunur. Dosya adı dağıtıma ve sürüme göre değişir, ancak yaygın isimler `vmlinuz-<sürüm>` veya `bzImage-<sürüm>` şeklindedir (örn. `/boot/vmlinuz-6.1.0-18-amd64`).
*   **İşlevi:** Çekirdek, işletim sisteminin kalbidir. Donanımı yönetir, işlemleri zamanlar, belleği yönetir ve sistem çağrıları aracılığıyla kullanıcı alanı (user space) programlarına hizmet verir. Önyükleyici tarafından belleğe yüklenir ve çalıştırılır.

## Başlangıç RAM Dosya Sistemi (initrd / initramfs)

*   **Konum:** Çekirdek imajı gibi, genellikle `/boot` dizini altında bulunur. Dosya adı `initrd.img-<sürüm>` veya `initramfs-<sürüm>.img` gibi olabilir (örn. `/boot/initramfs-6.1.0-18-amd64.img`).
*   **İşlevi:** Çekirdek başlatıldıktan hemen sonra, ancak gerçek kök dosya sistemi (`/`) bağlanmadan önce, bellekte (RAM) geçici bir kök dosya sistemi olarak kullanılır. Temel amacı, gerçek kök dosya sistemini bağlamak için gerekli olan modülleri ve araçları içermektir. Bunlar şunları içerebilir:
    *   Disk denetleyici sürücüleri (SATA, NVMe, SCSI vb.).
    *   Dosya sistemi sürücüleri (ext4, xfs, btrfs vb.).
    *   Mantıksal Birim Yönetimi (LVM) veya yazılımsal RAID araçları ve modülleri.
    *   Şifreli diskleri açmak için gerekli araçlar (cryptsetup).
    *   Ağ sürücüleri (ağ üzerinden boot yapılıyorsa).
*   **Oluşturulması:** Initramfs, genellikle çekirdek kurulduğunda veya güncellendiğinde otomatik olarak sisteme özgü olarak oluşturulur. Bu işlemi yapan araçlar dağıtıma göre değişir:
    *   `mkinitrd` (Eski RHEL/CentOS)
    *   `dracut` (Modern RHEL/CentOS/Fedora)
    *   `mkinitcpio` (Arch Linux)
    *   `update-initramfs` (Debian/Ubuntu)
*   **initrd vs initramfs:** `initrd` (initial RAM disk) daha eski bir yöntemdi ve sıkıştırılmış bir dosya sistemi imajı (örn. ext2) olarak yüklenirdi. `initramfs` (initial RAM filesystem) ise daha modern bir yöntemdir ve sıkıştırılmış bir `cpio` arşivi olarak yüklenir. Daha esnektir ve genellikle daha az bellek kullanır. Günümüzdeki çoğu sistem `initramfs` kullanır.

## Önyükleyici Yapılandırması ve Menüsü

Önyükleyici (örn. GRUB, systemd-boot), hangi çekirdek ve initramfs dosyasını yükleyeceğini, hangi çekirdek parametrelerini geçeceğini yapılandırma dosyasından okur.

*   **GRUB 2:** Ana yapılandırma `/boot/grub/grub.cfg` dosyasındadır. Bu dosya genellikle `update-grub` veya `grub-mkconfig -o /boot/grub/grub.cfg` komutları tarafından `/etc/default/grub` ve `/etc/grub.d/` altındaki betikler kullanılarak otomatik olarak oluşturulur.
*   **systemd-boot:** Yapılandırma EFI Sistem Bölümü (ESP) içindedir. `/boot/loader/loader.conf` genel ayarları, `/boot/loader/entries/` altındaki `.conf` dosyaları ise her bir önyükleme girdisini (çekirdek, initramfs, parametreler) tanımlar.

Sistem açılırken, önyükleyici genellikle bir menü gösterir. Bu menü şunları sağlar:
*   Farklı işletim sistemleri veya çekirdek sürümleri arasında seçim yapma.
*   Belirli bir önyükleme girdisi için çekirdek parametrelerini geçici olarak düzenleme (örn. tek kullanıcı moduna geçmek için `single` veya `init=/bin/bash` eklemek).
*   Belirli bir süre (timeout) sonunda varsayılan girdiyi otomatik olarak başlatma.

## Yükleme Süreci

1.  Önyükleyici, yapılandırmasına göre seçilen (veya varsayılan) çekirdek imajını ve ilgili initramfs dosyasını diskten okuyarak belleğe (RAM) yükler.
2.  Önyükleyici, yapılandırmada tanımlanan veya menüden eklenen çekirdek parametrelerini hazırlar.
3.  Önyükleyici, kontrolü belleğe yüklediği çekirdeğin başlangıç noktasına devreder ve çekirdek parametrelerini iletir.
4.  Çekirdek çalışmaya başlar, initramfs'i bağlar ve init sürecini başlatmak için gerekli adımları atar.
