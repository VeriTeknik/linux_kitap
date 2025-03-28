# Güncelleme Teknikleri

FreeBSD'de güncellemeler iki ana kategoriye ayrılır: **Temel Sistem Güncellemeleri** (işletim sisteminin çekirdeği ve ana kullanıcı alanı araçları) ve **Paket Güncellemeleri** (kurulan üçüncü parti yazılımlar). Bu iki tür güncelleme için farklı araçlar kullanılır.

## 1. Temel Sistem Güncellemeleri (`freebsd-update`)

`freebsd-update` aracı, FreeBSD temel sistemini (kernel ve world) resmi FreeBSD sunucularından indirilen ikili (binary) yamalar veya dosyalar aracılığıyla güncellemek için kullanılır. Kaynak koddan derleme yapmaya gerek kalmadan sistemi güncel tutmanın standart ve önerilen yoludur.

**Güvenlik Yamalarını ve Küçük Güncellemeleri Alma/Uygulama:**

Bu, sisteminizi mevcut sürüm dalındaki (örn. 13.2-RELEASE) en son yama seviyesine getirir.
```bash
# Güncellemeleri kontrol et ve indir
sudo freebsd-update fetch

# İndirilen güncellemeleri kur
sudo freebsd-update install 
```
`freebsd-update install` komutu çekirdek güncellemeleri içeriyorsa, genellikle sistemi yeniden başlatmanızı (`sudo shutdown -r now`) ve ardından komutu *tekrar* çalıştırmanızı isteyecektir (kullanıcı alanı güncellemelerini tamamlamak için).

**Yeni Bir Küçük Sürüme (Minor Version) Yükseltme:**

Örneğin, 13.1-RELEASE'den 13.2-RELEASE'e yükseltmek için:
```bash
# Hedef sürümü belirterek yükseltme işlemini başlat
sudo freebsd-update -r 13.2-RELEASE upgrade

# Komut, indirilecek değişiklikleri ve potansiyel olarak manuel müdahale 
# gerektirebilecek yapılandırma dosyası farklılıklarını gösterecektir.
# Dikkatlice inceleyin ve onaylayın (genellikle 'yes' ile).
# ... (İndirme ve hazırlık süreci) ...

# İlk kurulum adımını çalıştır (genellikle yeni çekirdek kurulur)
sudo freebsd-update install

# Sistemi yeniden başlat
sudo shutdown -r now

# Yeniden başlattıktan sonra, kalan güncellemeleri kur
sudo freebsd-update install 
```

**Yeni Bir Ana Sürüme (Major Version) Yükseltme:**

Örneğin, 12.4-RELEASE'den 13.2-RELEASE'e yükseltmek için süreç küçük sürüm yükseltmeye benzer, ancak daha fazla değişiklik içerir ve daha dikkatli olunmalıdır:
```bash
# Hedef sürümü belirterek yükseltme işlemini başlat
sudo freebsd-update -r 13.2-RELEASE upgrade

# ... (Değişiklikleri incele, onayla, indir) ...

# İlk kurulum (yeni çekirdek)
sudo freebsd-update install

# Sistemi yeniden başlat
sudo shutdown -r now

# Kalan güncellemeleri kur (ilk adım)
sudo freebsd-update install 

# ÖNEMLİ: Ana sürüm yükseltmesinden sonra, kurulan tüm paketlerin
# yeni sürümle uyumlu hale getirilmesi gerekir. Bu genellikle 
# 'pkg upgrade' ile yapılır (bkz. Paket Güncellemeleri).
sudo pkg bootstrap -f # pkg'nin kendisini güncellemek gerekebilir
sudo pkg upgrade -y

# Paketler güncellendikten sonra, eski kütüphaneleri vb. kaldırmak için
# freebsd-update install komutunu SON KEZ çalıştırın.
sudo freebsd-update install 
```
Ana sürüm yükseltmeleri öncesinde mutlaka FreeBSD Sürüm Notları'nı (Release Notes) ve Errata'yı okumak, önemli verileri yedeklemek şiddetle tavsiye edilir.

## 2. Paket Güncellemeleri (`pkg upgrade`)

Sisteminize `pkg install` ile veya Ports üzerinden kurduğunuz üçüncü parti yazılımları (paketleri) güncellemek için `pkg upgrade` komutu kullanılır. Bu komut, yapılandırılmış depolardaki en son paket sürümlerini kontrol eder ve kurulu paketleri günceller.

```bash
# Önce paket kataloğunu güncelle (isteğe bağlı ama önerilir)
sudo pkg update

# Kurulu tüm paketleri en son sürümlerine yükselt
sudo pkg upgrade 
```
Onay istenecektir (`-y` ile atlanabilir).

Eğer paketleri Ports Koleksiyonu'ndan derleyerek kurduysanız, `pkg upgrade` yine de çalışabilir (eğer güncel binary paketler depoda mevcutsa). Ancak, derleme seçenekleriniz özel ise veya binary paket yoksa, Ports ağacını güncelledikten sonra (`portsnap fetch update` veya `git pull`) ilgili port dizininde `make deinstall install clean` yapmak veya `portmaster -a` gibi bir araç kullanmak gerekebilir. Yine de çoğu durumda `pkg upgrade` yeterli olacaktır.

## 3. Ports Ağacını Güncelleme (`portsnap`)

Eğer yazılımları kaynak koddan derlemek için Ports Koleksiyonu'nu kullanıyorsanız, `/usr/ports` dizinindeki ağacı güncel tutmanız gerekir. Bu işlem, kurulu paketleri *güncellemez*, sadece yeni yazılımları derlemek için gerekli olan "tarifleri" (Makefile vb.) günceller.

```bash
# Ports ağacını güncelle
sudo portsnap fetch update
```
Bu komut, sadece Ports kullanıyorsanız düzenli olarak çalıştırılmalıdır. Sadece `pkg` kullanıyorsanız bu adıma genellikle gerek yoktur.

Özetle:
*   Temel FreeBSD sistemi için: `sudo freebsd-update fetch` ve `sudo freebsd-update install`.
*   Kurulu paketler için: `sudo pkg update` ve `sudo pkg upgrade`.
*   Ports Koleksiyonu'nun kendisi için (eğer kullanılıyorsa): `sudo portsnap fetch update`.
