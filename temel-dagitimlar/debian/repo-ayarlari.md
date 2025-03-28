# APT Depo (Repository) Ayarları

`apt` paket yöneticisi, paketleri indirmek için **depo** (repository) adı verilen sunuculardaki kaynak listelerini kullanır. Bu depoların tanımları `/etc/apt/sources.list` dosyasında ve `/etc/apt/sources.list.d/` dizini altındaki `.list` uzantılı dosyalarda bulunur.

## `sources.list` Dosyasının Yapısı

`/etc/apt/sources.list` dosyasındaki her satır genellikle şu formatı takip eder:

```
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware

deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
deb-src http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware

# bookworm-updates, previously known as 'volatile'
deb http://deb.debian.org/debian/ bookworm-updates main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm-updates main contrib non-free non-free-firmware
```
(Yukarıdaki örnek Debian 12 "Bookworm" içindir.)

*   **Tür:**
    *   `deb`: İkili (binary) paketler için depo (en yaygın).
    *   `deb-src`: Kaynak kod paketleri için depo (genellikle geliştiriciler için gereklidir).
*   **URI:** Deponun adresi (örn. `http://deb.debian.org/debian/`). Genellikle ülkeye özgü bir ayna (mirror) adresi de kullanılabilir (örn. `http://ftp.tr.debian.org/debian/`).
*   **Dağıtım (Distribution):** Hangi Debian sürümünün kullanılacağını belirtir. Bu, sürümün kod adı (`bookworm`, `trixie` vb.) veya sürüm dalı (`stable`, `testing`, `unstable`) olabilir. Güvenlik güncellemeleri için genellikle `<kodadı>-security` (örn. `bookworm-security`), önemli güncellemeler için `<kodadı>-updates` (örn. `bookworm-updates`) kullanılır.
*   **Bileşen(ler) (Component(s)):** Deponun hangi bölümlerinin kullanılacağını belirtir. Boşlukla ayrılırlar. Standart Debian bileşenleri şunlardır:
    *   `main`: Debian Özgür Yazılım Yönergeleri'ne (DFSG) uyan, tamamen özgür paketleri içerir. Debian sisteminin temelini oluşturur.
    *   `contrib`: DFSG'ye uyan ancak `non-free` bölümündeki paketlere bağımlı olan özgür paketleri içerir.
    *   `non-free`: DFSG'ye uymayan, yani özgür olmayan lisanslara sahip paketleri içerir (örn. bazı aygıt sürücüleri, firmware'ler, özel yazılımlar).
    *   `non-free-firmware`: Debian 12 ile birlikte `non-free` bölümünden ayrılan, özgür olmayan firmware dosyalarını içeren bileşen. Kurulum sırasında veya sonrasında bazı donanımların çalışması için gerekebilir.

## `/etc/apt/sources.list.d/` Dizini

Sistemin ana `sources.list` dosyasını temiz tutmak ve üçüncü parti depoları (örneğin Google Chrome, Spotify, Docker vb. tarafından sağlananlar) veya kişisel depoları yönetmek için `/etc/apt/sources.list.d/` dizini kullanılır. Bu dizine eklenen `.list` uzantılı her dosya, `sources.list` ile aynı formatta depo tanımları içerebilir. `apt update` komutu bu dizindeki dosyaları da okur.

Örnek bir `/etc/apt/sources.list.d/google-chrome.list` dosyası:
```
deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main
```

## Depo Eklemek ve Yönetmek

**Manuel Düzenleme:**
Depoları yönetmenin en temel yolu `/etc/apt/sources.list` dosyasını veya `/etc/apt/sources.list.d/` altındaki dosyaları bir metin düzenleyici ile (root yetkisiyle) düzenlemektir.

**`add-apt-repository` (Özellikle Ubuntu/PPA için):**
Bu komut, özellikle Ubuntu'daki PPA (Personal Package Archives) depolarını eklemek için kullanılır. Depo adresini, dağıtım bilgilerini ve gerekli GPG anahtarını otomatik olarak eklemeye çalışır. Debian'da da bulunabilir (`software-properties-common` paketi ile gelir) ancak PPA'lar Debian için resmi olarak desteklenmez ve kararlılık sorunlarına yol açabilir.
```bash
# Örnek bir PPA ekleme (Ubuntu'da yaygın)
# sudo add-apt-repository ppa:webupd8team/java
# sudo apt update 
```
Normal depoları eklemek için de kullanılabilir, ancak genellikle manuel düzenleme veya ilgili yazılımın belgelerinde belirtilen yöntem (örn. GPG anahtarını indirip `.list` dosyası oluşturma) daha yaygındır.

**GPG Anahtarları:**
Güvenli depolar, paketlerin orijinalliğini doğrulamak için GPG anahtarları kullanır. Bir depo eklediğinizde, genellikle o deponun GPG anahtarını da sisteme eklemeniz gerekir. Bu işlem genellikle `wget` veya `curl` ile anahtarı indirip `gpg --dearmor` ile dönüştürdükten sonra `/etc/apt/trusted.gpg.d/` dizinine kaydetmek veya `apt-key add` (eski yöntem, daha az güvenli) komutu ile yapılır. Modern `.list` dosyaları `signed-by` seçeneği ile doğrudan anahtar dosyasını belirtebilir. Üçüncü parti depoları eklerken, ilgili yazılımın belgelerindeki adımları takip etmek en doğrusudur.

## Değişiklikleri Uygulama (`apt update`)

`/etc/apt/sources.list` dosyasında veya `/etc/apt/sources.list.d/` altındaki dosyalarda herhangi bir değişiklik yaptıktan sonra, `apt`'nin yeni depo bilgilerini ve paket listelerini alması için **mutlaka** aşağıdaki komut çalıştırılmalıdır:
```bash
sudo apt update
```
Bu komut, yeni eklenen depolardaki paketleri `apt search` ile aranabilir ve `apt install` ile kurulabilir hale getirir.
