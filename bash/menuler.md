# Menüler

Yazdığınız programların kullanıcı ile etkileşimini sağlayan çeşitli menüler oluşturmak isteyebilirsiniz. Aşağıda iki yöntemini sunacağız.

##select

En temel yöntem select komutunu kullanmaktır. for loop kullanır gibi kullanıcı etkileşimi oluşturmanızı sağlar. Temel yapsını paylaşıyoruz. Bunun üzerine kendi programınızı geliştirip fonksiyonlarla süsleyebilirsiniz.


```bash
eaydin@dixon ~/calisma/bash $ cat select.sh 
#!/bin/bash

SECENEKLER="Disk-Durumu Yardim Cikis"
select i in $SECENEKLER; do
    if [ "$i" = "Cikis" ]; then
        exit
    elif [ "$i" = "Yardim" ]; then
        echo "Yardim menusu"
    elif [ "$i" = "Disk-Durumu" ]; then
        df -h
    else
        echo "Secenek Anlasilmadi?"
    fi
done
eaydin@dixon ~/calisma/bash $ ./select.sh 
1) Disk-Durumu
2) Yardim
3) Cikis
#? 1
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       346G   32G  296G  10% /
none            4,0K     0  4,0K   0% /sys/fs/cgroup
udev            3,8G  4,0K  3,8G   1% /dev
tmpfs           777M  1,5M  775M   1% /run
none            5,0M     0  5,0M   0% /run/lock
none            3,8G  114M  3,7G   3% /run/shm
none            100M   20K  100M   1% /run/user
/dev/sda6       374G  150G  225G  40% /media/backups
#? 3
eaydin@dixon ~/calisma/bash $
```