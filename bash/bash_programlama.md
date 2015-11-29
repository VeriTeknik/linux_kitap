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

