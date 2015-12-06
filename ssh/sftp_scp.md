# sftp ve scp Kullanımı

SSH protokolünü kullanarak güvenli dosya transferi sağlamak mümkündür. Bunun için scp veya sftp kullanılabilir.

## scp

scp (secure copy) doğrudan SSH protokolü üzerinden dosya transferi sağlar. Böylece sisteme farklı mekanizmalarla (FTP vb.) erişmenize gerek olmaz.

Uzak sunucudan dosya indirmek için

```bash
scp root@192.168.16.5:/root/dosya.tar.gz /home/eaydin
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