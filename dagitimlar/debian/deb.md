# deb Paketleri

Red Hat sistemler üzerindeki **rpm** paketlerinin Debian (ve Ubuntu) sistemler üzerindeki karşılığı **deb** paketleridir.

## Yükle

deb paketlerini yüklemek için **-i** parametresi kullanılır.

```bash
dpkg -i paket-dosyası.deb
```

## Yüklü Paketleri Listele

Mevcut paketleri liste halinde gösterir.

```bash
# dpkg -l
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                                        Version                                             Architecture Description
+++-===========================================-===================================================-============-=====================================================================================================================
ii  accountsservice                             0.6.35-0ubuntu7.2                                   amd64        query and manipulate user account information
ii  acl                                         2.2.52-1                                            amd64        Access control list utilities
ii  acpi-support                                0.142                                               amd64        scripts for handling many ACPI events
ii  acpid                                       1:2.0.21-1ubuntu2                                   amd64        Advanced Configuration and Power Interface event daemon
ii  add-apt-key                                 1.0-0.5                                             all          Command line tool to add GPG keys to the APT keyring
ii  adduser                                     3.113+nmu3ubuntu3                                   all          add and remove users and groups
ii  adobe-flashplugin                           1:20151110.1-0trusty1                               amd64        Adobe Flash Player plugin
ii  alsa-base                                   1.0.25+dfsg-0ubuntu4                                all          ALSA driver configuration files
ii  alsa-utils                                  1.0.27.2-1ubuntu2                                   amd64        Utilities for configuring and using ALSA
ii  anacron                                     2.3-20ubuntu1                                       amd64        cron-like program that doesn't go by time
ii  apg                                         2.2.3.dfsg.1-2ubuntu1                               amd64        Automated Password Generator - Standalone version
ii  apt                                         1.0.1ubuntu2.10                                     amd64        commandline package manager
ii  apt-clone                                   0.3.1~ubuntu11.1                                    all          Script to create state bundles
ii  apt-transport-https                         1.0.1ubuntu2.10                                     amd64        https download transport for APT
ii  apt-utils                                   1.0.1ubuntu2.10                                     amd64        package management related utility programs
```

Belirli bir paketin yüklü olup olmadığını görmek için, paket ismi verebilirsiniz.

```bash
# dpkg -l htop
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name               Version        Architecture   Description
+++-==================-==============-==============-==========================================
ii  htop               1.0.2-3        amd64          interactive processes viewer
```

Paket hakkında bilgi almak için **-s** seçeneği kullanılabilir. Eğer paket yüklü değilse bunun bilgisini de verecektir.

```bash
# dpkg -s htop
Package: htop
Status: install ok installed
Priority: optional
Section: utils
Installed-Size: 184
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: amd64
Version: 1.0.2-3
Depends: libc6 (>= 2.15), libncursesw5 (>= 5.6+20070908), libtinfo5
Suggests: strace, ltrace
Description: interactive processes viewer
 Htop is an ncursed-based process viewer similar to top, but it
 allows one to scroll the list vertically and horizontally to see
 all processes and their full command lines.
 .
 Tasks related to processes (killing, renicing) can be done without
 entering their PIDs.
Original-Maintainer: Eugene V. Lyubimkin <jackyf@debian.org>
Homepage: http://htop.sourceforge.net
```

```bash
# dpkg -s atop
dpkg-query: package 'atop' is not installed and no information is available
Use dpkg --info (= dpkg-deb --info) to examine archive files,
and dpkg --contents (= dpkg-deb --contents) to list their contents.
```


## Bir Paketi Kaldırmak

Yüklü bir paketi kaldırmak için **-r** parametresi kullanılabilir.

```bash
dpkg -r htop
```

Eğer ilgili paketin ayar dosyalarının (configuration files) beraberinde silinmesini istiyorsak, **-p** (purge) ile kaldırılabilir.

```bash
dpkg -p htop
```

## Bir Paket Dosyasının İçeriğini Görmek

Paket dosyasının içeriğindeki dosyaları görmek için,

```bash
dpkg -c paket-dosyası.deb
```