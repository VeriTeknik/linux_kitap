# pacman ve AUR

Arch Linux'un paket yöneticisi **pacman**, sistemin en belirgin özelliklerinden biridir. `pacman`, ikili (binary) paketleri resmi depolardan (repositories) veya yerel dosyalardan kurmayı, güncellemeyi, kaldırmayı ve sorgulamayı sağlar. Bağımlılıkları otomatik olarak yönetir ve sistemi güncel tutmayı kolaylaştırır. Arch Linux paketleri genellikle `.pkg.tar.zst` (veya eski `.pkg.tar.xz`) formatındadır.

`pacman`'in ana yapılandırma dosyası `/etc/pacman.conf`'tur. Bu dosyada aktif depolar, genel seçenekler (renkli çıktı, paralel indirmeler vb.) ve yoksayılacak paketler gibi ayarlar bulunur.

## `pacman` Temel Komutları

**Paket Listelerini ve Sistemi Güncelleme:**

Arch Linux sürekli güncellenen (rolling release) bir model kullandığı için, sistemi düzenli olarak güncellemek önemlidir. Paket kurmadan önce de genellikle sistemin güncellenmesi tavsiye edilir.

*   **`-Syyu`**: Yerel paket veritabanını sunucularla zorla senkronize eder (`yy`) ve kurulu tüm paketleri günceller (`u`). Genellikle ayna (mirror) listesi değiştirildikten sonra kullanılır.
*   **`-Syu`**: Yerel paket veritabanını (gerekirse) senkronize eder (`y`) ve kurulu tüm paketleri günceller (`u`). **En sık kullanılan güncelleme komutudur.**
    ```bash
    sudo pacman -Syu 
    ```
*   **`-Sy`**: Sadece yerel paket veritabanını senkronize eder. Paket kurmadan sadece `-Sy` çalıştırmak, güncel olmayan bir sistemle yeni paket listesi arasında uyumsuzluklara (partial upgrades) yol açabileceğinden **kesinlikle önerilmez**.

**Paket Kurma:**

*   **`-S <paket_adı>`**: Belirtilen paketi (ve bağımlılıklarını) depolardan indirip kurar.
    ```bash
    sudo pacman -S htop vim screen
    ```
