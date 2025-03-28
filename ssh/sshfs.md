# sshfs

**sshfs** (SSH File System), FUSE (Filesystem in Userspace) kullanarak uzak bir sunucudaki bir dizini yerel makinenizdeki bir dizin gibi bağlamanıza (mount) olanak tanıyan bir araçtır. Tüm dosya transferi SSH protokolü üzerinden güvenli bir şekilde yapılır. Bu sayede uzak dosyalara yerel uygulamalarınızla (dosya yöneticisi, metin düzenleyici vb.) doğrudan erişebilirsiniz.

**Kurulum:**
`sshfs` genellikle sistemlerde önyüklü gelmez. Paket yöneticinizle kurmanız gerekir:
```bash
# Debian/Ubuntu
sudo apt install sshfs

# RHEL/CentOS/Fedora
sudo dnf install fuse-sshfs 

# Arch Linux
sudo pacman -S sshfs
```
Ayrıca, kullanıcınızın FUSE dosya sistemlerini bağlayabilmesi için genellikle `fuse` grubuna üye olması gerekebilir (`sudo usermod -aG fuse $USER` komutu sonrası yeniden giriş yapmanız gerekebilir) veya `/etc/fuse.conf` dosyasında `user_allow_other` seçeneğinin aktif edilmesi gerekebilir.

## Uzak Dosya Sistemini Bağlama (Mount)

1.  **Yerel Bağlama Noktası Oluşturma:** Uzak dizini bağlamak için yerel sisteminizde boş bir dizin oluşturun:

    ```bash
    mkdir ~/uzak-sunucu
    ```
2.  **`sshfs` Komutu ile Bağlama:**
    ```bash
    # Temel kullanım: sshfs [kullanici@]sunucu:[uzak_dizin] /yerel/baglama/noktasi
    sshfs root@sunucu-ip:/remote/dizin ~/uzak-sunucu 
    ```
    *   `root@sunucu-ip`: Uzak sunucudaki kullanıcı adı ve sunucu adresi/IP'si.
    *   `:/remote/dizin`: Uzak sunucuda bağlanılacak dizin (boş bırakılırsa kullanıcının ev dizini).
    *   `~/uzak-sunucu`: Yerel makinede oluşturulan bağlama noktası.

    **Seçenekler:**
    *   **Port Belirtme:** `-p` seçeneği yerine `-o port=PORT_NO` kullanılır:
        ```bash
        sshfs root@sunucu-ip:/remote/dizin ~/uzak-sunucu -o port=2222
        ```
    *   **Anahtar Kullanma:** `-o IdentityFile=ANAHTAR_YOLU` kullanılır:

        ```bash
        sshfs root@sunucu-ip:/remote/dizin ~/uzak-sunucu -o IdentityFile=~/.ssh/id_ed25519
        ```
    *   **Diğer Kullanıcıların Erişimi:** Varsayılan olarak sadece bağlayan kullanıcı erişebilir. Diğer kullanıcıların da erişmesi için `allow_other` seçeneği kullanılır (genellikle `/etc/fuse.conf` içinde `user_allow_other` ayarı da gerekir):
        ```bash
        sshfs root@sunucu-ip:/remote/dizin ~/uzak-sunucu -o allow_other
        ```
    *   **Bağlantı Kopmalarına Karşı:** Bağlantı koptuğunda otomatik yeniden bağlanmayı denemek için:
        ```bash
        sshfs root@sunucu-ip:/remote/dizin ~/uzak-sunucu -o reconnect
        ```

    Artık `~/uzak-sunucu` dizini altında yapacağınız dosya işlemleri (listeleme, okuma, yazma vb.) SSH üzerinden uzak sunucuda gerçekleşecektir.

## Otomatik Bağlama (`/etc/fstab`)

`sshfs` bağlantısının sistem başlangıcında otomatik olarak yapılmasını sağlamak için `/etc/fstab` dosyasına bir satır eklenebilir. Ancak bu yöntem, ağ bağlantısı hazır olmadan veya uzak sunucuya ulaşılamadığında sistem başlangıcının yavaşlamasına veya takılmasına neden olabilir. **Dikkatli kullanılmalıdır.**

Örnek `/etc/fstab` satırı:
```
# Kullanıcı 'kullanici_adi' olarak bağlanacak, anahtar kullanılacak, ağ beklenilecek
kullanici_adi@sunucu-ip:/uzak/dizin  /yerel/baglama/noktasi  fuse.sshfs  defaults,_netdev,user,identityfile=/home/kullanici_adi/.ssh/id_ed25519,allow_other,reconnect  0  0
```
*   `kullanici_adi@sunucu-ip:/uzak/dizin`: Uzak kaynak.
*   `/yerel/baglama/noktasi`: Yerel bağlama noktası.
*   `fuse.sshfs`: Dosya sistemi türü.
*   `defaults`: Standart bağlama seçenekleri.
*   `_netdev`: Bu bir ağ aygıtıdır, ağ hazır olana kadar bağlama işlemini geciktirir.
*   `user`: Normal kullanıcıların bağlamasına/ayırmasına izin verir (genellikle `allow_other` ile birlikte kullanılırsa anlamlıdır veya bağlama noktasının sahibi ilgili kullanıcı olmalıdır).
*   `identityfile=...`: Kullanılacak özel anahtar dosyasının tam yolu.
*   `allow_other`: Diğer kullanıcıların da erişimine izin verir (güvenlik etkilerini göz önünde bulundurun).
*   `reconnect`: Bağlantı koparsa yeniden bağlanmayı dener.
*   `0 0`: Dump ve fsck kontrolü için (ağ dosya sistemleri için genellikle 0).

**Alternatifler:** `/etc/fstab` yerine `systemd mount` veya `autofs` gibi daha modern ve esnek otomatik bağlama yöntemleri de kullanılabilir.

## Bağlantıyı Kesme (Unmount)

Bağlı uzak dosya sistemini ayırmak için standart `umount` komutu kullanılır:
```bash
umount ~/uzak-sunucu
```
Eğer "device is busy" hatası alırsanız, bağlama noktası içinde çalışan bir işlem olmadığından emin olun veya `-l` (lazy unmount) seçeneğini deneyin: `umount -l ~/uzak-sunucu`.
