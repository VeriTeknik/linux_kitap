# BASH Programlama

GNU/Linux üzerinde bugün en yaygın kullanılan kabuğun **bash** (Bourne Again SHell) olduğunu söyleyebiliriz. Bu bölümde bash kullanarak akıllı işlemleri nasıl yapacağımızı, temel kontrolleri, hatta basit bazı yükleme scriptleri geliştirmeyi göreceğiz.

## Temel Yapısı

Bash scriptlerinin temel yapısı, başında yorumlayıcının belirtildiği, ardından gerekli komutların sıralandığı dosyalardır. Aşağıdaki scripti ```yukle.sh``` ismiyle kaydedip çalıştıralım.

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

if [ $(id -u) -ne 0 ]; then
    echo "root yetkisi ile calistirilmali"
    exit 1
fi
echo "Yukleme basliyor..."
apt-get install htop
exit 0
eaydin@dixon ~/calisma/bash $ ./yukle.sh 
root yetkisi ile calistirilmali
```

Yukarıdaki satırların üzerinden geçecek olursak:

* id -u komutu ile mevcut kullanıcının id'si alınıyor. Eğer bu değer 0'a eşit değilse (-ne : not equal) if şartı sağlanmış oluyor. Linux üzerinde sadece root kullanıcısının id'si 0'dır.
* Eğer root kullanıcısı değilse, exit 1 ile programdan çıkıyoruz. Eğer root kullanıcısıysak, exit 0 ile çıkıyoruz. Buradaki 1 ve 0 değerleri exit mesajlarıdır. Linux'ta programlar başarıyla işlerini yerine getirmişse 0 ile çıkarlar, bir hatayla çıkarlarsa 1 veya farklı değerler alırlar.


Yukarıdaki programın nasıl sonuçlandığını (exit mesajını) almak için aşağıdaki yöntemi kullanabiliriz.

```bash
eaydin@dixon ~/calisma/bash $ echo $?
1
```

Buradaki **$?** bash için özel bir değişkendir ve "bir önceki programın hata mesajı"nı temsil eder. Yani programımız root yetkisiyle çalıştırılmadığı için **exit 1** ile çıkmış.