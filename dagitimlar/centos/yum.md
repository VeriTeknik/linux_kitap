# yum Paket Yöneticisi ve rpm

RedHat sistemler yum (Yellowdow Updater Modified) paket yöneticisini kullanırlar. Sisteminiz için uygun yazılımları, bağımlılıkları (dependency) ile birlikte yüklemeye yarayan bu paket yöneticisinin kullanımı oldukça kolaydır. yum bize rpm (RedHat Package Manager) dosyaları sağlar. Dolayısıyla istersek **rpm** paket dosyalarını edinip yükleyebiliriz, istersek paketleri **yum** ile yükleyebiliriz. Bu bölümde iki sistemi bir arada kullanmayı göreceğiz.

## list ve search

Örneğin *Gelişmiş Terminal Komutları* bölümünde karşılaştığımız **htop** programını yüklemek istiyorsak, öncelikle ilgili paketin ismini arayabiliriz.

```bash
yum search htop
Loaded plugins: fastestmirror, priorities, replace
Loading mirror speeds from cached hostfile
 * base: mirror.rackdc.com
 * elrepo: mirrors.ircam.fr
 * epel: ftp.linux.org.tr
 * extras: mirror.rackdc.com
 * rpmforge: mir01.syntis.net
 * updates: mirror.rackdc.com
 * webtatic: uk.repo.webtatic.com
1445 packages excluded due to repository priority protections
============================== N/S Matched: htop ===============================
htop.x86_64 : Interactive process viewer
php-lightopenid.noarch : PHP OpenID library

  Name and summary matches only, use "search all" for everything.
```
Görüleceği gibi içinde *htop* geçen iki paket bulundu, birisi bir php kütüphanesi olduğundan ilgimizi çekmiyor. Eğer **search** yerine **list** parametersini kullansaydık, daha dar bir sonuç elde edecektik.

```bash
yum list htop
Loaded plugins: fastestmirror, priorities, replace
Loading mirror speeds from cached hostfile
 * base: mirror.rackdc.com
 * elrepo: mirrors.ircam.fr
 * epel: ftp.linux.org.tr
 * extras: mirror.rackdc.com
 * rpmforge: mir01.syntis.net
 * updates: mirror.rackdc.com
 * webtatic: uk.repo.webtatic.com
1445 packages excluded due to repository priority protections
Available Packages
htop.x86_64                                         1.0.3-1.el6.rf                                         rpmforge
```

yum ile yükleyebileceğiniz bütün paketleri, **list** parametersini paket ismi belirtmeden görebilirsiniz. Liste çok uzun olacağı için sayfa sayfa görüntülemekte fayda var.

```bash
yum list | more
```

Benzer şekilde yüklü paketleri görüntüleyebiliriz.

```bash
yum list installed
```

Ancak yum ile yüklü paketleri görüntülemektense, **rpm** ile bu işi yapmak daha sağlıklıdır ve genellikle tercih edilir.

```bash
rpm -qa
```


## install

Şimdi paketimizi yükleyebiliriz.

```bash
yum install htop
Loaded plugins: fastestmirror, priorities, replace
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirror.rackdc.com
 * elrepo: mirrors.ircam.fr
 * epel: ftp.linux.org.tr
 * extras: mirror.rackdc.com
 * rpmforge: mir01.syntis.net
 * updates: mirror.rackdc.com
 * webtatic: uk.repo.webtatic.com
1445 packages excluded due to repository priority protections
Resolving Dependencies
--> Running transaction check
---> Package htop.x86_64 0:1.0.3-1.el6.rf will be installed
--> Finished Dependency Resolution

Dependencies Resolved

============================================================================================================================
 Package                  Arch                       Version                             Repository                    Size
============================================================================================================================
Installing:
 htop                     x86_64                     1.0.3-1.el6.rf                      rpmforge                      87 k

Transaction Summary
============================================================================================================================
Install       1 Package(s)

Total download size: 87 k
Installed size: 209 k
Is this ok [y/N]: 
```

Yukarıdaki soruya "yes" cevabı verirsek yükleme işlemi devam ediyor (Eğer "Is this ok" sorusunu sormasını istemeseydik, *install* komutunu **-y** parametresiyle çalıştırabilirdik).

