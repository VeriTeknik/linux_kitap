# deb Paketleri ve dpkg

Debian ve türevi sistemlerde (Ubuntu, Mint vb.) kullanılan temel paket formatı `.deb` uzantılı dosyalardır. Bu dosyalar, programları, kütüphaneleri, yapılandırma dosyalarını ve ilgili betikleri içerir.

**dpkg** (Debian Package), `.deb` dosyalarını kurmak, kaldırmak ve sorgulamak için kullanılan düşük seviyeli komut satırı aracıdır.

**Önemli Not:** `dpkg`, paketleri doğrudan yönetir ancak **bağımlılıkları otomatik olarak çözmez veya yönetmez**. Paket kurma, kaldırma veya güncelleme gibi işlemler için genellikle `apt` (veya `apt-get`) gibi daha üst seviye paket yöneticilerinin kullanılması **şiddetle tavsiye edilir**. `apt`, depoları kullanarak gerekli bağımlılıkları otomatik olarak indirir ve kurar. `dpkg` daha çok indirilen `.deb` dosyalarını manuel olarak kurmak (ancak bağımlılıkları sağlamak sizin sorumluluğunuzdadır), kurulu paketleri sorgulamak veya düşük seviyeli paket manipülasyonları için kullanılır.

## `dpkg` Komutu ile Sorgulama ve Yönetim

**Paket Kurulumu (Bağımlılıkları Çözmez!):**

İndirilmiş bir `.deb` dosyasını kurmak için `-i` (veya `--install`) kullanılır:

```bash
sudo dpkg -i paket-dosyası.deb
```
**Not:** Eğer bu paket, sistemde kurulu olmayan başka paketlere bağımlıysa, `dpkg` hata verecek ve paketi yapılandırmayacaktır. Bu durumda genellikle `sudo apt --fix-broken install` komutu ile eksik bağımlılıkların kurulması gerekir. Yerel bir `.deb` dosyasını bağımlılıklarıyla birlikte kurmanın daha kolay yolu `apt` kullanmaktır: `sudo apt install ./paket-dosyası.deb`.

**Yüklü Paketleri Listeleme ve Sorgulama:**

Sistemde kurulu paketleri (ve durumlarını) listelemek için `-l` (veya `--list`) kullanılır (`less` ile kullanmak faydalıdır):

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
# Belirli bir paketin durumunu listele (grep ile filtreleyerek)
dpkg -l | grep htop
# veya doğrudan paket adıyla:
dpkg -l htop 
```
Çıktıdaki ilk iki harf (örn. `ii`) paketin durumunu gösterir:
*   İlk harf (İstenen Durum): `i` (Install), `h` (Hold), `r` (Remove), `p` (Purge).
*   İkinci harf (Mevcut Durum): `n` (Not installed), `i` (Installed), `c` (Config-files), `U` (Unpacked), `F` (Half-configured), `H` (Half-installed), `W` (Triggers-awaited), `T` (Triggers-pending).
*   `ii`: İstenen ve mevcut durum "Installed".
*   `rc`: Paket kaldırılmış (Removed) ancak yapılandırma dosyaları duruyor (Config-files).

Kurulu bir paket hakkında detaylı bilgi almak için `-s` (veya `--status`) kullanılır:

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

Kurulu bir paketin sisteme hangi dosyaları yüklediğini listelemek için `-L` (veya `--listfiles`) kullanılır:
```bash
dpkg -L htop
```

Belirli bir dosyanın hangi kurulu pakete ait olduğunu bulmak için `-S` (veya `--search`) kullanılır:
```bash
dpkg -S /bin/htop
```

**Paket Kaldırma:**

**Uyarı:** Aşağıdaki komutlar bağımlılıkları otomatik yönetmez. Genellikle `apt remove` veya `apt purge` kullanın.

Yüklü bir paketi kaldırmak (yapılandırma dosyaları kalır) için `-r` (veya `--remove`) kullanılır:

```bash
sudo dpkg -r htop
```

Bir paketi yapılandırma dosyalarıyla birlikte tamamen kaldırmak için `-P` (veya `--purge`) kullanılır:

```bash
sudo dpkg -P htop
```

**Diğer İşlemler:**

Bir `.deb` paket dosyasının içeriğini (kurmadan) listelemek için `-c` (veya `--contents`) kullanılır:

```bash
dpkg -c paket-dosyası.deb
```

Bir `.deb` paket dosyası hakkında bilgi (kontrol dosyası içeriği) almak için `-I` (veya `--info`) kullanılır:
```bash
dpkg -I paket-dosyası.deb
```

Kurulumu tamamlanmamış veya yarıda kalmış paketleri yapılandırmak için `--configure -a` kullanılır:
```bash
sudo dpkg --configure -a
```
(Bu işlem genellikle `apt --fix-broken install` komutunun bir parçası olarak da çalıştırılır.)

Özetle, `dpkg` `.deb` paketleriyle düşük seviyede çalışmak için temel araçtır, ancak bağımlılık yönetimi karmaşıklığı nedeniyle günlük kullanımda `apt` komutları tercih edilmelidir.
