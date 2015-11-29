# Döngüler

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