```bash
Is this ok [y/N]: y
Downloading Packages:
htop-1.0.3-1.el6.rf.x86_64.rpm                                                                       |  87 kB     00:00     
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : htop-1.0.3-1.el6.rf.x86_64                                                                               1/1 
  Verifying  : htop-1.0.3-1.el6.rf.x86_64                                                                               1/1 

Installed:
  htop.x86_64 0:1.0.3-1.el6.rf                                                                                              

Complete!
```

Bu kadar! Artık sistemimizde *htop* yüklü.

## remove

Aynı paketi sistemimizden kaldırmak istersek,

```bash
yum remove htop
Loaded plugins: fastestmirror, priorities, replace
Setting up Remove Process
Resolving Dependencies
--> Running transaction check
---> Package htop.x86_64 0:1.0.3-1.el6.rf will be erased
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package       Arch            Version                 Repository          Size
================================================================================
Removing:
 htop          x86_64          1.0.3-1.el6.rf          @rpmforge          209 k

Transaction Summary
================================================================================
Remove        1 Package(s)

Installed size: 209 k
Is this ok [y/N]:
```

## update

Eğer belirli bir programı güncellemek istersek, **update** parametresini kullanabiliriz.

```
bash
yum update vim
Loaded plugins: fastestmirror, priorities, replace
Setting up Update Process
Loading mirror speeds from cached hostfile
 * base: mirror.rackdc.com
 * elrepo: mirrors.ircam.fr
 * epel: ftp.linux.org.tr
 * extras: mirror.rackdc.com
 * rpmforge: mir01.syntis.net
 * updates: mirror.rackdc.com
 * webtatic: uk.repo.webtatic.com
1445 packages excluded due to repository priority protections
Resolving Dependencies
--> Running transaction check
---> Package vim-enhanced.x86_64 2:7.2.411-1.8.el6 will be updated
---> Package vim-enhanced.x86_64 2:7.4.629-5.el6 will be an update
--> Processing Dependency: vim-common = 2:7.4.629-5.el6 for package: 2:vim-enhanced-7.4.629-5.el6.x86_64
--> Running transaction check
---> Package vim-common.x86_64 2:7.2.411-1.8.el6 will be updated
---> Package vim-common.x86_64 2:7.4.629-5.el6 will be an update
--> Processing Dependency: vim-filesystem for package: 2:vim-common-7.4.629-5.el6.x86_64
--> Running transaction check
---> Package vim-filesystem.x86_64 2:7.4.629-5.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================
 Package                        Arch                   Version                          Repository            Size
===================================================================================================================
Updating:
 vim-enhanced                   x86_64                 2:7.4.629-5.el6                  base                 1.0 M
Installing for dependencies:
 vim-filesystem                 x86_64                 2:7.4.629-5.el6                  base                  15 k
Updating for dependencies:
 vim-common                     x86_64                 2:7.4.629-5.el6                  base                 6.7 M

Transaction Summary
===================================================================================================================
Install       1 Package(s)
Upgrade       2 Package(s)

Total download size: 7.8 M
Is this ok [y/N]:
```

Gördüğünüz gibi *vim* ile birlikte gerekli paketler de güncelleniyor.

Öte yandan sistemimizdeki bütün paketlerin güncellenmesi için, paket ismi belirtmeden *update* komutunu kullanabilirdik.

```bash
yum update
```

Eğer sadece hangi paketlerin güncellemesinin mevcut olduğunu öğrenmek istersek aşağıdaki komutu kullanabiliriz.

```bash
yum check-update
```

## info

Eğer bir paket hakkında bilgi edinmek istiyorsak, **info** parametresini kullanabiliriz.

```bash
[root@emre ~]# yum info htop
Loaded plugins: fastestmirror, priorities, replace
Loading mirror speeds from cached hostfile
 * base: mirror.rackdc.com
 * elrepo: mirrors.ircam.fr
 * epel: ftp.linux.org.tr
 * extras: mirror.rackdc.com
 * rpmforge: mir01.syntis.net
 * updates: mirror.rackdc.com
 * webtatic: uk.repo.webtatic.com
1445 packages excluded due to repository priority protections
Available Packages
Name        : htop
Arch        : x86_64
Version     : 1.0.3
Release     : 1.el6.rf
Size        : 87 k
Repo        : rpmforge
Summary     : Interactive process viewer
URL         : http://htop.sourceforge.net/
License     : GPL
Description : htop is an interactive process viewer for Linux.
```

Benzer şekilde paket bilgisini **rpm** ile almak da mümkün.

