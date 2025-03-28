# Init Versiyonunu Belirlemek

Linux sisteminizde hangi init sisteminin (PID 1 olarak çalışan ve sistemi başlatan süreç) kullanıldığını bilmek, servisleri yönetmek ve sistemin davranışını anlamak için önemlidir. Modern dağıtımların çoğu `systemd` kullanırken, eski sistemlerde `System V init` (SysVinit) veya `Upstart` gibi alternatifler bulunabilir.

İşte çalışan init sistemini belirlemek için kullanılabilecek bazı yaygın yöntemler:

1.  **PID 1'in Komut Adını Kontrol Etme:**
    En doğrudan yöntemlerden biri, PID 1 olarak çalışan sürecin komut adını kontrol etmektir:
    ```bash
    ps -p 1 -o comm=
    ```
    *   Eğer çıktı `systemd` ise, sisteminiz systemd kullanıyor demektir.
    *   Eğer çıktı `init` ise, genellikle SysVinit veya Upstart kullanılıyor demektir. (Upstart genellikle eski Ubuntu sürümlerinde bulunur).

2.  **`/sbin/init` Sembolik Linkini Kontrol Etme:**
    Çekirdek genellikle `/sbin/init`'i başlatır. Bu dosyanın nereye işaret ettiğini kontrol etmek init sistemini gösterebilir:
    ```bash
    ls -l /sbin/init
    ```
    *   Eğer `/lib/systemd/systemd`'ye veya benzer bir yola işaret ediyorsa, systemd kullanılıyordur.
    *   Eğer doğrudan `/sbin/init` çalıştırılabilir bir dosyaysa veya başka bir `init` dosyasına işaret ediyorsa, muhtemelen SysVinit veya Upstart'tır.

3.  **`systemctl` Komutunun Varlığı ve Çalışması:**
    `systemctl` komutu, systemd'nin ana yönetim aracıdır. Bu komutun varlığı ve çalışması, systemd'nin kullanıldığının güçlü bir göstergesidir:
    ```bash
    systemctl status 
    # veya
    systemctl is-system-running
    ```
    Eğer bu komutlar systemd'ye özgü çıktılar veriyorsa veya hata vermeden çalışıyorsa, sisteminiz systemd kullanıyordur. SysVinit sistemlerde bu komut bulunmaz veya farklı davranır.

4.  **`/etc/inittab` Dosyasını Kontrol Etme:**
    Geleneksel SysVinit, ana yapılandırması için `/etc/inittab` dosyasını kullanır.
    ```bash
    ls /etc/inittab
    ```
    *   Eğer bu dosya **varsa ve içinde runlevel tanımları gibi aktif yapılandırma satırları içeriyorsa**, sistem muhtemelen SysVinit kullanıyordur.
    *   Modern systemd sistemlerinde `/etc/inittab` dosyası ya **bulunmaz** ya da sadece bilgilendirme amaçlı yorum satırları içeren **boş veya minimal** bir dosyadır.

5.  **`runlevel` Komutunu Kontrol Etme:**
    SysVinit sistemlerde mevcut çalışma seviyesini (runlevel) görmek için `runlevel` komutu kullanılır. systemd sistemlerde bu komut hala bulunabilir ancak genellikle farklı veya daha az anlamlı bir çıktı verir. systemd'de benzer işlev için `systemctl get-default` (varsayılan hedefi gösterir) veya `who -r` kullanılabilir.

Genellikle `ps -p 1 -o comm=` veya `ls -l /sbin/init` komutları, çalışan init sistemini hızlıca belirlemek için yeterlidir. Çoğu modern ve popüler Linux dağıtımı (Debian, Ubuntu, Fedora, RHEL, CentOS Stream, Arch Linux, SUSE vb.) artık varsayılan olarak systemd kullanmaktadır.
