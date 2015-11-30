# BASH Programlama

GNU/Linux üzerinde bugün en yaygın kullanılan kabuğun **bash** (Bourne Again SHell) olduğunu söyleyebiliriz. Bu bölümde bash kullanarak akıllı işlemleri nasıl yapacağımızı, temel kontrolleri, hatta basit bazı yükleme scriptleri geliştirmeyi göreceğiz.

## Temel Kontroller

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

if [ $(id -u) -ne 0 ]; then
    echo "root yetkisi ile calistirilmali"
    exit 1
fi
echo "Yukleme basliyor..."
apt-get install htop
exit 0
```
```bash
eaydin@dixon ~/calisma/bash $ ./yukle.sh 
root yetkisi ile calistirilmali
```

Yukarıdaki satırların üzerinden geçecek olursak:

* id -u komutu ile mevcut kullanıcının id'si alınıyor. Eğer bu değer 0'a eşit değilse (```-ne``` : not equal) if şartı sağlanmış oluyor. Linux üzerinde sadece root kullanıcısının id'si 0'dır.
* Eğer root kullanıcısı değilse, exit 1 ile programdan çıkıyoruz. Eğer root kullanıcısıysak, exit 0 ile çıkıyoruz. Buradaki 1 ve 0 değerleri exit mesajlarıdır. Linux'ta programlar başarıyla işlerini yerine getirmişse 0 ile çıkarlar, bir hatayla çıkarlarsa 1 veya farklı değerler alırlar.


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

Kullanacağımız yöntemlerden birisi, dpkg ile yüklü paketlerimizi inceleyip, sonuçları grep'lemek olacaktır.

```bash
eaydin@dixon ~/calisma/bash $ dpkg -l | grep htop
ii  htop    1.0.2-3      amd64        interactive processes viewer
eaydin@dixon ~/calisma/bash $ echo $?
0
eaydin@dixon ~/calisma/bash $ dpkg -l | grep olmayanpaketismi
eaydin@dixon ~/calisma/bash $ echo $?
1
```

Yukarıdan, grep'in bir sonuç yakaladığında **0** ile exit verdiğini görebilirsiniz. Öyleyse bu bilgiyi kullanabiliriz.

```bash
#!/bin/bash
# yukle.sh dosya icerigi

# Root yetkiniz var mi?
if [ $(id -u) -ne 0 ]; then
    echo "root yetkisi ile calistirilmali"
    exit 1
fi

# htop zaten yuklu mu?
dpkg -l | grep htop > /dev/null
if [ $? -eq 1 ]; then
    echo "Yukleme basliyor..."
    apt-get install htop
else
    echo "htop zaten yuklu"
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
if [ $# -lt 1 ]; then
    echo "Parametre vermediniz"
    echo "Kullanım: yukle.sh program-adi"
    exit 1
fi

# program zaten yuklu mu?
dpkg -l | grep $1 > /dev/null
if [ $? -eq 1 ]; then
    echo "Yukleme basliyor..."
    apt-get install $1
else
    echo "$1 zaten yuklu"
fi
    	
exit 0
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

Programımız fena iş çıkarmasa da, sadece Debian sistemler üzerinde çalışacak şekilde yazıldı, ve bunun kontrolünü hiç yapmıyor. Öte yandan sistemimizin Debian olup olmadığını anlamak için ```/etc/debian_version``` dosyasının var olup olmadığına bakmamız yeterli. Bunun için bash aşağıdaki gibi bir kontrol mekanizması sunuyor.

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

# Eger sistem Debian ise
if [ -f /etc/debian_version ]; then
   
    # program zaten yuklu mu?
    dpkg -l | grep $1 > /dev/null
    if [ $? -eq 1 ]; then
         echo "Yukleme basliyor..."
         apt-get install $1
    else
        echo "$1 zaten yuklu"
    fi
# Eger sistem Red Hat ise
elif [ -f /etc/redhat-release ]; then
    # program zaten yuklu mu?
    rpm -qa | grep $1 > /dev/null
    if [ $? -eq 1 ]; then
        echo "Yukleme basliyor..."
        yum install $1
    else
        echo "$1 zaten yuklu"
    fi
# Tanimadigimiz sistem
else
    echo "Tanimadigimiz bir sistem?"
    exit 1
fi
	
exit 0
```

## Fonksiyonlar

Yukarıdaki yükleme işlemlerini işletim sistemi özelinde fonksiyonlara taşırsak ileride geliştirmesi daha rahat olabilir.

Fonksiyonlar hakkında karıştırılmaması gereken, argümanları da tıpkı programın kendisi gibi **$1 $2 $3 ...** şeklinde ifade edilir. Dolayısıyla fonksiyon içerisinde kullanacağınız **$1** ifadesi, fonksiyona gönderilen 1. parametre olur, programa gönderilen değil. Fonksiyon içinden, programa gönderilen parametrelere doğrudan erişemezsiniz, ancak dolaylı olarak erişebilirsiniz.

Yükleme işlemimizi fonksiyonlara bölüp programın son halini görelim.

```bash
#!/bin/bash
# yukle.sh dosya icerigi

# Debian Yukleme Fonksiyonu
debian_yukle () {
    # program zaten yuklu mu?
    dpkg -l | grep $1 > /dev/null
    if [ $? -eq 1 ]; then
         echo "Yukleme basliyor..."
         apt-get install $1
    else
        echo "$1 zaten yuklu"
    fi

    return 0
}

redhat_yukle () {
    # program zaten yuklu mu?
    rpm -qa | grep $1 > /dev/null
    if [ $? -eq 1 ]; then
        echo "Yukleme basliyor..."
        yum install $1
    else
        echo "$1 zaten yuklu"
    fi

    return 0
}

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

# Eger sistem Debian ise
if [ -f /etc/debian_version ]; then
    debian_yukle $1

# Eger sistem Red Hat ise
elif [ -f /etc/redhat-release ]; then
    redhat_yukle $1

# Tanimadigimiz sistem
else
    echo "Tanimadigimiz bir sistem?"
    exit 1
fi

exit 0
```