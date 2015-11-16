# yum Paket Yöneticisi

RedHat sistemler yum (Yellowdow Updater Modified) paket yöneticisini kullanırlar. Sisteminiz için uygun yazılımları, bağımlılıkları (dependency) ile birlikte yüklemeye yarayan bu paket yöneticisinin kullanımı oldukça kolaydır. Örneğin *Gelişmiş Terminal Komutları* bölümünde karşılaştığımız **htop** programını yüklemek istiyorsak, öncelikle ilgili paketin ismini arayabiliriz.

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
Görüleceği gibi içinde *htop* geçen iki paket bulundu, birisi bir php kütüphanesi olduğundan ilgimizi çekmiyor. Şimdi paketimizi yükleyebiliriz.

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

Yukarıdaki soruya "yes" cevabı verirsek yükleme işlemi devam ediyor.

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

