# Arch Linux

Arch Linux, hafif, esnek ve basit ("Keep It Simple, Stupid" - KISS) olmayı hedefleyen, bağımsız olarak geliştirilen bir Linux dağıtımıdır. Sürekli güncellenen (rolling release) bir model kullanır, yani belirli sürüm yükseltmeleri yerine sistem sürekli olarak en son paketlerle güncellenir. Arch Linux, kullanıcısına sistem üzerinde tam kontrol vermeyi amaçlar ve bu nedenle kurulum süreci diğer birçok dağıtıma göre daha manuel ve komut satırı odaklıdır.

**ÖNEMLİ UYARI:** Arch Linux kurulum süreci detaylıdır ve zamanla değişiklik gösterebilir. Bu bölümdeki adımlar genel bir rehber niteliğindedir. **Her zaman en güncel ve doğru bilgiler için resmi Arch Wiki Kurulum Kılavuzu'na başvurmanız şiddetle tavsiye edilir:** [https://wiki.archlinux.org/title/Installation_guide](https://wiki.archlinux.org/title/Installation_guide)

## Başlangıç ve Kurulum Ortamı

Arch Linux kurulumu, güncel bir kurulum medyasından (USB bellek veya CD/DVD) sistemi başlatarak başlar. Kurulum medyası boot edildiğinde, genellikle doğrudan bir komut satırı (root shell) açılır. Kurulumun büyük bölümü bu komut satırı üzerinden gerçekleştirilir.

## İnternet Bağlantısı Kurulumu

Kurulum için aktif bir internet bağlantısı gereklidir (paketleri indirmek için).

1.  **Bağlantıyı Kontrol Et:** Kurulum ortamı genellikle kablolu bağlantılar için `dhcpcd` servisini otomatik başlatır. Bağlantıyı test edin:
    ```bash
    ping archlinux.org -c 3 
    ```
2.  **Wi-Fi Bağlantısı:**
    *   Arayüz adını bulun: `iwctl device list`
    *   `iwctl` interaktif aracını kullanarak ağları tarayın, bağlanın ve şifreyi girin:
        ```bash
        iwctl
        # iwctl içinde:
        # station <cihaz_adı> scan
        # station <cihaz_adı> get-networks
        # station <cihaz_adı> connect <SSID> 
        # ... (şifre istenir) ...
        # exit 
        ```
    *   Alternatif olarak `wifi-menu` (eğer mevcutsa) veya NetworkManager (`nmtui`) gibi araçlar da kullanılabilir.
3.  **Statik IP (Gerekirse):** Kablolu veya kablosuz bağlantı için statik IP gerekiyorsa, `ip` komutları ile geçici olarak ayarlayabilir veya `systemd-networkd`, NetworkManager gibi araçların yapılandırma dosyalarını düzenleyebilirsiniz (kurulum ortamında bu genellikle gerekli olmaz, asıl sistem kurulduktan sonra yapılır).

İnternet bağlantısı sağlandıktan sonra sistem saatini NTP ile senkronize etmek iyi bir fikirdir:
```bash
timedatectl set-ntp true
```

## Diskleri Hazırlama

1.  **Diskleri Listele:** Mevcut diskleri ve bölümleri görmek için:
    ```bash
    lsblk
    # veya fdisk -l
    ```
2.  **Bölümlendirme:** Diski bölümlendirmek için `fdisk`, `cfdisk` (metin arayüzü) veya `parted` gibi araçları kullanın.
    *   **BIOS/MBR Sistemler:** Genellikle bir kök (`/`) bölümü ve bir swap bölümü yeterlidir.
    *   **UEFI/GPT Sistemler:**
        *   **EFI Sistem Bölümü (ESP):** FAT32 olarak biçimlendirilmiş, genellikle 200-512MB boyutunda, `/boot` veya `/efi` altına bağlanacak bir bölüm gereklidir.
        *   **Kök Bölümü (`/`):** Sistemin kurulacağı ana bölüm (örn. ext4, xfs, btrfs).
        *   **Swap:** Ayrı bir bölüm veya swap dosyası olarak oluşturulabilir.
        *   **(İsteğe Bağlı) Diğer Bölümler:** `/home` gibi ayrı bölümler oluşturulabilir.
    ```bash
    # Örnek: cfdisk ile /dev/sda üzerinde bölümlendirme
    cfdisk /dev/sda 
    ```
    `cfdisk` içinde yeni bölümler oluşturun, boyutlarını ayarlayın, türlerini belirleyin (Linux filesystem, Linux swap, EFI System) ve değişiklikleri yazın (`[Write]`).
