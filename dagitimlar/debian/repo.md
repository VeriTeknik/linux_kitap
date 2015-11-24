# Repo Ayarları

Debian sistemlerde de repo bilgileri dosyalarda tutulur. Aşağıda Debian 7 sistemin standart repoları görülüyor.

```bash
root@test:~# cat /etc/apt/sources.list
# 

# deb cdrom:[Debian GNU/Linux 7.0.0 _Wheezy_ - Official amd64 NETINST Binary-1 20130504-14:43]/ wheezy main

#deb cdrom:[Debian GNU/Linux 7.0.0 _Wheezy_ - Official amd64 NETINST Binary-1 20130504-14:43]/ wheezy main

deb http://ftp.debian.org/debian/ wheezy main
deb-src http://ftp.debian.org/debian/ wheezy main

deb http://security.debian.org/ wheezy/updates main
deb-src http://security.debian.org/ wheezy/updates main

# wheezy-updates, previously known as 'volatile'
deb http://ftp.debian.org/debian/ wheezy-updates main
deb-src http://ftp.debian.org/debian/ wheezy-updates main
```

Benzer şekilde, her işleminiz için farklı bir dosya oluşturup ```/etc/apt/sources.list.d/``` dizini altında yerleştirebilirsiniz. Örneğin Ubuntu bir sistemde spotify yüklenmesi için kullanılan repo dosyası aşağıdaki gibidir.

```bash
# cat /etc/apt/sources.list.d/spotify.list 
deb http://repository.spotify.com stable non-free
```

Repo eklemek için bu dosyalar düzenlenebileceği gibi, **add-apt-repository** programı da kullanılabilir.

```bash
add-apt-repository "deb http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main"
```

Yukarıdaki komut, ```/etc/apt/sources.list``` dosyasına ilgili satırları ekleyecektir, ```/etc/apt/sources.list.d/``` dizinine yeni bir dosya oluşturmayacaktır.