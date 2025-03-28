# BASH Programlama

GNU/Linux üzerinde bugün en yaygın kullanılan kabuğun **bash** (Bourne Again SHell) olduğunu söyleyebiliriz. Bu bölümde bash kullanarak akıllı işlemleri nasıl yapacağımızı, temel kontrolleri, hatta basit bazı yükleme scriptleri geliştirmeyi göreceğiz.

## Temel Kontroller

Bash scriptlerinin temel yapısı, başında yorumlayıcının belirtildiği, ardından gerekli komutların sıralandığı dosyalardır. Aşağıdaki scripti `yukle.sh` ismiyle kaydedip çalıştıralım.

```bash
#!/bin/bash
# yukle.sh dosya icerigi

echo "Yukleme basliyor..."
```

```bash
eaydin@dixon ~/calisma/bash $ chmod +x yukle.sh 
eaydin@dixon ~/calisma/bash $ ./yukle.sh
Yukleme basliyor...
```

Tabii scriptimiz bu haliyle pek işe yaramıyor. Örneğin **htop** programının yüklenmesini sağlayabiliriz.

```bash
eaydin@dixon ~/calisma/bash $ cat yukle.sh 
#!/bin/bash
# yukle.sh dosya icerigi

echo "Yukleme basliyor..."
apt-get install htop
```

```bash
eaydin@dixon ~/calisma/bash $ ./yukle.sh 
Yukleme basliyor...
E: Could not open lock file /var/lib/dpkg/lock - open (13: Permission denied)
E: Unable to lock the administration directory (/var/lib/dpkg/), are you root?
```

Gördüğünüz gibi, apt-get programı **root** yetkimiz olmadığı için hata verdi. root yetkimizin olup olmadığının kontrolünü scriptimize yaptırıp buna uygun bir hata verebilirdik.

```bash
eaydin@dixon ~/calisma/bash $ cat yukle.sh 
#!/bin/bash
# yukle.sh dosya icerigi

# Root yetkisi kontrolü (Modern Yöntem: $EUID)
if [[ "$EUID" -ne 0 ]]; then
    echo "Hata: Bu betik root yetkisi ile çalıştırılmalıdır." >&2 # Hata mesajını stderr'e yaz
    exit 1
fi

echo "Yükleme başlıyor..."
# Modern Debian/Ubuntu paket yöneticisi: apt
apt install -y htop # -y ile onayı otomatik ver

# Betiğin başarıyla tamamlandığını belirt
exit 0
```

```bash
eaydin@dixon ~/calisma/bash $ ./yukle.sh 
Hata: Bu betik root yetkisi ile çalıştırılmalıdır.
```

Yukarıdaki satırların üzerinden geçecek olursak:

*   `[[ "$EUID" -ne 0 ]]`: Betiği çalıştıran kullanıcının etkili kullanıcı kimliğinin (Effective User ID) 0 (yani root) olup olmadığını kontrol eder. `[[ ... ]]` modern Bash test yapısıdır. `$EUID` değişkeni genellikle `$(id -u)`'dan daha pratiktir.
*   `>&2`: Hata mesajlarının standart çıktı (stdout) yerine standart hataya (stderr) yönlendirilmesi iyi bir pratiktir.
*   `exit 1`: Betiğin bir hata koduyla (genellikle 0 olmayan bir değer) sonlanmasını sağlar.
*   `apt install -y htop`: `apt-get` yerine modern `apt` komutu kullanılır. `-y` seçeneği, kurulum sırasında sorulacak onay sorularına otomatik olarak "evet" yanıtı verir.
*   `exit 0`: Betiğin başarıyla tamamlandığını belirtir.

Yukarıdaki programın nasıl sonuçlandığını (exit mesajını) almak için aşağıdaki yöntemi kullanabiliriz.

```bash
eaydin@dixon ~/calisma/bash $ echo $?
1
```