*   **`-U <dosya_adı.pkg.tar.zst>`**: Yerel bir paket dosyasını (ve `pacman`'in bulabildiği bağımlılıklarını) kurar.
    ```bash
    sudo pacman -U ./indirilen-paket.pkg.tar.zst
    ```

**Paket Kaldırma:**

*   **`-R <paket_adı>`**: Belirtilen paketi kaldırır, ancak bağımlılıklarına dokunmaz.
*   **`-Rs <paket_adı>`**: Belirtilen paketi ve artık başka hiçbir paket tarafından gereksinim duyulmayan bağımlılıklarını kaldırır (`s`: recursive). **Genellikle tercih edilen kaldırma yöntemidir.**
    ```bash
    sudo pacman -Rs htop
    ```
*   **`-Rns <paket_adı>`**: `-Rs` gibidir, ancak kaldırırken yapılandırma dosyalarını da siler (`n`: nosave).
*   **`-Rsc <paket_adı>`**: Paketi, bağımlılıklarını ve bu pakete bağımlı olan diğer tüm paketleri kaldırır (`c`: cascade). **Dikkatli kullanılmalıdır!**
*   **`-Rdd <paket_adı>`**: Bağımlılık kontrollerini tamamen yoksayarak paketi kaldırır. **Çok tehlikelidir ve genellikle kullanılmamalıdır!**

**Paket Sorgulama:**

*   **`-Ss <anahtar_kelime>`**: Depolarda paket adı veya açıklamasında arama yapar (`s`: search).
    ```bash
    pacman -Ss network manager
    ```
*   **`-Qs <anahtar_kelime>`**: Kurulu paketler içinde arama yapar.
*   **`-Si <paket_adı>`**: Depodaki bir paket hakkında detaylı bilgi gösterir (`i`: info).
*   **`-Qi <paket_adı>`**: Kurulu bir paket hakkında detaylı bilgi gösterir.
*   **`-Ql <paket_adı>`**: Kurulu bir paketin içerdiği dosyaları listeler (`l`: list).
*   **`-Qo <dosya_yolu>`**: Belirtilen dosyanın hangi pakete ait olduğunu gösterir (`o`: owns).
    ```bash
    pacman -Qo /usr/bin/htop
    ```
*   **`-Qdt`**: Yetim (orphan) paketleri, yani başka hiçbir paket tarafından gereksinim duyulmayan bağımlılıkları listeler (`d`: dependencies, `t`: unrequired). Bunlar genellikle `sudo pacman -Rns $(pacman -Qdtq)` komutuyla temizlenebilir (`q`: quiet, sadece isimleri listeler).
*   **`-Qe`**: Açıkça (manuel olarak) kurulmuş paketleri listeler. Bağımlılık olarak kurulanları göstermez (`e`: explicit).
*   **`-Qk <paket_adı>`**: Kurulu bir paketin dosyalarının bütünlüğünü kontrol eder (`k`: check).

## AUR (Arch User Repository)

AUR (Arch User Repository), Arch Linux'un resmi depolarında bulunmayan binlerce paketin topluluk tarafından sağlanan yapılandırma betiklerini (PKGBUILD) içeren bir depodur. AUR'daki paketler genellikle kaynak koddan derlenir.

**PKGBUILD:** Bir paketin nasıl indirileceğini, derleneceğini ve paketleneceğini tanımlayan bir kabuk betiğidir.

**makepkg:** Bir `PKGBUILD` dosyasını kullanarak paketi derleyen ve `.pkg.tar.zst` formatında paket oluşturan araçtır (`pacman` ile birlikte gelir).

**AUR Yardımcıları (AUR Helpers):** AUR'dan paket arama, indirme, derleme ve kurma işlemlerini otomatikleştiren araçlardır (örn. `yay`, `paru`). `pacman`'in kendisi doğrudan AUR ile etkileşim kurmaz.

**Uyarı:** AUR paketleri topluluk tarafından sağlandığı için resmi depolar kadar denetlenmezler. Kurmadan önce `PKGBUILD` dosyasını incelemek güvenlik açısından iyi bir pratiktir.

**AUR Yardımcısı Kurulumu (Örnek: `yay`)**

Bir AUR yardımcısını kurmak için genellikle önce onun `PKGBUILD` dosyasını AUR web sitesinden bulup manuel olarak kurmanız gerekir (bootstrap). `yay` popüler bir yardımcıdır:

1.  **Gerekli Paketleri Kur:** `base-devel` grubu ve `git` genellikle gereklidir.
    ```bash
    sudo pacman -Syu --needed base-devel git
    ```
2.  **`yay` deposunu klonla:** AUR'dan `yay`'ın PKGBUILD'ini içeren git deposunu klonlayın (genellikle geçici bir dizine).
    ```bash
    # Örnek: /tmp dizinine klonlama
    cd /tmp
    git clone https://aur.archlinux.org/yay.git
    ```
3.  **Derle ve Kur:** Klonlanan dizine girin ve `makepkg` ile paketi oluşturup kurun.
    ```bash
    cd yay
    makepkg -si
    ```
    *   `m`: Eksik bağımlılıkları `pacman` ile kurar (make dependencies).
    *   `s`: Kaynak kod bağımlılıklarını `pacman` ile kurar (sync dependencies).
    *   `i`: Paket başarıyla derlenirse `pacman -U` ile kurar (install).

    `makepkg` işlemi sırasında PGP anahtarlarını içe aktarmanız istenebilir. Güvenilir anahtarları içe aktarmak için onay vermeniz gerekir.

4.  **(İsteğe Bağlı) Temizlik:** Derleme dizinini silebilirsiniz.
    ```bash
    cd ..
    rm -rf yay
    ```

**AUR Yardımcısı Kullanımı (Örnek: `yay`)**

`yay` kurulduktan sonra, `pacman` komutlarına benzer şekilde hem resmi depolardan hem de AUR'dan paket yönetimi için kullanılabilir:

```bash
# AUR'da ve resmi depolarda paket ara
yay -Ss <anahtar_kelime>

# AUR'dan veya resmi depodan paket kur/güncelle
yay -S <paket_adı>

# Sistemi ve AUR paketlerini güncelle (pacman -Syu + AUR güncellemeleri)
yay -Syu 
# veya sadece:
yay 

# Kurulu paketleri kaldır (pacman -R komutlarını kullanır)
yay -Rns <paket_adı> 

# AUR paketlerini geliştirme sürümlerini de kontrol ederek güncelle
# yay -Syu --devel 
```

AUR, Arch Linux'un gücünü ve esnekliğini artıran önemli bir parçasıdır, ancak kullanırken dikkatli olmak gerekir.
