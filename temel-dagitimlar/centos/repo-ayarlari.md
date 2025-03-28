# DNF Depo (Repository) Ayarları

`dnf` paket yöneticisi, RPM paketlerini **depo** (repository) adı verilen kaynaklardan indirir. Bu depolar, dağıtımın resmi paketlerini barındıran sunucular olabileceği gibi, üçüncü parti yazılımları veya güncel sürümleri içeren ek depolar da olabilir.

## Depo Yapılandırma Dosyaları (`.repo`)

Yazılım depoları, `/etc/yum.repos.d/` dizini altında bulunan `.repo` uzantılı metin dosyaları ile tanımlanır. Her bir `.repo` dosyası bir veya daha fazla depo bölümü içerebilir.

Bir depo bölümü genellikle aşağıdaki gibi görünür:

```ini
[repo-id]
name=Deponun Açıklayıcı Adı
baseurl=http://depo.sunucusu/yol/mimari/
# veya mirrorlist=http://ayna.listesi.sunucusu/?release=$releasever&arch=$basearch&repo=repo-id
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-repo-id
```

*   **`[repo-id]`**: Depo için benzersiz bir kimlik (ID). Köşeli parantez içinde yazılır.
*   **`name`**: Deponun insan tarafından okunabilir adı.
*   **`baseurl`**: Deponun bulunduğu ana URL. Birden fazla `baseurl` satırı olabilir. `$releasever` (örn. 8, 9) ve `$basearch` (örn. x86_64, aarch64) gibi değişkenler içerebilir.
*   **`mirrorlist`**: `baseurl` yerine, coğrafi olarak en yakın veya en hızlı depoyu bulmak için kullanılan bir ayna listesi URL'si.
*   **`enabled`**: Deponun aktif olup olmadığını belirtir (`1` aktif, `0` pasif).
*   **`gpgcheck`**: Paketlerin GPG imzalarının kontrol edilip edilmeyeceğini belirtir (`1` evet, `0` hayır). Güvenlik için genellikle `1` olması önerilir.
*   **`gpgkey`**: Paketleri doğrulamak için kullanılacak GPG anahtarının URL'si veya dosya yolu. `gpgcheck=1` ise bu gereklidir.

## Depoları Listeleme ve Yönetme

**Aktif Depoları Listeleme:**
Sistemde tanımlı ve aktif (enabled) olan depoları listelemek için:
```bash
sudo dnf repolist
```

**Tüm Depoları Listeleme (Aktif ve Pasif):**
```bash
sudo dnf repolist all
```

**Depoları Etkinleştirme/Devre Dışı Bırakma (Geçici - Komut Bazında):**
Bir `dnf` komutunu çalıştırırken belirli depoları geçici olarak etkinleştirmek veya devre dışı bırakmak için `--enablerepo` ve `--disablerepo` seçenekleri kullanılır:
```bash
# epel deposunu geçici olarak etkinleştirerek zmap paketini ara
sudo dnf --enablerepo=epel search zmap

# base ve updates dışındaki tüm depoları devre dışı bırakarak sadece bu depolardaki paketleri listele
sudo dnf --disablerepo="*" --enablerepo="base,updates" list available 
```

**Depoları Etkinleştirme/Devre Dışı Bırakma (Kalıcı - `dnf config-manager`):**
`dnf-plugins-core` paketi ile gelen `dnf config-manager` aracı, depoları kalıcı olarak yönetmek için kolay bir yol sunar (eğer kurulu değilse `sudo dnf install dnf-plugins-core` ile kurun):
```bash
# epel deposunu kalıcı olarak etkinleştir
sudo dnf config-manager --set-enabled epel

# epel deposunu kalıcı olarak devre dışı bırak
sudo dnf config-manager --set-disabled epel

# Yeni bir depo URL'si ekle
sudo dnf config-manager --add-repo http://ornek.com/yeni/depo.repo
```
Alternatif olarak, ilgili `.repo` dosyasındaki `enabled=1` veya `enabled=0` satırını manuel olarak da düzenleyebilirsiniz.

## Örnek: EPEL Deposunu Ekleme

EPEL (Extra Packages for Enterprise Linux), Fedora topluluğu tarafından yönetilen ve RHEL tabanlı dağıtımlar için birçok ek paket içeren popüler bir depodur.

Modern RHEL, CentOS Stream, Rocky Linux, AlmaLinux sürümlerine EPEL eklemek için genellikle ilgili `epel-release` paketini kurmak yeterlidir:
```bash
# EPEL deposunu kur ve etkinleştir
sudo dnf install epel-release -y

# Depo listesini kontrol et
sudo dnf repolist
```
Bu komut, gerekli `.repo` dosyasını ve GPG anahtarını `/etc/yum.repos.d/` altına otomatik olarak ekler.

## Depo Kaldırma

Bir depo genellikle onu ekleyen paket (örneğin `epel-release`) kaldırılarak sistemden kaldırılır:
```bash
# EPEL deposunu kaldır
sudo dnf remove epel-release
```
Eğer depo manuel olarak bir `.repo` dosyası ile eklenmişse, ilgili `.repo` dosyasını `/etc/yum.repos.d/` dizininden silmek veya `enabled=0` olarak ayarlamak yeterlidir. Depo kaldırıldıktan veya devre dışı bırakıldıktan sonra `dnf clean all` komutu ile önbelleği temizlemek iyi bir pratik olabilir.
