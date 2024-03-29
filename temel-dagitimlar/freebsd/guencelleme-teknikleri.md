# Güncelleme Teknikleri

Diğer dağıtımlarda olduğu gibi FreeBSD üzerinde de birden çok paket ve dağıtım güncelleme yöntemi vardır. Ancak bu yöntemler diğer dağıtyımlardaki gibi sadece bir yazılım üzerinde yoğunlaşmamıştır ve neredeyse hepsi aynı popülerliktedir.

Çoğunlukla bir update yönteminin çalışmadığı noktada diğer update yöntemi çalışacak ya da hangi update yöntemi ile başlanıldıysa onunla devam etmek doğru olacaktır.

## Major Güncellemeler

FreeBSD üzerinden Major (X.0) güncellemeleri yapılabilmektedir, portların uyumluluğu gözden geçirildikten sonra bir sorun yaşamadan büyük update gerçekleştirebilirsiniz. FreeBSD üzerinde tüm kodları yeniden derlemek gibi bir opsiyonunuz olsa dahi, 6.3 versiyonundan bu yana binary güncellemeleri de sunulmakta, eşit performansta hızlı bir güncelleme opsiyonu sunulmaktadır.

Major sürüm güncellemesinden sonra tüm paketlerin sağlıklı bir şekilde yeni sürüme port edilebilmesi için portupgrade paketine ihtiyaç duyulmaktadır, eğer sistemde yüklü değilse portlardan kolaylıkla yükleyebilirsiniz:

```bash
which portupgrade
```

sorusuna aşağıdaki gibi bir cevap alıyorsanız, portupgrade yüklüdür:

```bash
#/usr/local/sbin/portupgrade
```

cevap gelmiyorsa yüklemeyi yapınız:

```bash
cd /usr/ports/ports-mgmt/portupgrade
make install clean
```

Major güncellemeyi başlatmak için FreeBSD sitesinden en son kararlı sürüm versiyonuna bakınız, daha sonra şu komutu yazınız:

```bash
freebsd-update upgrade -r 10.2-RELEASE
#Fetching metadata signature for 10.1-RELEASE from update5.freebsd.org... done.
#Fetching metadata index... done.
#Fetching 1 metadata patches. done.
#Applying metadata patches... done.
#Inspecting system... done.
#
#The following components of FreeBSD seem to be installed:
#kernel/generic world/base world/doc world/lib32
#
#The following components of FreeBSD do not seem to be installed:
#src/src world/games
#
#Does this look reasonable (y/n)? y
#.
#.
/usr/sbin/freebsd-update install
#Installing updates...
#Kernel updates have been installed.  Please reboot and run
#"/usr/sbin/freebsd-update install" again to finish installing updates.
reboot
```

Yükleme sonrası sistemin yeniden başlatılması istenmektedir, yeniden başlattıktan sonra aynı komutu tekrar çalıştırmalısınız:

```bash
/usr/sbin/freebsd-update install
#Installing updates... done.
```

Geçiş yapılan son versiyonu görüntülemek için:

```bash
uname -a
# FreeBSD buildtest.veriteknik.com 10.2-RELEASE-p7 FreeBSD 10.2-RELEASE-p7 #0: Mon Nov  2 14:19:39 UTC 2015     root@amd64-builder.daemonology.net:/usr/obj/usr/src/sys/GENERIC  amd64
```

## Minör Güncellemeler

freebsd-update install komutu ile de indirilen paketlerin güncellemesi sağlanır, bu işlemden sonra ise daha önceki sürüm ile uyumlu olan portların güncellemesi yapılmalıdır. Bu işlem bir kaç saat sürecektir.

```bash
rm /var/db/pkg/pkgdb.db /usr/ports/INDEX-*.db
portupgrade -af
```

Bu işlemden sonra tekrar freebsd-update çalıştırılır ve sisten yeniden başlatılır.

### Portsnap Yöntemi

Portsnap yöntemi ile de tüm portları güncellemeniz mümkündür. Eğer yüklü değilse /var/db/portsnap içerisinden yüklenebilir. Yükleme tamamlandıktan sonra üç adımda tüm paketleri güncelleyebilirsiniz.

```bash
portsnap fetch
portsnap extract
```

Bu iki komutu ilk defa kullandıktan sonra, daha sonra şu şekilde çalıştırabilirsiniz:

```bash
portsnap fetch
portsnap update
```
