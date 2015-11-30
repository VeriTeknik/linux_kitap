# Döngüler ve Diğer Kontrol Yöntemleri

Bash programlamanın temellerini anladıktan sonra, döngüler ve yaygın kullanılan kontrol yöntemlerini inceleyebiliriz.

## for

Aşağıdaki script ile içinde birkaç dosya bulunan dizinin içerisinde for döngüsü ile dosyalar listeleniyor.

```bash
eaydin@dixon ~/calisma/bash $ ls
deneme_dosyasi  for.sh  logo.png  yukle.sh
```
```bash
eaydin@dixon ~/calisma/bash $ cat for.sh 
#!/bin/bash

for i in $( ls ); do
    echo Dosya: $i
done
```
```bash
eaydin@dixon ~/calisma/bash $ ./for.sh 
Dosya: deneme_dosyasi
Dosya: for.sh
Dosya: logo.png
Dosya: yukle.sh
```

## while

Aşağıda hem değişken değerlerinin nasıl değiştirildiğini, hem de while döngüsünün genel yapısını görebilirsiniz.

```bash
eaydin@dixon ~/calisma/bash $ cat while.sh 
#!/bin/bash

SAYAC=0
while [ $SAYAC -lt 10 ]; do
    echo Sayac durumu: $SAYAC
    let SAYAC=SAYAC+1
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

## case

Çoklu kontroller için kullanılır. Aşağıdaki örnekte ```case``` kullanımı dışında ```echo -n``` ile **new line** kullanmadan echo yapmayı, ```read``` ile standart girdiden seçenek okumayı öğrenebilirsiniz.

```bash
eaydin@dixon ~/calisma/bash $ cat case.sh 
#!/bin/bash

echo -n "İşleme devam etmek istiyor musunuz? [yes/evet veya no/hayir]: "
read cevap
case $cevap in
    [yY] | [eE] | [yY][eE][sS] | [eE][vV][eE][tT] )
        echo "İşlem devam ediyor..."
        ;;
    [nN] | [hH] | [nN][oO] | [hH][aA][yY][ıiI][rR] )
        echo "İşlem iptal edildi"
        ;;
    *) echo "Seçeneğiniz anlaşılmadı"
        ;;
esac
```
```bash
eaydin@dixon ~/calisma/bash $ ./case.sh 
İşleme devam etmek istiyor musunuz? [yes/evet veya no/hayir]: eVEt
İşlem devam ediyor...
eaydin@dixon ~/calisma/bash $ ./case.sh 
İşleme devam etmek istiyor musunuz? [yes/evet veya no/hayir]: n
İşlem iptal edildi
eaydin@dixon ~/calisma/bash $ ./case.sh 
İşleme devam etmek istiyor musunuz? [yes/evet veya no/hayir]: asd
Seçeneğiniz anlaşılmadı
```