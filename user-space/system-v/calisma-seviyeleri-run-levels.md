# Çalışma Seviyeleri (Runlevels)

System V init (SysVinit), sistemi farklı çalışma modlarına veya durumlarına sokmak için **çalışma seviyeleri (runlevels)** konseptini kullanır. Her runlevel, hangi servislerin çalıştırılacağını veya durdurulacağını tanımlar. Sistem belirli bir runlevel'a geçtiğinde, o runlevel için tanımlanmış servisler başlatılırken, önceki runlevel'da çalışıp yeni runlevel'da tanımlı olmayan servisler durdurulur.

## Standart Runlevel'lar

Geleneksel olarak 7 runlevel (0'dan 6'ya kadar) tanımlanmıştır ve bunların çoğu için standartlaşmış anlamlar bulunur, ancak dağıtımlar arasında küçük farklılıklar olabilir:

*   **Runlevel 0: Halt (Durdurma)**
    *   Sistemi güvenli bir şekilde kapatır. Bu runlevel'a geçmek, `shutdown -h now` veya `halt` komutlarına benzer.
*   **Runlevel 1: Single-User Mode (Tek Kullanıcı Modu)**
    *   Sistemi bakım ve kurtarma işlemleri için minimal bir modda başlatır. Genellikle ağ servisleri çalışmaz ve sadece root kullanıcısı giriş yapabilir. Kök dosya sistemi genellikle salt okunur (read-only) olarak bağlanabilir.
*   **Runlevel 2: Multi-User Mode (Çok Kullanıcılı Mod - Ağsız)**
    *   Debian tabanlı sistemlerde: Ağ servisleri *olmadan* çok kullanıcılı metin modu.
    *   RHEL tabanlı sistemlerde: Genellikle Runlevel 3 ile aynıdır (ağ destekli çok kullanıcılı metin modu).
*   **Runlevel 3: Multi-User Mode with Networking (Ağ Destekli Çok Kullanıcılı Mod)**
    *   Tamamen işlevsel, ağ destekli, komut satırı tabanlı çok kullanıcılı mod. Sunucular için **yaygın varsayılan** runlevel'dır.
*   **Runlevel 4: Unused/Custom (Kullanılmayan/Özel)**
    *   Genellikle sistem yöneticisi tarafından özel amaçlar için tanımlanmak üzere boş bırakılır.
*   **Runlevel 5: Multi-User Mode with Graphical Interface (Grafik Arayüzlü Çok Kullanıcılı Mod)**
    *   Runlevel 3'teki her şeye ek olarak grafiksel bir ekran yöneticisi (örn. GDM, KDM, LightDM) başlatılır. Masaüstü sistemler için **yaygın varsayılan** runlevel'dır.
*   **Runlevel 6: Reboot (Yeniden Başlatma)**
    *   Sistemi güvenli bir şekilde yeniden başlatır. Bu runlevel'a geçmek, `shutdown -r now` veya `reboot` komutlarına benzer.

## Runlevel Yönetimi

*   **Varsayılan Runlevel:** Sistemin normalde hangi runlevel'da başlayacağı genellikle `/etc/inittab` dosyasındaki `id:N:initdefault:` satırı ile belirlenir (burada N varsayılan runlevel numarasıdır, örn. 3 veya 5). Bazı modern SysVinit sistemleri veya önyükleyiciler bu ayarı farklı yerlerde tutabilir (örn. GRUB çekirdek parametresi).
*   **Mevcut Runlevel'ı Görme:** `runlevel` komutu, önceki ve mevcut runlevel'ı gösterir. Çıktıdaki ilk karakter önceki (eğer yoksa 'N'), ikinci karakter mevcut runlevel'dır.
    ```bash
    runlevel
    # Örnek Çıktı: N 3 
    ```
*   **Runlevel Değiştirme:** Çalışan sistemin runlevel'ını değiştirmek için `init` veya `telinit` komutları kullanılır (root yetkisiyle).
    ```bash
    # Runlevel 3'e geç
    sudo init 3 

    # Tek kullanıcı moduna geç (Runlevel 1)
    sudo telinit 1 
    ```

Runlevel konsepti, systemd'deki **target** birimleri ile benzer bir amaca hizmet eder, ancak systemd target'ları daha esnek ve bağımlılık tabanlı bir yapı sunar.
