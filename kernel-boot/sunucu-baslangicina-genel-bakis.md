# Sunucu Başlangıcına Genel Bakış

Bir Linux sunucusunun (veya herhangi bir modern bilgisayarın) açılış (boot) süreci, güç düğmesine basıldığı andan itibaren kullanıma hazır hale gelene kadar bir dizi adımdan oluşur. Bu adımlar genel olarak aşağıdaki sırayı takip eder:

1.  **Güç Verilmesi ve Donanım Başlatma:**
    *   Güç düğmesine basıldığında, güç kaynağı (PSU) sisteme enerji sağlar.
    *   Anakart üzerindeki temel donanımlar (CPU, RAM vb.) başlatılır.

2.  **Firmware (BIOS veya UEFI):**
    *   İşlemci, anakart üzerinde bulunan firmware yazılımını çalıştırmaya başlar. Bu yazılım ya eski **BIOS** (Basic Input/Output System) ya da modern **UEFI** (Unified Extensible Firmware Interface) olabilir.
    *   **POST (Power-On Self-Test):** Firmware, temel donanımların (RAM, CPU, klavye vb.) çalışıp çalışmadığını kontrol eden bir dizi test gerçekleştirir.
    *   **Donanım Algılama ve Başlatma:** Depolama aygıtları (diskler), ağ kartları gibi diğer donanımlar algılanır ve temel düzeyde başlatılır.
    *   **Önyükleyici (Bootloader) Arama ve Yükleme:** Firmware, yapılandırılmış önyükleme sırasına göre (boot order) önyüklenebilir bir aygıt arar.
        *   **BIOS/MBR:** Geleneksel BIOS sistemleri, genellikle diskin ilk sektöründe bulunan **MBR**'ı (Master Boot Record) okur. MBR, küçük bir önyükleyici kodu (ilk aşama önyükleyici) ve disk bölüm tablosunu içerir. Bu ilk aşama kod, genellikle daha büyük olan ikinci aşama önyükleyiciyi (örn. GRUB'ın bir kısmını) diskteki başka bir yerden yükler.
        *   **UEFI/GPT:** Modern UEFI sistemleri, genellikle **GPT** (GUID Partition Table) ile bölümlenmiş diskleri kullanır. UEFI, disk üzerinde özel olarak biçimlendirilmiş (FAT32) bir **EFI Sistem Bölümü** (EFI System Partition - ESP) arar. Bu bölümde bulunan önyükleyici uygulamalarını (`.efi` dosyaları, örn. GRUB veya systemd-boot için) doğrudan çalıştırır. UEFI, MBR'a göre daha esnek ve güvenli bir önyükleme süreci sunar.

3.  **Önyükleyici (Bootloader - Örn. GRUB, systemd-boot):**
    *   Firmware tarafından yüklenen önyükleyici (genellikle GRUB - GRand Unified Bootloader veya systemd-boot) kontrolü devralır.
    *   **Çekirdek (Kernel) ve Initramfs'i Yükleme:** Önyükleyici, yapılandırma dosyasına göre (örn. `/boot/grub/grub.cfg`) seçilen Linux çekirdek imajını (`vmlinuz-...`) ve başlangıç RAM dosya sistemini (**initramfs** veya eski sistemlerde **initrd**) belleğe yükler.
    *   **Önyükleme Menüsü:** Genellikle kullanıcıya farklı çekirdek sürümlerini veya işletim sistemlerini seçme, çekirdek parametrelerini düzenleme imkanı sunan bir menü gösterir.
    *   **Kontrolü Çekirdeğe Devretme:** Seçilen çekirdeği bellekte çalıştırır ve ona gerekli bilgileri (çekirdek parametreleri, initramfs'in konumu vb.) iletir.

4.  **Çekirdek (Kernel) Başlatma:**
    *   Çekirdek kontrolü devralır ve kendini bellekte açar (decompress).
    *   **Donanım Başlatma:** CPU, bellek yönetimi gibi temel sistemleri kurar ve algılanan donanımlar için gerekli sürücüleri (genellikle initramfs içindekileri kullanarak) yüklemeye başlar.
    *   **Initramfs:** Belleğe yüklenen initramfs (initial RAM filesystem), kök dosya sisteminin bağlanabilmesi için gerekli sürücüleri (disk, dosya sistemi, LVM, RAID vb.) içeren geçici bir kök dosya sistemidir. Çekirdek, initramfs içindeki bir başlangıç betiğini çalıştırır.
    *   **Kök Dosya Sistemini Bağlama (Mount):** Initramfs içindeki betikler, gerçek kök dosya sistemini (`/`) bulur ve genellikle salt okunur (read-only) olarak bağlar.
    *   **Init Sürecini Başlatma:** Çekirdek, kök dosya sistemi üzerindeki `/sbin/init` programını (veya çekirdek parametresi ile belirtilmişse farklı bir programı) **PID 1** olarak çalıştırır ve kontrolü ona devreder.

5.  **Init Süreci (PID 1 - Örn. systemd, SysV init):**
    *   `init` (veya modern sistemlerde genellikle `systemd`), kullanıcı alanındaki (user space) ilk işlemdir ve diğer tüm sistem işlemlerinin atasıdır.
    *   **Sistemi Başlatma:** Yapılandırma dosyalarına göre (örn. systemd için unit dosyaları, SysV init için runlevel betikleri) gerekli servisleri (ağ, depolama, günlükleme, kullanıcı girişi vb.) başlatır.
    *   **Hedef Duruma Ulaşma:** Sistemi, tanımlanan hedef duruma (target veya runlevel) getirir (örn. çok kullanıcılı komut satırı veya grafiksel arayüz).

Bu adımların sonunda sistem kullanıma hazır hale gelir ve kullanıcı giriş yapabilir. Her adım, bir sonrakinin çalışması için gerekli ortamı hazırlar.
