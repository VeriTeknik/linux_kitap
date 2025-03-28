# Menüler

Yazdığınız programların kullanıcı ile etkileşimini sağlayan çeşitli menüler oluşturmak isteyebilirsiniz. Aşağıda iki yöntemini sunacağız.

## `select` Komutu

Bash kabuğunun kendi içinde bulunan `select` komutu, kullanıcıya numaralandırılmış bir seçenek listesi sunar ve seçilen öğeye göre işlem yapmanızı sağlar. `for` döngüsüne benzer bir yapısı vardır.

```bash
#!/bin/bash

# Boşluk içeren seçenekler için tırnak kullanmak daha güvenli olabilir
# veya bir dizi (array) kullanılabilir:
# SECENEKLER=("Disk Durumu" Yardım Çıkış)
# select i in "${SECENEKLER[@]}"; do ...
SECENEKLER="Disk-Durumu Yardim Cikis"

# PS3 değişkeni select'in göstereceği istemi (prompt) ayarlar
PS3="Lütfen bir seçenek girin (1-3): " 

echo "Bir işlem seçin:"
select i in $SECENEKLER; do
    # Modern test [[ ... ]] kullanımı
    if [[ "$i" == "Cikis" ]]; then
        echo "Çıkılıyor..."
        break # Döngüden çık
    elif [[ "$i" == "Yardim" ]]; then
        echo "Yardım menüsü gösteriliyor..."
        # Burada yardım metni gösterilebilir
    elif [[ "$i" == "Disk-Durumu" ]]; then
        echo "Disk durumu:"
        df -h
    else
        # Kullanıcı geçersiz bir numara girerse $i boş olur
        # $REPLY değişkeni kullanıcının girdiği ham değeri içerir
        echo "Geçersiz seçenek: $REPLY"
    fi
done
```

Örnek Çalıştırma:
```bash
$ ./select.sh 
Bir işlem seçin:
1) Disk-Durumu
2) Yardim
3) Cikis
Lütfen bir seçenek girin (1-3): 1
Disk durumu:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       346G   32G  296G  10% /
udev            3,8G     0  3,8G   0% /dev
tmpfs           777M  1,5M  775M   1% /run
/dev/sda6       374G  150G  225G  40% /media/backups
Lütfen bir seçenek girin (1-3): 4
Geçersiz seçenek: 4
Lütfen bir seçenek girin (1-3): 3
Çıkılıyor...
$ 
```

## `whiptail` (ve `dialog`)

`whiptail`, `newt` kütüphanesini kullanarak terminalde metin tabanlı grafiksel diyalog kutuları (mesaj kutuları, giriş kutuları, menüler vb.) oluşturmanızı sağlayan bir programdır. Kurulum betiklerinde veya basit TUI (Text User Interface) uygulamalarında kullanıcı etkileşimi için sıkça kullanılır.

Benzer bir araç olan `dialog`, `ncurses` kütüphanesini kullanır ve genellikle `whiptail` ile çok benzer komut satırı seçeneklerine sahiptir. Sisteminizde hangisinin kurulu olduğuna bağlı olarak birini tercih edebilirsiniz (`sudo apt install whiptail` veya `sudo apt install dialog` / `sudo dnf install newt` veya `sudo dnf install dialog`).

Aşağıda `whiptail` ile bazı yaygın diyalog kutusu örnekleri verilmiştir.

