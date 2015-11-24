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

## Paketleri YÜklemek

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

