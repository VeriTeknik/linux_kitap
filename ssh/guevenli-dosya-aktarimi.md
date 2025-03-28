# Güvenli Dosya Aktarımı

SSH protokolünü kullanarak güvenli dosya transferi sağlamak mümkündür. Bunun için scp, sftp veya rsync kullanılabilir.

## rsync

`rsync` (remote sync), dosyaları ve dizinleri yerel olarak veya uzak sistemler arasında verimli bir şekilde senkronize etmek için kullanılan çok güçlü bir araçtır. Sadece değişen dosya bölümlerini transfer eden delta transfer algoritması sayesinde özellikle yedekleme ve büyük veri transferleri için çok popülerdir.

`rsync`, varsayılan olarak SSH protokolünü kullanarak güvenli bir bağlantı kurar, ancak kendi `rsync` daemon protokolü üzerinden veya yerel dosya sistemleri arasında da çalışabilir. Kullanımı oldukça esnektir.

**Temel Kullanım (SSH üzerinden):**

*   **Yerelden Uzak Sunucuya:**
    ```bash
    # /yerel/dizin içeriğini uzak sunucudaki /uzak/dizin içine kopyala/eşitle
    # -a: arşiv modu (izinleri, zamanları, sembolik linkleri vb. korur, -rlptgoD ile eşdeğer)
    # -v: verbose (detaylı çıktı)
    # -z: transfer sırasında sıkıştırma uygula
    rsync -avz /yerel/dizin/ root@UZAK_SUNUCU:/uzak/dizin/ 
    ```
    **Not:** Kaynak dizinin sonundaki `/` işareti önemlidir. `/yerel/dizin/` kullanıldığında dizinin *içeriği* hedefe kopyalanır. `/yerel/dizin` kullanıldığında ise dizinin *kendisi* (ve içeriği) hedefin içine kopyalanır (yani `/uzak/dizin/dizin` oluşur).

*   **Uzak Sunucudan Yerele:**
    ```bash
    rsync -avz root@UZAK_SUNUCU:/uzak/dizin/ /yerel/dizin/
    ```

*   **SSH Portu Belirtme:**
    ```bash
    rsync -avz -e 'ssh -p 2222' /yerel/dizin/ root@UZAK_SUNUCU:/uzak/dizin/
    ```

*   **Simülasyon (Dry Run):** `-n` veya `--dry-run` seçeneği ile hangi dosyaların transfer edileceğini görebilir, ancak gerçek transferi yapmazsınız.
    ```bash
    rsync -avzn /yerel/dizin/ root@UZAK_SUNUCU:/uzak/dizin/
    ```

*   **Hedefte Olmayan Dosyaları Silme:** `--delete` seçeneği, kaynakta bulunmayan dosyaların hedef dizinden silinmesini sağlar (dikkatli kullanılmalıdır!).
    ```bash
    rsync -avz --delete /yerel/dizin/ root@UZAK_SUNUCU:/uzak/dizin/
    ```

rsync, argüman sayısının fazlalığından dolayı \(1 milyon ve üzeri\) "rm" komutunun dahi silemediği dosyaları kolaylıkla siler. Bu dosyaları silmek için hedefteki dizini boş bir dizin ile eşleştirmeniz yeterlidir.

## scp (Secure Copy)

`scp`, SSH protokolünü kullanarak dosyaları yerel ve uzak sistemler arasında güvenli bir şekilde kopyalamak için kullanılan klasik bir komuttur. Kullanımı `cp` komutuna benzer.

Uzak sunucudan dosya indirmek için

```bash
scp root@192.168.168.5:/root/dosya.tar.gz /home/eaydin
```

Uzak sunucuya dosya göndermek için

```bash
scp /root/dosya.tar test@10.42.30.65:/opt/settings
```

Eğer sunucunun SSH portu 22'den farklıysa, `-P` parametresiyle bağlantı portunu tanımlayabilirsiniz.

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

**Not:** Modern OpenSSH sürümlerinde `scp` komutu, arka planda genellikle SFTP protokolünü kullanır. Bu, eski `scp` protokolünün bazı sınırlamalarını (örn. hata bildirme) ortadan kaldırır. Ancak `scp` komutunun sözdizimi ve temel işlevi aynı kalmıştır.

## sftp (SSH File Transfer Protocol)

`sftp`, SSH üzerinden güvenli dosya transferi yapmak için tasarlanmış ayrı bir protokoldür ve genellikle `scp`'ye göre daha fazla özellik sunan interaktif bir komut satırı istemcisi (`sftp`) ile birlikte gelir. FTP komutlarına benzer bir arayüz sunar.

**Avantajları:**
*   İnteraktif oturum: Bağlantı kurulduktan sonra `ls`, `cd`, `get`, `put`, `mkdir`, `rm` gibi komutlarla uzak dosya sistemi üzerinde gezinebilir ve işlem yapabilirsiniz.
*   Dosya listeleme, dizin oluşturma/silme, yeniden adlandırma gibi dosya sistemi işlemleri yapabilme.
*   Yarım kalan transferleri devam ettirme (`reget`, `reput` - sunucu ve istemci desteğine bağlı).

**Dezavantajları:**
*   `scp`'ye göre bazı durumlarda biraz daha yavaş olabilir (protokol ek yükü nedeniyle).
*   Basit tek dosya transferleri için `scp` kadar hızlı olmayabilir.

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
reget remote [local]        Resume download file
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

* Mevcut sistemimizde \(örneğin kullandığımız laptop\) bir komut çalıştırmak istersek, komutun başına **!** koyabiliriz.

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

* Ayrıca mevcut sistemde bazı yerel komutlar çalıştırmak için komutun başına **l** harfi \(**l**ocal\) konulur. Örneğin `!cd` çalışmayacağı için, `lcd` kullanılır.
* Dosya indirmek \(download\) için `get` komutu kullanılır. `mget` (multiple get) genellikle joker karakterlerle birden fazla dosya indirmek için kullanılır (bazı istemcilerde desteklenmeyebilir). Yarım kalan dosya indirme işlemlerini devam ettirmek için `reget` kullanılır.
* Dosya yüklemek \(upload\) için `put` komutu kullanılır. `mput` (multiple put) benzer şekilde birden fazla dosya yüklemek için kullanılır. Yarım kalan yüklemeleri devam ettirmek için `reput` kullanılır.