Buradaki **$?** bash için özel bir değişkendir ve "bir önceki programın hata mesajı"nı temsil eder. Yani programımız root yetkisiyle çalıştırılmadığı için **exit 1** ile çıkmış.

Şimdi programımızı root yetkisiyle çalıştıralım.

```bash
eaydin@dixon ~/calisma/bash $ sudo ./yukle.sh 
Yukleme basliyor...
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  htop
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/68,0 kB of archives.
After this operation, 188 kB of additional disk space will be used.
Selecting previously unselected package htop.
(Reading database ... 175141 files and directories currently installed.)
Preparing to unpack .../htop_1.0.2-3_amd64.deb ...
Unpacking htop (1.0.2-3) ...
Processing triggers for gnome-menus (3.10.1-0ubuntu2) ...
Processing triggers for desktop-file-utils (0.22-1ubuntu1) ...
Processing triggers for mime-support (3.54ubuntu1.1) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Setting up htop (1.0.2-3) ...
```

Programımızı tekrar çalıştırırsak, htop'un zaten yüklü olduğunu göreceğiz. Öyleyse programımız bunu da kontrol etsin.

Bir paketin kurulu olup olmadığını kontrol etmenin daha güvenilir yolu, paket yöneticisinin kendi sorgulama mekanizmasını kullanmaktır. `grep` ile filtrelemek, paket adının başka bir paketin açıklamasında geçmesi gibi durumlarda yanıltıcı olabilir.

*   **Debian/Ubuntu:** `dpkg -s <paket_adı>` komutunun çıkış kodunu kontrol edebiliriz. Paket kuruluysa komut 0 ile çıkar, kurulu değilse 0 olmayan bir kodla çıkar.
*   **RHEL/CentOS/Fedora:** `rpm -q <paket_adı>` komutunun çıkış kodunu kontrol edebiliriz. Paket kuruluysa 0, değilse 0 olmayan bir kodla çıkar.

```bash
#!/bin/bash
# yukle.sh dosya icerigi

# Root yetkisi kontrolü
if [[ "$EUID" -ne 0 ]]; then
    echo "Hata: Bu betik root yetkisi ile çalıştırılmalıdır." >&2
    exit 1
fi

PACKAGE_NAME="htop"

# htop zaten yuklu mu? (dpkg -s kullanarak)
# Çıktıyı ve hatayı /dev/null'a yönlendir (&>) ve sadece çıkış kodunu kontrol et ($?)
if dpkg -s "$PACKAGE_NAME" &> /dev/null; then
    echo "$PACKAGE_NAME zaten kurulu."
else
    echo "$PACKAGE_NAME kuruluyor..."
    apt install -y "$PACKAGE_NAME"
fi

exit 0
```

## Parametrik Kullanım

Programımız sadece htop için çalışıyor, eğer herhangi bir program için çalışmasını isteseydik, parametre almamız gerekirdi.

Bash ile gelen parametrelerin sayısını **$#** ile, gelen her parametreyi ise **$1 $2 $3 ...** şeklinde alabilirsiniz.

```bash
#!/bin/bash
# yukle.sh dosya icerigi

# Root yetkiniz var mi?
if [ $(id -u) -ne 0 ]; then
    echo "root yetkisi ile calistirilmali"
    exit 1
fi

# Parametre sayisi kontrol ediliyor
# $# değişkeni betiğe verilen argüman sayısını tutar
if [[ "$#" -lt 1 ]]; then
    echo "Hata: Lütfen kurulacak paket adını belirtin." >&2
    echo "Kullanım: $0 <paket_adı>" >&2 # $0 betiğin kendi adını verir
    exit 1
fi

# İlk argümanı bir değişkene ata (daha okunaklı)
PACKAGE_NAME="$1" # Değişkenleri çift tırnak içinde kullanmak önemlidir

# program zaten yuklu mu? (dpkg -s kullanarak)
if dpkg -s "$PACKAGE_NAME" &> /dev/null; then
    echo "$PACKAGE_NAME zaten kurulu."
else
    echo "$PACKAGE_NAME kuruluyor..."
    apt install -y "$PACKAGE_NAME"
fi

exit 0
```