3.  **Dosya Sistemlerini Oluşturma:** Oluşturulan bölümleri biçimlendirin:
    ```bash
    # Kök bölümü için (örnek: ext4)
    mkfs.ext4 /dev/sda2 

    # EFI Sistem Bölümü için (FAT32)
    mkfs.fat -F32 /dev/sda1 

    # Swap bölümü için (varsa)
    mkswap /dev/sda3 
    ```
4.  **Bölümleri Bağlama (Mount):** Dosya sistemlerini kurulacak sistemin hiyerarşisine göre bağlayın. Kök bölümü `/mnt` altına bağlanmalıdır.
    ```bash
    # Kök bölümünü bağla
    mount /dev/sda2 /mnt

    # Diğer bölümleri oluştur ve bağla (örnek: /home)
    # mkdir /mnt/home
    # mount /dev/sdaX /mnt/home 

    # EFI Sistem Bölümünü bağla (UEFI için)
    mkdir -p /mnt/boot # veya /mnt/efi
    mount /dev/sda1 /mnt/boot # veya /mnt/efi

    # Swap'ı etkinleştir (varsa)
    swapon /dev/sda3 
    ```

## Temel Sistemi Kurma (`pacstrap`)

`pacstrap` betiği, belirtilen bağlama noktasına (`/mnt`) temel Arch Linux paketlerini ve isteğe bağlı diğer paketleri indirip kurar.
```bash
# Temel sistemi, Linux çekirdeğini ve yaygın firmware'leri kur
pacstrap /mnt base linux linux-firmware

# İsteğe bağlı: Metin düzenleyici, ağ yönetim aracı gibi ek paketler
pacstrap /mnt vim networkmanager 
```
`base-devel` grubu, AUR paketlerini derlemek için genellikle gereklidir ve bu aşamada veya daha sonra kurulabilir.

## Sistemi Yapılandırma

1.  **fstab Oluşturma:** Bağlı dosya sistemleri için `/etc/fstab` dosyasını oluşturun. UUID kullanmak genellikle daha güvenilirdir.
    ```bash
    genfstab -U /mnt >> /mnt/etc/fstab
    ```
    Oluşturulan `/mnt/etc/fstab` dosyasını kontrol edin (`cat /mnt/etc/fstab`).
2.  **chroot:** Kurulan sisteme `chroot` ile geçiş yapın:
    ```bash
    arch-chroot /mnt
    ```
    Bundan sonraki komutlar yeni kurulan sistem içinde çalışacaktır.
3.  **Zaman Dilimi (Timezone):**
    ```bash
    # Örnek: İstanbul için
    ln -sf /usr/share/zoneinfo/Europe/Istanbul /etc/localtime
    # Donanım saatini UTC'ye göre ayarla (önerilen)
    hwclock --systohc --utc 
    ```
4.  **Yerelleştirme (Localization):**
    *   `/etc/locale.gen` dosyasını düzenleyerek istediğiniz yerel ayarların (örn. `en_US.UTF-8 UTF-8`, `tr_TR.UTF-8 UTF-8`) başındaki `#` işaretini kaldırın.
    *   Yerel ayarları oluşturun:
        ```bash
        locale-gen
        ```
    *   Sistem dilini ayarlayın (`/etc/locale.conf`):
        ```bash
        echo LANG=en_US.UTF-8 > /etc/locale.conf
        # veya Türkçe için: echo LANG=tr_TR.UTF-8 > /etc/locale.conf
        ```
    *   (İsteğe bağlı) Klavye düzenini ayarlayın (`/etc/vconsole.conf`):
        ```bash
        # Örnek: Türkçe F klavye için
        # echo KEYMAP=trf > /etc/vconsole.conf 
        ```
