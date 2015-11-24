# aptitude Paket Yöneticisi

Debian (ve Ubuntu) sistemlerde, Red Hat/CentOS için gördüğümüz rpm'in yum karşılığı olan paket yöneticisi aptitude isminde bir yazılımdır. Aptitude kullanıcı için tasarlanmış, başka programlar tarafından paketlerin yönetimi için apt-get ve apt-cache gibi programlar geliştirilmiştir. Ancak apt-get gibi programların yaygın kullanımlarından dolayı çoğu sistem yöneticisi aptitude programını neredeyse hiç kullanmaz.

apt-get, apt-cache araçları aptitude programı ile aynı repoları kullandığından, birini öğrenmek sorun teşkil etmez. Biz de bu bölümde yaygın kullanıma aşina olabilmek adına apt-get, apt-cache ve benzer araçlarla (kısaca apt) deb paket yönetimini göreceğiz.

## Paket Listelerini Güncellemek ve Aramak

apt paket listelerini yum'dan farklı bir mekanizmayla tutar. Her arama yaptığınızda sunuculara bağlanmaz. Böylelikle daha hızlı sonuç verir ancak her defasında sonuçlarınız güncel olmayabilir. Güncel sonuç almak için listelerinizi güncellemeniz gerekir.

apt ile paket listenizi güncellemek için **update** komutunu kullanabilirsiniz.

```bash
apt-get update
```

Artık paket listeleri içerisinde arama yapabiliriz. Örneğin adında **htop** geçen bir paket ismi ararsak şöyle arama yapabiliriz.

```bash
# apt-cache search htop
aha - ANSI color to HTML converter
htop - interactive processes viewer
libauthen-oath-perl - Perl module for OATH One Time Passwords
```

Sonuca bakacak olursak, içinde **htop** paketinin yer alması dışında, ilgisi olmayan paketler de geldi. Aslında apt-cache search komutu, paketlerin açıklamalarının içerisinde de arama yaptığı için. Örneğin **aha** paketinin açıklamasına bakarsak htop geçen satırı görebiliriz.

```bash
# apt-cache show aha|grep htop
 want to publish the output of ls --color=yes, git diff, ccal or htop
```

apt-cache search regular expression destekleyen bir yazılımdır. Örneğin sadece htop ile başlayan ifadeler arasaydık şöyle yapabilirdik.

```bash
# apt-cache search ^htop
htop - interactive processes viewer
```

Veya, sadece isminde **htop** ifadesi geçen paketleri arayabilirdik.

```bash
# apt-cache search --names-only htop
htop - interactive processes viewer
```

Eğer ilk harfi farklı olan, ama top ile biten, toplam 4 kelimelik paket isimlerini aramak isteseydik?

```bash
# apt-cache search --names-only ^.top$
atop - Monitor for system resources and process activity
htop - interactive processes viewer
itop - simple top-like interrupt load monitor
ntop - display network usage in web browser
ptop - transitional dummy package
```

Gördüğünüz gibi böylece bir çok **top** türevi program keşfetmiş olduk.

## Paketleri Yüklemek ve Güncellemek

Bulduğumuz bir paketi yüklemek için, apt-get komutunu kullanabiliriz.

```bash
# apt-get install htop
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  htop
0 upgraded, 1 newly installed, 0 to remove and 32 not upgraded.
Need to get 0 B/68,0 kB of archives.
After this operation, 188 kB of additional disk space will be used.
Selecting previously unselected package htop.
(Reading database ... 166430 files and directories currently installed.)
Preparing to unpack .../htop_1.0.2-3_amd64.deb ...
Unpacking htop (1.0.2-3) ...
Processing triggers for gnome-menus (3.10.1-0ubuntu2) ...
Processing triggers for desktop-file-utils (0.22-1ubuntu1) ...
Processing triggers for mime-support (3.54ubuntu1.1) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Setting up htop (1.0.2-3) ...
```

Bu komut aynı zamanda mevcut yüklü bir paketi güncellemeye de yarar.

```bash
# dpkg -l udev
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                     Version           Architecture      Description
+++-========================-=================-=================-=====================================================
ii  udev                     204-5ubuntu20.12  amd64             /dev/ and hotplug management daemon
```

