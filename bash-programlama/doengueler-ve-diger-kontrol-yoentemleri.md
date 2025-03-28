# Döngüler ve Diğer Kontrol Yöntemleri

Bash programlamanın temellerini anladıktan sonra, döngüler ve yaygın kullanılan kontrol yöntemlerini inceleyebiliriz.

## `for` Döngüsü

`for` döngüsü, bir listedeki (kelimeler, dosya adları vb.) her öğe için belirli komutları tekrarlamak amacıyla kullanılır.

**Örnek 1: Dosyalar Üzerinde Döngü (Globbing ile - Önerilen Yöntem)**

Mevcut dizindeki tüm `.txt` dosyalarını listelemek için:
```bash
#!/bin/bash

echo "Bulunan .txt dosyaları:"
# '*' glob karakteri mevcut dizindeki tüm .txt dosyalarıyla eşleşir
# Dosya adlarında boşluk veya özel karakter varsa, tırnak içinde kullanmak önemlidir
for dosya in *.txt; do
    # Dosyanın var olup olmadığını kontrol et (eğer hiç .txt dosyası yoksa glob kendisiyle eşleşir)
    if [[ -f "$dosya" ]]; then 
        echo "İşlenen dosya: $dosya"
    fi
done
```
**Uyarı:** `for i in $(ls)` gibi `ls` komutunun çıktısını ayrıştırmak, dosya adlarında boşluk veya özel karakterler olduğunda sorunlara yol açar ve **kesinlikle önerilmez**. Dosyalar üzerinde işlem yapmak için globbing (`*`, `?`, `[...]`) veya `find` komutu ile `while read` döngüsü kullanılmalıdır.

**Örnek 2: Sayı Aralığında Döngü**
```bash
#!/bin/bash

echo "1'den 5'e kadar sayılar:"
for i in {1..5}; do
    echo "Sayı: $i"
done
```

## `while` Döngüsü

`while` döngüsü, belirli bir koşul doğru (`true`, çıkış kodu 0) olduğu sürece komutları tekrarlar. Koşul genellikle `[[ ... ]]` test ifadesiyle kontrol edilir.

Aşağıda 0'dan 9'a kadar sayan basit bir `while` döngüsü örneği ve modern Bash aritmetik/test sözdizimi gösterilmiştir:
```bash
#!/bin/bash

SAYAC=0
# [[ ... ]] modern test yapısı, -lt sayısal karşılaştırma (less than)
while [[ "$SAYAC" -lt 10 ]]; do
    echo "Sayaç durumu: $SAYAC"
    # ((...)) modern aritmetik yapısı
    ((SAYAC++)) 
    # Alternatifler: ((SAYAC = SAYAC + 1)) veya let SAYAC=SAYAC+1
done
```
```bash
eaydin@dixon ~/calisma/bash $ ./while.sh 
Sayac durumu: 0
Sayac durumu: 1
Sayac durumu: 2
Sayac durumu: 3
Sayac durumu: 4
Sayac durumu: 5
Sayac durumu: 6
Sayac durumu: 7
Sayac durumu: 8
Sayac durumu: 9
```

## `until` Döngüsü

`until` döngüsü, `while`'ın tersidir; belirtilen koşul yanlış (`false`, çıkış kodu 0 olmayan) olduğu sürece komutları tekrarlar. Koşul doğru olduğunda döngü sona erer.

```bash
#!/bin/bash

SAYAC=0
# Sayaç 5'e eşit olana kadar döngü devam eder
until [[ "$SAYAC" -eq 5 ]]; do
    echo "Until Sayaç: $SAYAC"
    ((SAYAC++))
done
```

## `case` İfadesi

Bir değişkenin değerini birden fazla kalıpla karşılaştırmak için `if`/`elif`/`else` yapısına alternatif olarak `case` ifadesi kullanılır. Özellikle menü seçenekleri veya komut satırı argümanlarını işlemek için kullanışlıdır.

Aşağıdaki örnekte `case` kullanımı, `echo -n` (yeni satır eklemeden yazdırma) ve `read -r` (kullanıcıdan güvenli girdi okuma) gösterilmektedir:
```bash
#!/bin/bash

# -n ile yeni satır eklemeden soruyu sor
echo -n "İşleme devam etmek istiyor musunuz? [E/H]: " 
# -r ile ters eğik çizgilerin yorumlanmasını engelle
read -r cevap 

# cevap değişkeninin değerini kontrol et
case "$cevap" in
    # Büyük/küçük E veya e ile başlayanlar
    [Ee]*) 
        echo "İşlem devam ediyor..."
        ;; 
    # Büyük/küçük H veya h ile başlayanlar
    [Hh]*) 
        echo "İşlem iptal edildi."
        ;;
    # Yukarıdaki kalıplara uymayan her şey (*)
    *) 
        echo "Geçersiz seçenek."
        ;;
esac # case ifadesini bitir
```
*   Her kalıp `)` ile biter.
*   Her kalıp bloğundaki komutlar `;;` ile sonlandırılır.
*   `*` kalıbı, diğer kalıpların hiçbiriyle eşleşmeyen durumları yakalar (varsayılan durum gibi).

Örnek Çalıştırma:
```bash
$ ./case.sh 
İşleme devam etmek istiyor musunuz? [E/H]: E
İşlem devam ediyor...

$ ./case.sh
İşleme devam etmek istiyor musunuz? [E/H]: h
İşlem iptal edildi.

$ ./case.sh
İşleme devam etmek istiyor musunuz? [E/H]: Belki
Geçersiz seçenek.
```
