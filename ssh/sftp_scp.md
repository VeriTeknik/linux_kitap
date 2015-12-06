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