```bash
# apt-get install udev
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following extra packages will be installed:
  libudev1 libudev1:i386
The following packages will be upgraded:
  libudev1 libudev1:i386 udev
3 upgraded, 0 newly installed, 0 to remove and 33 not upgraded.
Need to get 803 kB of archives.
After this operation, 4.096 B of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu/ trusty-updates/main udev amd64 204-5ubuntu20.15 [735 kB]
Get:2 http://archive.ubuntu.com/ubuntu/ trusty-updates/main libudev1 i386 204-5ubuntu20.15 [34,3 kB]
Get:3 http://archive.ubuntu.com/ubuntu/ trusty-updates/main libudev1 amd64 204-5ubuntu20.15 [33,4 kB]
Fetched 803 kB in 2s (400 kB/s)    
(Reading database ... 166440 files and directories currently installed.)
Preparing to unpack .../udev_204-5ubuntu20.15_amd64.deb ...
Adding 'diversion of /bin/udevadm to /bin/udevadm.upgrade by fake-udev'
Unpacking udev (204-5ubuntu20.15) over (204-5ubuntu20.12) ...
Preparing to unpack .../libudev1_204-5ubuntu20.15_i386.deb ...
De-configuring libudev1:amd64 (204-5ubuntu20.12) ...
Unpacking libudev1:i386 (204-5ubuntu20.15) over (204-5ubuntu20.12) ...
Preparing to unpack .../libudev1_204-5ubuntu20.15_amd64.deb ...
Unpacking libudev1:amd64 (204-5ubuntu20.15) over (204-5ubuntu20.12) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Processing triggers for ureadahead (0.100.0-16) ...
Setting up libudev1:amd64 (204-5ubuntu20.15) ...
Setting up libudev1:i386 (204-5ubuntu20.15) ...
Setting up udev (204-5ubuntu20.15) ...
udev stop/waiting
udev start/running, process 22510
Removing 'diversion of /bin/udevadm to /bin/udevadm.upgrade by fake-udev'
update-initramfs: deferring update (trigger activated)
Processing triggers for libc-bin (2.19-0ubuntu6.6) ...
Processing triggers for initramfs-tools (0.103ubuntu4.2) ...
update-initramfs: Generating /boot/initrd.img-3.16.0-38-generic
Warning: No support for locale: en_US.utf8
```

```bash
# dpkg -l udev
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                     Version           Architecture      Description
+++-========================-=================-=================-=====================================================
ii  udev                     204-5ubuntu20.15  amd64             /dev/ and hotplug management daemon
```

Eğer sisteminizdeki bütün paketleri güncellemek isterseniz, **upgrade** komutunu kullanabilirsiniz.

```bash
apt-get upgrade
```

Bu yöntem, ekstra paket yüklemeyecek, veya artık kullanılmayan paket/kütüphaneleri kaldırmayacaktır. Eğer bunu önemsemiyorsanız, **dist-upgrade** komutunu kullanabilirsiniz.

```bash
apt-get dist-upgrade
```

Güncelleme işlemini yapmadan, güncellenecek paketler hakkında bilgi almak için aşağıdaki gibi bir yöntem izlenebilir.

```bash
# apt-get -u upgrade --assume-no
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following packages will be upgraded:
  base-files gir1.2-gudev-1.0 grub-common grub-pc grub-pc-bin grub2-common
  icedtea-7-plugin icedtea-netx icedtea-netx-common icedtea-plugin
  libegl1-mesa libegl1-mesa-drivers libgbm1 libgl1-mesa-dri:i386
  libgl1-mesa-dri libgl1-mesa-glx libgl1-mesa-glx:i386 libglapi-mesa:i386
  libglapi-mesa libgles1-mesa libgles2-mesa libgudev-1.0-0 libgudev-1.0-0:i386
  libopenvg1-mesa libpam-systemd libsystemd-daemon0 libsystemd-journal0
  libsystemd-login0 libwayland-egl1-mesa libxatracker2 linux-firmware
  linux-libc-dev systemd-services
33 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Need to get 42,2 MB of archives.
After this operation, 6.100 kB of additional disk space will be used.
Do you want to continue? [Y/n] N
Abort.
```

apt'nin bir diğer güzelliği, işlemlerinizi simüle edebilmesidir. Örneğin yükleme veya güncelleme işleminin tamamını simüle edebilir, ancak gerçekleştirmeyebilirsiniz.

```bash
# apt-get install -s atop
NOTE: This is only a simulation!
      apt-get needs root privileges for real execution.
      Keep also in mind that locking is deactivated,
      so don't depend on the relevance to the real current situation!
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  atop
0 upgraded, 1 newly installed, 0 to remove and 33 not upgraded.
Inst atop (1.26-2 Ubuntu:14.04/trusty [amd64])
Conf atop (1.26-2 Ubuntu:14.04/trusty [amd64])
```

## Paketleri Kaldırmak

Kaldırma işlemi için **remove** komutu kullanılabilir.

```bash
# apt-get remove htop
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be REMOVED:
  htop
0 upgraded, 0 newly installed, 1 to remove and 33 not upgraded.
After this operation, 188 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 166439 files and directories currently installed.)
Removing htop (1.0.2-3) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Processing triggers for gnome-menus (3.10.1-0ubuntu2) ...
Processing triggers for desktop-file-utils (0.22-1ubuntu1) ...
Processing triggers for mime-support (3.54ubuntu1.1) ...
```

Bu işlem **htop** için gerekli ayar dosyalarını ve deb dosyalarını kaldırmaz. Eğer bunları da kaldırmak isteseydik, **purge** komutunu kullanmamız gerekirdi.

```bash
apt-get purge htop
```

