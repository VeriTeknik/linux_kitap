# rsync, sftp ve scp Kullanımı

SSH protokolünü kullanarak güvenli dosya transferi sağlamak mümkündür. Bunun için scp veya sftp kullanılabilir.

## rsync

rsync Linux sistemlerin vazgeçilmez arşivleme, kopyalama ve transfer yazılımıdır, hafif mimarisi ve hızı ile vazgeçilmez bir araçtır, kullanımı oldukça kolay olan yazılımın işlem yapılacak her iki sunucuda da yüklü olması gerekmektedir. Kaynaktan ya da hedeften kolayca kullanılabilir, en temel eşitleme komutu aşağıdaki gibidir:

Kaynaktan çalışıtılırsa:
```bash
rsync -arzgopv /dizin/* root@HEDEF_IPADRESI:/dizin/
```
Hedeften çalışıtılırsa:
```bash
rsync -arzgopv root@KAYNAK_IPADRESI:/dizin/* /dizin/
```

rsync, argüman sayısının fazlalığından dolayı (1 milyon ve üzeri) "rm" komutunun dahi silemediği dosyaları kolaylıkla siler. Bu dosyaları silmek için hedefteki dizini boş bir dizin ile eşleştirmeniz yeterlidir.
## scp

scp (secure copy) doğrudan SSH protokolü üzerinden dosya transferi sağlar. Böylece sisteme farklı mekanizmalarla (FTP vb.) erişmenize gerek olmaz.

Uzak sunucudan dosya indirmek için

```bash
scp root@192.168.168.5:/root/dosya.tar.gz /home/eaydin
```

Uzak sunucuya dosya göndermek için

```bash
scp /root/dosya.tar test@10.42.30.65:/opt/settings
```

Eğer sunucunun SSH portu 22'den farklıysa, ```-P``` parametresiyle bağlantı portunu tanımlayabilirsiniz.

```bash
scp -P 2299 root@192.168.1.30:/var/log/syslog .
```

### İki Uzak Sunucu Arasında Kopyalama Yapmak

Örneğin Veriteknik Sunucu Merkezinde iki sunucumuz var ve bu sunucular arasında scp ile dosya transferi gerçekleştirmek istiyoruz. Bunun için sunuculardan birine SSH ile bağlanıp, onun üzerinden diğerine scp ile dosya transferi başlatabiliriz.

Ancak alternatif bir yöntem, scp'nin **-3** parametresini kullanmak.

```bash
scp -3 root@sunucu1:/var/log/syslog root@sunucu2:/root/backups
```

Yukarıdaki yöntem ile dosyalar doğrudan transfer edilecektir. Ancak bunun için OpenSSH versiyonunun 5.7+ olması gerekmektedir.

## sftp

scp'nin bazı limitlerinden dolayı sftp geliştirilmiştir. scp ile benzer şekilde SSH protokolünü kullanır, ancak dosya transferi için alışıla gelmiş FTP komutlarını destekler.

SFTP ile birden fazla dosyanın transferi daha rahat gerçekleştirilebilir, yarım kalan transferler devam ettirilebilir. scp'nin transfer hızı, algoritma farklılığından dolayı sftp'den daha hızlı olabilir.

Bir sunucuya SFTP bağlantısı kurmak için aşağıdaki gibi bir yol izlenir.

```bash
eaydin@dixon ~ $ sftp root@test-centos1
Connected to test-centos1.
sftp> 
```

Artık karşı sunucuya SFTP ile bağlanmış olursunuz. Kullanabileceğiniz komutları görmek için **?** yazılıp ENTER'a basılabilir.

```bash
sftp> ?
Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit                               Quit sftp
get [-Ppr] remote [local]          Download file
reget remote [local]		Resume download file
help                               Display this help text
lcd path                           Change local directory to 'path'
lls [ls-options [path]]            Display local directory listing
lmkdir path                        Create local directory
ln [-s] oldpath newpath            Link remote file (-s for symlink)
lpwd                               Print local working directory
ls [-1afhlnrSt] [path]             Display remote directory listing
lumask umask                       Set local umask to 'umask'
mkdir path                         Create remote directory
progress                           Toggle display of progress meter
put [-Ppr] local [remote]          Upload file
pwd                                Display remote working directory
quit                               Quit sftp
rename oldpath newpath             Rename remote file
rm path                            Delete remote file
rmdir path                         Remove remote directory
symlink oldpath newpath            Symlink remote file
version                            Show SFTP version
!command                           Execute 'command' in local shell
!                                  Escape to local shell
?                                  Synonym for help
```

Burada bilinmesi gereken önemli noktalar şunlardır:

* Mevcut sistemimizde (örneğin kullandığımız laptop) bir komut çalıştırmak istersek, komutun başına **!** koyabiliriz.

 
```bash
sftp> lsblk
Invalid command.
sftp> !lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 931,5G  0 disk 
├─sda1   8:1    0   350M  0 part 
├─sda2   8:2    0   200G  0 part 
├─sda3   8:3    0 350,9G  0 part /
├─sda4   8:4    0     1K  0 part 
├─sda5   8:5    0   6,9G  0 part [SWAP]
└─sda6   8:6    0 373,5G  0 part /media/backups
sr0     11:0    1  1024M  0 rom
```

* Ayrıca mevcut sistemde bazı yerel komutlar çalıştırmak için komutun başına **l** harfi (**l**ocal) konulur. Örneğin ```!cd``` çalışmayacağı için, ```lcd``` kullanılır.
* Dosya indirmek (download) için ```get``` ve ```mget``` komutları kullanılır. Yarım kalan dosya indirme işlemlerini devam ettirmek için ```reget``` kullanılır.
* Dosya yüklemek (upload) için ```put``` ve ```mput``` komutları kullanılır.