```bash
rpm -qi htop
Name        : htop                         Relocations: (not relocatable)
Version     : 1.0.3                             Vendor: Dag Apt Repository, http://dag.wieers.com/apt/
Release     : 1.el6.rf                      Build Date: Wed 07 May 2014 12:16:32 AM EEST
Install Date: Mon 16 Nov 2015 11:48:37 PM EET      Build Host: lisse.hasselt.wieers.com
Group       : Applications/System           Source RPM: htop-1.0.3-1.el6.rf.src.rpm
Size        : 214070                           License: GPL
Signature   : DSA/SHA1, Wed 07 May 2014 12:26:56 AM EEST, Key ID a20e52146b8d79e6
Packager    : Dag Wieers <dag@wieers.com>
URL         : http://htop.sourceforge.net/
Summary     : Interactive process viewer
Description :
htop is an interactive process viewer for Linux.
```

## provides

Bir paket yöneticisi kullanmanın en güzel yanı, belirli dosya veya kütüphaneleri hangi paketlerin sağladığının rahatlıkla bulunabilmesidir. yum ile bu işi *provides* ve *whatprovides* parametreleri yapar. Her iki parametre de aynı işi yapar, sadece farklı isimlerdir.

Örneğin Sistemimizde ```/etc/httpd/conf/httpd.conf``` şeklinde bir dosya var ve bu dosyanın hangi paketle geldiğini öğrenmek istiyoruz,

```bash
yum provides /etc/httpd/conf/httpd.conf 
Loaded plugins: fastestmirror, priorities, replace
Loading mirror speeds from cached hostfile
 * base: mirror.rackdc.com
 * elrepo: mirrors.ircam.fr
 * epel: ftp.linux.org.tr
 * extras: mirror.rackdc.com
 * rpmforge: mir01.syntis.net
 * updates: mirror.rackdc.com
 * webtatic: uk.repo.webtatic.com
1445 packages excluded due to repository priority protections
httpd-2.2.15-45.el6.centos.x86_64 : Apache HTTP Server
Repo        : base
Matched from:
Filename    : /etc/httpd/conf/httpd.conf



httpd-2.2.15-47.el6.centos.x86_64 : Apache HTTP Server
Repo        : updates
Matched from:
Filename    : /etc/httpd/conf/httpd.conf



httpd-2.2.15-39.el6.centos.x86_64 : Apache HTTP Server
Repo        : installed
Matched from:
Other       : Provides-match: /etc/httpd/conf/httpd.conf
```

Yukarıdaki sonuçlardan, dosyanın **httpd** paketi ile geldiğini görebiliyoruz.

## Paket Grupları

yum ile bazı paketler gruplandırılmıştır. Örneğin sık kullanılan masaüstü uygulamaları, veya ağ uygulamaları ayrı paketlerde bulunur. Yükleyebileceğimiz paketleri listelemek için **grouplist** parametresi kullanılır.

```bash
yum grouplist
```

Listelenen gruplardan birisi hakkında bilgi edinmek istersek **groupinfo** parametresini kullanabiliriz.

```bash
yum groupinfo "Networking Tools"
Loaded plugins: fastestmirror, priorities, replace
Setting up Group Process
Loading mirror speeds from cached hostfile
 * base: mirror.rackdc.com
 * elrepo: mirrors.ircam.fr
 * epel: ftp.linux.org.tr
 * extras: mirror.rackdc.com
 * rpmforge: mir01.syntis.net
 * updates: mirror.rackdc.com
 * webtatic: uk.repo.webtatic.com
1445 packages excluded due to repository priority protections

Group: Networking Tools
 Description: Tools for configuring and analyzing computer networks.
 Mandatory Packages:
   tcpdump
 Default Packages:
   nc
   openswan
 Optional Packages:
   NetworkManager-openswan
   arptables_jf
   arpwatch
   dropwatch
   ebtables
   ettercap
   ipset
   iptraf
   iptstate
   isic
   lksctp-tools
   mipv6-daemon
   mrtg
   netlabel_tools
   nmap
   openvpn
   qstat
   stunnel
   vtun
   wireshark
   xprobe2
 ```
 
 Bu grubu yüklemek, güncellemek veya kaldırmak için uygulayacağınız komutlar artık aşikar,
 
 ```bash
 yum groupinstall "Networking Tools"
 yum groupupdate "Networking Tools"
 yum groupremove "Networking Tools"
 ```