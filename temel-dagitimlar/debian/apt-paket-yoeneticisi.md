# apt Paket Yöneticisi

Debian ve türevi sistemlerde (Ubuntu, Mint vb.) paket yönetimi için kullanılan ana araç **APT** (Advanced Package Tool) ailesidir. Bu aile, paketleri kurmak, güncellemek, kaldırmak ve depoları yönetmek için çeşitli komut satırı araçları sunar.

## `apt`, `apt-get`, `apt-cache` ve `aptitude`

*   **`apt`**: Modern ve kullanıcı dostu komut satırı arayüzüdür. `apt-get` ve `apt-cache`'in en sık kullanılan komutlarını daha basit bir sözdizimi ve ek özelliklerle (örn. ilerleme çubuğu) birleştirir. **Genellikle günlük kullanım için önerilen araçtır.**
*   **`apt-get`**: Paketleri kurmak, güncellemek ve kaldırmak için kullanılan daha eski, temel komut satırı aracıdır. Betiklerde (scripting) hala yaygın olarak kullanılır.
*   **`apt-cache`**: Paket deposu önbelleğini sorgulamak (paket aramak, bilgi göstermek vb.) için kullanılan eski araçtır. `apt` komutu bu işlevleri de içerir.
*   **`aptitude`**: Hem komut satırı hem de metin tabanlı interaktif bir arayüz sunan alternatif bir paket yöneticisidir. Özellikle karmaşık bağımlılık sorunlarını çözmede bazen `apt` veya `apt-get`'ten daha yetenekli olabilir.

Bu bölümde, modern ve önerilen `apt` komutuna odaklanacağız. Komutların çoğu `sudo` yetkisi gerektirir.

## Paket Listelerini Güncelleme (`update`)

Paket kurulumu veya güncellemesi yapmadan önce, sistemin mevcut depolarındaki paket listelerini yenilemek önemlidir. Bu işlem, hangi paketlerin yeni sürümlerinin olduğunu sisteme bildirir.
```bash
sudo apt update
```
Bu komut herhangi bir paketi kurmaz veya güncellemez, sadece paket bilgilerini günceller.

## Paketleri Güncelleme (`upgrade`, `full-upgrade`)

Kurulu paketleri mevcut depolardaki en son sürümlerine yükseltmek için `upgrade` kullanılır:
```bash
# Kurulu paketleri güncelle (yenilerini kurmaz, mevcutları kaldırmaz)
sudo apt upgrade
```
Onay istenecektir. `-y` parametresi ile onay otomatik olarak verilebilir:
```bash
sudo apt upgrade -y
```
`upgrade` komutu, mevcut paketleri güncellerken sistemde kurulu paketleri kaldırmaz veya yenilerini eklemez. Eğer bir güncelleme işlemi yeni bağımlılıkların kurulmasını veya çakışan paketlerin kaldırılmasını gerektiriyorsa, `upgrade` o paketi güncellemez.

Bu tür durumları da yöneterek (gerekirse paket kaldırıp/ekleyerek) sistemi tamamen güncellemek için `full-upgrade` (eski `apt-get dist-upgrade` komutunun eşdeğeri) kullanılır:
```bash
sudo apt full-upgrade -y
```
Genellikle sunucularda `upgrade` daha kontrollü bir seçenekken, masaüstü sistemlerde veya sürüm yükseltmelerinde `full-upgrade` gerekebilir.

Hangi paketlerin güncellenebileceğini görmek için:
```bash
apt list --upgradable
```

## Paket Arama (`search`)

Depolarda belirli bir anahtar kelimeye göre paket aramak için `search` kullanılır:
```bash
# Adında veya açıklamasında 'htop' geçen paketleri ara
apt search htop
```

## Paket Bilgisi Gösterme (`show`)

Belirli bir paket hakkında detaylı bilgi (sürüm, bağımlılıklar, açıklama, boyut vb.) almak için `show` kullanılır:
```bash
apt show htop
```

## Paket Kurma (`install`)

Bir veya daha fazla paketi kurmak için `install` kullanılır. `apt`, gerekli bağımlılıkları otomatik olarak bulup kurar:
```bash
sudo apt install htop screen vim
```
Yerel bir `.deb` dosyasını kurarken de `apt` kullanmak, bağımlılıkların depolardan otomatik olarak çözülmesini sağlar:
```bash
sudo apt install ./indirilen-paket.deb
```

## Paket Kaldırma (`remove`, `purge`)

Bir paketi kaldırmak (ancak yapılandırma dosyalarını sistemde bırakmak) için `remove` kullanılır:
```bash
sudo apt remove htop
```
Bir paketi yapılandırma dosyalarıyla birlikte tamamen kaldırmak için `purge` kullanılır:
```bash
sudo apt purge htop
```

## Gereksiz Paketleri Kaldırma (`autoremove`)

Bir paket kurulduğunda otomatik olarak kurulan ancak artık başka hiçbir kurulu paketin ihtiyaç duymadığı bağımlılıkları sistemden kaldırmak için `autoremove` kullanılır. Genellikle `remove` veya `purge` işleminden sonra çalıştırılması önerilir:
```bash
sudo apt autoremove
```

## Kurulu Paketleri Listeleme (`list`)

Sistemde kurulu paketleri listelemek için:
```bash
apt list --installed | less
```
Belirli bir paketin kurulu olup olmadığını kontrol etmek için:
```bash
apt list --installed | grep htop
# veya daha basitçe:
apt list htop 
```

## Diğer Kullanışlı `apt` Komutları

*   **`edit-sources`**: `/etc/apt/sources.list` dosyasını düzenlemek için varsayılan metin düzenleyiciyi açar.
*   **`clean`**: İndirilen `.deb` paket dosyalarının yerel önbelleğini (`/var/cache/apt/archives/`) temizler. Disk alanı açmak için kullanılabilir.
    ```bash
    sudo apt clean
    ```
*   **`autoclean`**: `clean` gibidir ancak sadece artık depolarda bulunmayan veya daha yeni sürümü olan eski paket dosyalarını temizler.

## Easter Egg

`apt` komutunun da (tıpkı `apt-get` gibi) küçük bir sürprizi vardır:
```bash
apt moo
```

`apt`, Debian tabanlı sistemlerde paket yönetimini kolaylaştıran güçlü ve modern bir araçtır. Çoğu durumda `apt-get` ve `apt-cache` yerine tercih edilebilir.
