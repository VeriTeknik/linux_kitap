# Ports ve pkg Paket Yönetimi

FreeBSD, üçüncü parti yazılımları kurmak ve yönetmek için iki ana sistem sunar: **Ports Koleksiyonu** ve **pkg** ikili (binary) paket yöneticisi. Her iki sistem de aynı yazılım havuzuna erişir ancak farklı yöntemlerle çalışır.

## 1. Ports Koleksiyonu (`/usr/ports`)

Ports Koleksiyonu, binlerce üçüncü parti yazılımı FreeBSD üzerinde kaynak koddan derlemek için gerekli dosyaları (Makefile, yamalar, açıklamalar vb.) içeren bir dizin ağacıdır (genellikle `/usr/ports`). Bu yöntem, derleme seçeneklerini özelleştirerek yazılımı optimize etme imkanı sunar ancak paket kurma işlemi daha uzun sürer ve derleme için gerekli araçların (derleyici, kütüphaneler vb.) sistemde bulunmasını gerektirir.

**Ports Ağacını Alma ve Güncelleme:**

*   **`portsnap` (Önerilen):** Ports ağacının sıkıştırılmış anlık görüntülerini (snapshot) kullanarak hızlı bir şekilde ilk alımı ve güncellemeyi sağlar.
    ```bash
    # İlk kez alırken:
    sudo portsnap fetch extract

    # Sonraki güncellemeler için:
    sudo portsnap fetch update
    ```
*   **`git` (Alternatif):** Ports ağacını bir Git deposu olarak klonlamak ve güncellemek de mümkündür.

**Port Bulma:**

*   **`whereis`:** Belirli bir komutun port dizinini bulmak için kullanılabilir.
    ```bash
    whereis htop 
    # Örnek Çıktı: htop: /usr/ports/sysutils/htop
    ```
*   **`make search`:** Ports ağacı içinde anahtar kelimeye göre arama yapar (indeks oluşturulmuşsa hızlıdır).
    ```bash
    cd /usr/ports
    sudo make search name=htop 
    # veya daha geniş arama:
    # sudo make search key=process
    ```

**Port Kurulumu:**

1.  **Dizine Git:** İlgili portun dizinine gidin.
    ```bash
    cd /usr/ports/sysutils/htop
    ```
2.  **(İsteğe Bağlı) Seçenekleri Yapılandırma:** Birçok port, derleme sırasında etkinleştirilecek veya devre dışı bırakılacak özellikleri seçmenize olanak tanır.
    ```bash
    sudo make config # Metin tabanlı menü açılır
    # Veya tüm bağımlılıklar için seçenekleri sorması:
    # sudo make config-recursive
    ```
    Seçenekler genellikle `/var/db/ports/` altında saklanır.
3.  **Derleme, Kurulum ve Temizlik:**
    ```bash
    # Derle, kur ve derleme sonrası geçici dosyaları temizle
    sudo make install clean 
    ```
    Bu işlem, gerekli bağımlılıkları da (ya portlardan derleyerek ya da `pkg` ile binary olarak kurarak) otomatik olarak halletmeye çalışır.

**Port Kaldırma:**

İlgili port dizinindeyken:
```bash
sudo make deinstall
```

**Derleme Dosyalarını Temizleme:**

Derleme sırasında oluşan geçici dosyaları (`work` dizini) temizlemek için:
```bash
sudo make clean
```

**Yardımcı Araçlar:**
Ports koleksiyonunu yönetmek için `portmaster` veya `poudriere` (toplu derleme için) gibi üçüncü parti araçlar da mevcuttur, ancak temel işlemler `make` komutları ile yapılabilir.

## 2. pkg İkili Paket Yöneticisi

`pkg`, FreeBSD için modern ikili (binary) paket yöneticisidir. Önceden derlenmiş paketleri resmi veya üçüncü parti depolardan hızlı bir şekilde kurmayı, güncellemeyi ve kaldırmayı sağlar. Genellikle Ports'a göre çok daha hızlıdır ve derleme gerektirmez. Eski `pkg_add`, `pkg_info` gibi komutların yerini almıştır.

**`pkg`'yi Başlatma (Bootstrapping):**

Eğer sistemde `pkg` kurulu değilse (genellikle ilk kurulumdan sonra gerekir), ilk `pkg` komutu çalıştırıldığında otomatik olarak kurulması teklif edilir:
```bash
# Örneğin:
sudo pkg update 
# Çıktı benzeri:
# The package management tool is not yet installed on your system.
# Do you want to fetch and install it now? [y/N]: y 
# ... pkg kurulur ...
```
Veya manuel olarak:
```bash
sudo pkg bootstrap
```

**Paket Kataloğunu Güncelleme:**

Depolardaki mevcut paket listesini güncellemek için:
```bash
sudo pkg update
```

**Paket Arama:**

Depolarda paket aramak için:
```bash
pkg search htop
pkg search -i screen # Büyük/küçük harf duyarsız arama
```

**Paket Kurma:**

Bir veya daha fazla paketi kurmak için:
```bash
sudo pkg install htop screen vim
```
Onay istenecektir (`-y` ile atlanabilir).

**Paket Bilgisi Gösterme:**

Kurulu veya depodaki bir paket hakkında detaylı bilgi almak için:
```bash
pkg info htop
```

**Kurulu Paketleri Listeleme:**

Sistemde kurulu tüm paketleri listelemek için:
```bash
pkg info | less
```

**Paketleri Güncelleme:**

Kurulu tüm paketleri depolardaki en son sürümlerine yükseltmek için:
```bash
sudo pkg upgrade
```

**Paket Kaldırma:**

Bir veya daha fazla paketi kaldırmak için:
```bash
sudo pkg delete htop
```

**Gereksiz Bağımlılıkları Kaldırma:**

Artık başka hiçbir paketin ihtiyaç duymadığı bağımlılıkları kaldırmak için:
```bash
sudo pkg autoremove
```

**Paket Denetimi (Güvenlik Açıkları):**

Kurulu paketlerde bilinen güvenlik açıklarını kontrol etmek için:
```bash
sudo pkg audit -F # Önce veritabanını günceller, sonra denetler
```

**İndirilen Paket Önbelleğini Temizleme:**

`pkg` tarafından indirilen eski paket dosyalarını temizlemek için:
```bash
sudo pkg clean
```

**Hangi Paketin Bir Dosyayı Sağladığını Bulma:**

```bash
pkg which /usr/local/bin/htop
```

## Ports mu, pkg mi?

*   **`pkg`:** Hızlı kurulum, kolay yönetim, derleme gerektirmez. Çoğu kullanıcı ve sunucu için genellikle yeterli ve **önerilen** yöntemdir.
*   **Ports:** Derleme seçeneklerini özelleştirme ihtiyacı olduğunda, belirli bir yama uygulamak gerektiğinde veya `pkg` deposunda bulunmayan/çok eski olan bir yazılımı kurmak için kullanılır. Daha fazla kontrol sunar ancak daha yavaş ve zahmetlidir.

İki sistem bir arada kullanılabilir, ancak dikkatli olmak gerekir. Genellikle `pkg` ile kurulan bir paketi Ports ile güncellemek veya tersi sorunlara yol açabilir. Birincil yöntem olarak birini seçmek ve diğerini sadece gerektiğinde kullanmak daha iyi bir yaklaşımdır.
