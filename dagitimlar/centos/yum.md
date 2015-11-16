# yum Paket Yöneticisi ve rpm

RedHat sistemler yum (Yellowdow Updater Modified) paket yöneticisini kullanırlar. Sisteminiz için uygun yazılımları, bağımlılıkları (dependency) ile birlikte yüklemeye yarayan bu paket yöneticisinin kullanımı oldukça kolaydır. yum bize rpm (RedHat Package Manager) dosyaları sağlar. Dolayısıyla istersek **rpm** paket dosyalarını edinip yükleyebiliriz, istersek paketleri **yum** ile yükleyebiliriz. Bu bölümde iki sistemi bir arada kullanmayı göreceğiz.

##list ve search

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



# install

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