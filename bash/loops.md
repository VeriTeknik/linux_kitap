# Döngüler ve Diğer Kontrol Yöntemleri

Bash programlamanın temellerini anladıktan sonra, döngüler ve yaygın kullanılan kontrol yöntemlerini inceleyebiliriz.

## for döngüsü

Aşağıdaki script ile içinde birkaç dosya bulunan dizinin içerisinde for döngüsü ile dosyalar listeleniyor.

```bash
eaydin@dixon ~/calisma/bash $ ls
deneme_dosyasi  for.sh  logo.png  yukle.sh
eaydin@dixon ~/calisma/bash $ cat for.sh 
#!/bin/bash

for i in $( ls ); do
    echo Dosya: $i
done
eaydin@dixon ~/calisma/bash $ ./for.sh 
Dosya: deneme_dosyasi
Dosya: for.sh
Dosya: logo.png
Dosya: yukle.sh
```

## while döngüsü

Aşağıda hem değişken değerlerinin nasıl değiştirildiğini, hem de while döngüsünün genel yapısını görebilirsiniz.

```bash
eaydin@dixon ~/calisma/bash $ cat while.sh 
#!/bin/bash

SAYAC=0
while [ $SAYAC -lt 10 ]; do
    echo Sayac durumu: $SAYAC
    let SAYAC=SAYAC+1
done
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