```bash
eaydin@dixon ~/calisma/bash $ sudo ./yukle.sh 
Parametre vermediniz
Kullanım: yukle.sh program-adi
eaydin@dixon ~/calisma/bash $ echo $?
1
eaydin@dixon ~/calisma/bash $ sudo ./yukle.sh htop
htop zaten yuklu
eaydin@dixon ~/calisma/bash $ sudo ./yukle.sh atop
Yukleme basliyor...
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  atop
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/102 kB of archives.
After this operation, 281 kB of additional disk space will be used.
Selecting previously unselected package atop.
(Reading database ... 175134 files and directories currently installed.)
Preparing to unpack .../archives/atop_1.26-2_amd64.deb ...
Unpacking atop (1.26-2) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Processing triggers for ureadahead (0.100.0-16) ...
Setting up atop (1.26-2) ...
Processing triggers for ureadahead (0.100.0-16) ...
```

## Dosyanın Var Olup Olmadığını Anlama

Programımız fena iş çıkarmasa da, sadece Debian sistemler üzerinde çalışacak şekilde yazıldı, ve bunun kontrolünü hiç yapmıyor. Öte yandan sistemimizin Debian olup olmadığını anlamak için `/etc/debian_version` dosyasının var olup olmadığına bakmamız yeterli. Bunun için bash aşağıdaki gibi bir kontrol mekanizması sunuyor.

```bash
if [ -f /etc/debian_version ]; then
    echo "Bu sistem bir Debian türevi"
fi
```

Veya aşağıdaki yöntemle bir dosyanın var olmadığını kontrol edebilirdik.

```bash
if [ ! -f /etc/redhat-release ]; then
    echo "Bu sistem bir Red Hat türevi değil"
fi
```

Bu kontrolleri kullanarak, sistem Debian ise dpkg ve apt-get komutlarını, Red Hat ise rpm ve yum komutlarını kullanabiliriz. Scriptimizin son hali aşağıdaki gibi.

```bash
eaydin@dixon ~/calisma/bash $ cat yukle.sh 
#!/bin/bash
# yukle.sh dosya icerigi

# Root yetkiniz var mi?
if [ $(id -u) -ne 0 ]; then
    echo "root yetkisi ile calistirilmali"
    exit 1
fi

# Parametre sayisi kontrol ediliyor
if [ $# -lt 1 ]; then
    echo "Parametre vermediniz"
    echo "Kullanım: yukle.sh program-adi"
    exit 1
fi

# Eger sistem Debian/Ubuntu ise
if [[ -f /etc/debian_version ]]; then
    echo "Debian/Ubuntu tabanlı sistem algılandı."
    # program zaten yuklu mu?
    if dpkg -s "$PACKAGE_NAME" &> /dev/null; then
        echo "$PACKAGE_NAME zaten kurulu."
    else
         echo "$PACKAGE_NAME kuruluyor (apt)..."
         apt update # Kurulumdan önce listeyi güncellemek iyi bir pratik
         apt install -y "$PACKAGE_NAME"
    fi
# Eger sistem RHEL/CentOS/Fedora ise
elif [[ -f /etc/redhat-release ]]; then
    echo "Red Hat tabanlı sistem algılandı."
    # program zaten yuklu mu?
    if rpm -q "$PACKAGE_NAME" &> /dev/null; then
        echo "$PACKAGE_NAME zaten kurulu."
    else
        echo "$PACKAGE_NAME kuruluyor (dnf)..."
        dnf install -y "$PACKAGE_NAME"
    fi
# Tanımlanamayan sistem
else
    echo "Hata: Desteklenmeyen veya tanımlanamayan Linux dağıtımı." >&2
    exit 1
fi

exit 0
```
(Not: Dağıtımı belirlemek için `/etc/os-release` dosyasını kontrol etmek daha modern bir yöntemdir, ancak bu örnek için dosya varlığı kontrolü yeterlidir.)