**Not:** `whiptail` genellikle kullanıcı seçimini veya girdisini standart hataya (stderr - FD 2) yazar. Bunu bir değişkene atamak için, standart hata ile standart çıktının yerini değiştirmek gerekir. Örneklerdeki `3>&1 1>&2 2>&3` ifadesi bu işlemi yapar:
1.  `3>&1`: Geçici olarak FD 3'ü orijinal standart çıktıya (FD 1) yönlendirir.
2.  `1>&2`: Standart çıktıyı (FD 1) standart hataya (FD 2) yönlendirir (whiptail'in arayüzü buraya gider).
3.  `2>&3`: Standart hatayı (FD 2 - whiptail'in çıktısı) orijinal standart çıktıya (artık FD 3'te saklanan) yönlendirir.
Bu sayede `$(...)` komut yakalama mekanizması, `whiptail`'in stderr'e yazdığı sonucu yakalayabilir.

### msgbox

Aşağıdaki gibi bir komut ile görsel bir uyarı sağlayabilirsiniz.
```bash
whiptail --title "Bilgi" --msgbox "Bu bir uyarı mesajıdır." 10 50 
```
Komutun sonundaki rakamlar (10 50), pencerenin yüksekliğini (satır) ve genişliğini (karakter) belirtir.

Örneğin **Ok** yerine **Tamam** yazmasını sağlayabilirdik:
```bash
whiptail --msgbox "Bu bir uyarı mesajıdır." 10 50 --ok-button "Tamam"
```

### yes/no

Programınızın basit Evet/Hayır soruları sormasını sağlayabilirsiniz. `whiptail --yesno` komutu, kullanıcı "Evet" seçerse 0, "Hayır" seçerse 1 çıkış koduyla sonlanır. `$?` ile bu kontrol edilebilir.
```bash
#!/bin/bash

if whiptail --title "Anket" --yesno "GNU/Linux'u özgür buluyor musunuz?" \
   --yes-button Evet --no-button Hayır 10 60; then
    whiptail --msgbox "Çok haklısınız!" --ok-button Tamam 10 40
else
    whiptail --msgbox "Yanılıyor olabilirsiniz?" --ok-button Tamam 10 40
fi
```

### inputbox

Kullanıcıdan metin girdisi almak için `inputbox` kullanılır. Girilen metin standart hataya yazdırılır.
```bash
#!/bin/bash

# Mevcut hostname'i al
mevcut_hostname=$(hostname)

# Kullanıcıdan yeni hostname'i iste, sonucu YENI_HOSTNAME değişkenine ata
YENI_HOSTNAME=$(whiptail --inputbox "Yeni Hostname Giriniz:" \ 
8 60 "$mevcut_hostname" --title "Hostname Değiştirme" --ok-button Tamam \
--cancel-button İptal 3>&1 1>&2 2>&3)

# Kullanıcının Tamam mı İptal mi seçtiğini kontrol et ($? çıkış kodudur)
exit_status=$?

# Eğer Tamam seçildiyse (çıkış kodu 0) ve girilen değer boş değilse
if [[ $exit_status -eq 0 ]] && [[ -n "$YENI_HOSTNAME" ]]; then
    echo "Hostname '$YENI_HOSTNAME' olarak değiştiriliyor..."
    # sudo hostnamectl set-hostname "$YENI_HOSTNAME" # Modern systemd yöntemi
    # veya sadece geçici olarak: sudo hostname "$YENI_HOSTNAME"
else
    echo "İşlem iptal edildi veya boş değer girildi."
fi
```

### menu

Kullanıcıya bir listeden tek bir seçim yapma imkanı sunar. Seçilen öğenin "etiketi" standart hataya yazdırılır.
```bash
#!/bin/bash

# Menü seçenekleri: "Etiket" "Açıklama" şeklinde çiftler halinde
SECIM=$(whiptail --title "Programlama Menüsü" \
--menu "Favori dilinizi seçin" 17 60 5 \
"Python" "Guido van Rossum tarafından geliştirildi" \
"C"      "Dennis M. Ritchie tarafından geliştirildi" \
"Perl"   "Larry Wall tarafından geliştirildi" \
"Bash"   "Brian Fox tarafından geliştirildi" \
"Rust"   "Mozilla tarafından geliştirildi" 3>&1 1>&2 2>&3)

exit_status=$?
if [[ $exit_status -eq 0 ]]; then
    echo "Seçtiğiniz dil: $SECIM"
else
    echo "Dil seçmediniz veya iptal ettiniz."
fi
```

### checklist

Kullanıcıya bir listeden birden fazla seçim yapma (işaretleme) imkanı sunar. Seçilen öğelerin etiketleri, tırnak içinde ve boşlukla ayrılarak standart hataya yazdırılır.
```bash
#!/bin/bash

# Menü seçenekleri: "Etiket" "Açıklama" "ON/OFF" (başlangıç durumu)
SECIMLER=$(whiptail --title "Programlama Menüsü" --checklist \
"Kullandığınız Dilleri Seçin (Boşluk ile işaretleyin)" 15 60 5 \
"Python" "Guido van Rossum" ON \
"C"      "Dennis M. Ritchie" OFF \
"Perl"   "Larry Wall" OFF \
"Bash"   "Brian Fox" ON \
"Java"   "James Gosling" OFF 3>&1 1>&2 2>&3)

exit_status=$?
if [[ $exit_status -eq 0 ]]; then
    echo "Seçtiğiniz diller: $SECIMLER"
else
    echo "Dil seçmediniz veya iptal ettiniz."
fi
```

### radiolist

`checklist`'e benzer, ancak kullanıcıya listeden sadece *bir* tane seçim yapma imkanı sunar. Seçilen öğenin etiketi standart hataya yazdırılır.
```bash
#!/bin/bash

# Menü seçenekleri: "Etiket" "Açıklama" "ON/OFF" (sadece biri ON olabilir)
SECIM=$(whiptail --title "Programlama Menüsü" --radiolist \
"En çok kullandığınız dili seçin" 15 60 5 \
"Python" "Guido van Rossum" OFF \
"C"      "Dennis M. Ritchie" ON \
"Perl"   "Larry Wall" OFF \
"Bash"   "Brian Fox" OFF \
"Go"     "Google" OFF 3>&1 1>&2 2>&3)

exit_status=$?
if [[ $exit_status -eq 0 ]]; then
    echo "En çok kullandığınız dil: $SECIM"
else
    echo "Dil seçmediniz veya iptal ettiniz."
fi
