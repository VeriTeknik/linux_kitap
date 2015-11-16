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
  