## Fonksiyonlar

Yukarıdaki yükleme işlemlerini işletim sistemi özelinde fonksiyonlara taşırsak ileride geliştirmesi daha rahat olabilir.

Fonksiyonlar hakkında karıştırılmaması gereken, argümanları da tıpkı programın kendisi gibi **$1 $2 $3 ...** şeklinde ifade edilir. Dolayısıyla fonksiyon içerisinde kullanacağınız **$1** ifadesi, fonksiyona gönderilen 1. parametre olur, programa gönderilen değil. Fonksiyon içinden, programa gönderilen parametrelere doğrudan erişemezsiniz, ancak dolaylı olarak erişebilirsiniz.

Yükleme işlemimizi fonksiyonlara bölüp programın son halini görelim.

```bash
#!/bin/bash
# yukle.sh dosya icerigi

# Debian/Ubuntu Yukleme Fonksiyonu
debian_yukle() {
    local pkg_name="$1" # Fonksiyon içi yerel değişken kullan
    # program zaten yuklu mu?
    if dpkg -s "$pkg_name" &> /dev/null; then
        echo "$pkg_name zaten kurulu."
    else
         echo "$pkg_name kuruluyor (apt)..."
         apt update 
         apt install -y "$pkg_name"
    fi
    # Fonksiyonun çıkış kodunu apt install'dan al (başarı/hata)
    return $? 
}

# RHEL/CentOS/Fedora Yukleme Fonksiyonu
redhat_yukle() {
    local pkg_name="$1"
    # program zaten yuklu mu?
    if rpm -q "$pkg_name" &> /dev/null; then
        echo "$pkg_name zaten kurulu."
    else
        echo "$pkg_name kuruluyor (dnf)..."
        dnf install -y "$pkg_name"
    fi
    return $?
}

# === Ana Betik Mantığı ===

# Root yetkisi kontrolü
if [[ "$EUID" -ne 0 ]]; then
    echo "Hata: Bu betik root yetkisi ile çalıştırılmalıdır." >&2
    exit 1
fi

# Parametre sayısı kontrolü
if [[ "$#" -lt 1 ]]; then
    echo "Hata: Lütfen kurulacak paket adını belirtin." >&2
    echo "Kullanım: $0 <paket_adı>" >&2
    exit 1
fi

# Kurulacak paketi değişkene ata
PACKAGE_TO_INSTALL="$1"

# Dağıtımı kontrol et ve ilgili fonksiyonu çağır
if [[ -f /etc/debian_version ]]; then
    echo "Debian/Ubuntu tabanlı sistem algılandı."
    debian_yukle "$PACKAGE_TO_INSTALL" # Değişkeni tırnak içinde kullan
    exit_code=$? # Fonksiyonun çıkış kodunu yakala

elif [[ -f /etc/redhat-release ]]; then
    echo "Red Hat tabanlı sistem algılandı."
    redhat_yukle "$PACKAGE_TO_INSTALL" # Değişkeni tırnak içinde kullan
    exit_code=$?

else
    echo "Hata: Desteklenmeyen veya tanımlanamayan Linux dağıtımı." >&2
    exit 1
fi

# Fonksiyonun çıkış koduna göre betiğin çıkış kodunu ayarla
exit $exit_code
```

Bu örnekler, Bash betiklerinin temellerini (değişkenler, koşullar, fonksiyonlar, komut çalıştırma, çıkış kodları) göstermektedir. Daha karmaşık görevler için döngüler, dizi değişkenler, metin işleme gibi konular [Döngüler ve Diğer Kontrol Yöntemleri](doengueler-ve-diger-kontrol-yoentemleri.md) bölümünde ele alınacaktır.