5.  **Ağ Yapılandırması:**
    *   **Hostname:** Makine adını ayarlayın (`/etc/hostname`):
        ```bash
        echo myarchlinux > /etc/hostname
        ```
    *   **(İsteğe bağlı) Hosts dosyası:** `/etc/hosts` dosyasını düzenleyerek localhost ve makine adını ekleyin:
        ```
        127.0.0.1   localhost
        ::1         localhost
        127.0.1.1   myarchlinux.localdomain myarchlinux 
        ```
    *   **Ağ Yönetim Servisini Etkinleştirme:** Kurulum sırasında `pacstrap` ile kurduğunuz ağ yönetim aracının servisini etkinleştirin (örn. NetworkManager):
        ```bash
        systemctl enable NetworkManager 
        # veya dhcpcd için: systemctl enable dhcpcd
        # veya systemd-networkd için: systemctl enable systemd-networkd
        ```
6.  **Root Şifresi:** Root kullanıcısı için bir şifre belirleyin:
    ```bash
    passwd
    ```
7.  **Önyükleyici (Bootloader):** Sistemin başlayabilmesi için bir önyükleyici kurulmalıdır.
    *   **GRUB (BIOS veya UEFI):**
        ```bash
        pacman -S grub # Gerekliyse os-prober'ı da kurun
        # BIOS için:
        # grub-install --target=i386-pc /dev/sda 
        # UEFI için (ESP /boot altına bağlıysa):
        grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
        # Yapılandırma dosyasını oluştur
        grub-mkconfig -o /boot/grub/grub.cfg 
        ```
    *   **systemd-boot (Sadece UEFI):** Daha basit bir alternatiftir.
        ```bash
        bootctl --path=/boot install # ESP'nin /boot altına bağlı olduğunu varsayar
        # /boot/loader/loader.conf ve /boot/loader/entries/arch.conf dosyalarını oluştur/düzenle
        ```
    Resmi Arch Wiki, önyükleyici kurulumu için detaylı bilgi sağlar.

## Kurulumu Tamamlama

1.  **chroot'tan Çık:**
    ```bash
    exit
    ```
2.  **Bölümleri Ayır (Unmount):**
    ```bash
    umount -R /mnt 
    ```
3.  **Yeniden Başlat:**
    ```bash
    reboot
    ```
    Kurulum medyasını çıkarın ve yeni kurulan Arch Linux sisteminizi başlatın. İlk açılışta kullanıcı adı olarak `root` ve belirlediğiniz şifre ile giriş yapın.

## Kurulum Sonrası Adımlar

*   **Kullanıcı Oluşturma:** Normal bir kullanıcı hesabı oluşturun:
    ```bash
    useradd -m -G wheel kullanıcı_adı
    passwd kullanıcı_adı
    ```
*   **Sudo Kurulumu:** `sudo` paketini kurun (`pacman -S sudo`) ve `visudo` komutu ile `/etc/sudoers` dosyasını düzenleyerek `%wheel ALL=(ALL:ALL) ALL` satırının başındaki `#` işaretini kaldırın. Bu, `wheel` grubundaki kullanıcıların `sudo` kullanmasını sağlar.
*   **Paketleri Güncelleme:** Sistemi tamamen güncelleyin:
    ```bash
    sudo pacman -Syu
    ```
*   **Grafik Arayüzü (İsteğe Bağlı):** Masaüstü ortamı (GNOME, KDE, XFCE vb.) ve Xorg veya Wayland görüntü sunucusunu kurun.
*   **AUR Yardımcıları (İsteğe Bağlı):** Arch User Repository (AUR)'dan paket kurmayı kolaylaştırmak için `yay` veya `paru` gibi bir yardımcı kurun.

Arch Linux kurulumu, sistemi temelden anlamak için harika bir yoldur, ancak dikkat ve sabır gerektirir. Her adımda Arch Wiki'ye başvurmaktan çekinmeyin.
