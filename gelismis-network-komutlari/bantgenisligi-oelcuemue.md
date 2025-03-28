# Bantgenişliği Ölçümü

Bu bölümde iki sunucu arasındaki ağ kullanımının test edilmesi için birkaç teknikten bahsedeceğiz. Bu tekniklerin bazıları GNU/Linux dağıtımlarının çoğunda halihazırda yüklü olan araçları kullanırken, bazıları yeni programların yüklenmesini gerektirebilir.

## `dd` ve `netcat` Kullanımı

Bu yöntem, iki makine arasında ham TCP veya UDP aktarım hızını ölçmek için temel Linux araçlarını kullanır. Bir makinede `netcat` sunucu olarak dinlerken, diğer makinede `dd` ile belirli miktarda veri oluşturulup `netcat` istemcisi aracılığıyla sunucuya gönderilir. `dd` komutunun çıktısı, transfer süresini ve hızını gösterir.

**Uyarı:** Bu yöntem veriyi **şifrelemeden** aktarır. Güvenilmeyen ağlarda kullanılmamalıdır.

**Adımlar:**

1.  **Sunucu Tarafı (`192.168.16.40`):** Belirli bir portu (`5566`) dinle ve gelen veriyi `/dev/null`'a göndererek yok say:

```
# 192.168.16.40 makinası üzerinde
[root@server ~]# nc -v -l 5566 > /dev/null
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Listening on :::5566
Ncat: Listening on 0.0.0.0:5566
```

Yukarıdaki durum şunu söylüyor: 5566 portunu dinle, ama gelen bütün verileri olduğu gibi sil. Şimdi dd ile client makinasından 1GB veriyi server'a gönderelim ve ne kadar sürdüğüne bakalım.

```
# 192.168.16.30 makinası üzerinde
eaydin@client ~ $ dd if=/dev/zero bs=1024K count=1024 | nc -v 192.168.16.40 5566
Connection to 192.168.16.40 5566 port [tcp/*] succeeded!
1024+0 records in
1024+0 records out
1073741824 bytes (1,1 GB, 1,0 GiB) copied, 10,5557 s, 102 MB/s
```

Burada dd'ye dedik ki

* İçinde "0" bulunan Byte'lar gönder \(/dev/zero\)
* Her Byte bloğu 1024KByte \(yani 1MB\) boyutunda olsun \(bs=1024K\)
* Toplam 1024 tane blok gönder \(yani 1024x1MB = 1GB\)
* Çıktıyı netcat ile 192.168.16.40'ın 5566 portuna yönlendir

Sonunda da 1GB verinin gönderiminin 10 saniyeden biraz daha uzun sürdüğünü görüyoruz, yani saniyede 102MB transfer hızına sahipmiş bu iki makina arasındaki bağlantı.

Bütün bunlar gerçekleşirken server tarafında şunlar oldu.

```
[root@server ~]# nc -v -l 5566 > /dev/null
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Listening on :::5566
Ncat: Listening on 0.0.0.0:5566
Ncat: Connection from 192.168.16.30.
Ncat: Connection from 192.168.16.30:42554.
[root@localhost ~]#
```

Gördüğünüz gibi bağlantı client tarafından kapatılınca server da kapattı kendisini. Bunun olmasını istemiyorsak \(her testimizde server tarafındaki netcat'i yeniden çalıştırmak istemiyorsak\) parametreler arasında `-k` eklemek gerekir. Örneğin,

```
[root@server ~]# nc -v -k -l 5566 > /dev/null
```

Bu test varsayılan olarak TCP üzerinden yapılır. UDP hızını test etmek için hem sunucu hem de istemci tarafında `nc` komutuna `-u` parametresini eklemeniz gerekir. Ancak UDP bağlantısız olduğu için, bu test paket kaybı gibi faktörleri tam olarak yansıtmayabilir ve `iperf` gibi araçlar UDP testi için genellikle daha uygundur.

## `iperf` / `iperf3`

`iperf` (ve modern versiyonu `iperf3`), ağ bant genişliği performansını ölçmek için özel olarak tasarlanmış standart bir araçtır. TCP ve UDP testlerini destekler, çeşitli parametrelerle testin süresini, paralel akış sayısını, raporlama aralığını vb. ayarlama imkanı sunar.

Genellikle istemci-sunucu modunda çalışır. Test edilecek iki makineye de kurulması gerekir (`sudo apt install iperf3` veya `sudo dnf install iperf3`).

Sunucu tarafında aşağıdaki komutu çalıştırdığınızda kendisi zaten default port üzerinden dinlemeye başlar:

```
[root@server ~]# iperf3 -s
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
```

Client tarafındaysa bu sunucu üzerinde test yapmasını söylemek gerekiyor. Sırayla testleri yaptıktan sonra aşağıdaki gibi bir sonuç sunuyor.

```
eaydin@client ~ $ iperf3 -c 94.103.33.50
Connecting to host 94.103.33.50, port 5201
[  4] local 192.168.16.30 port 45944 connected to 94.103.33.50 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  88.4 MBytes   742 Mbits/sec   12    361 KBytes       
[  4]   1.00-2.00   sec  82.5 MBytes   692 Mbits/sec    3    373 KBytes       
[  4]   2.00-3.00   sec  82.5 MBytes   692 Mbits/sec    3    378 KBytes       
[  4]   3.00-4.00   sec  87.5 MBytes   734 Mbits/sec    9    385 KBytes       
[  4]   4.00-5.00   sec  85.0 MBytes   713 Mbits/sec    7    389 KBytes       
[  4]   5.00-6.00   sec  95.0 MBytes   797 Mbits/sec    0    547 KBytes       
[  4]   6.00-7.00   sec   109 MBytes   912 Mbits/sec  126    219 KBytes       
[  4]   7.00-8.00   sec   109 MBytes   912 Mbits/sec   95    283 KBytes       
[  4]   8.00-9.00   sec   108 MBytes   902 Mbits/sec  118    214 KBytes       
[  4]   9.00-10.00  sec   109 MBytes   912 Mbits/sec   57    305 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   955 MBytes   801 Mbits/sec  430             sender
[  4]   0.00-10.00  sec   952 MBytes   798 Mbits/sec                  receiver

iperf Done.
```

**Yaygın Seçenekler:**

*   `-s`: Sunucu modunda çalıştırır.
*   `-c <sunucu_ip>`: İstemci modunda çalıştırır ve belirtilen sunucuya bağlanır.
*   `-p <port>`: Kullanılacak port numarasını belirtir (iperf3 için varsayılan 5201).
*   `-u`: UDP testi yapar.
    *   `-b <hız>`: UDP için hedef bant genişliğini belirtir (örn. `100M` = 100 Mbit/s).
*   `-P <sayı>`: Paralel istemci akışı (stream) sayısını belirtir.
*   `-t <saniye>`: Test süresini belirtir (varsayılan 10 saniye).
*   `-i <saniye>`: Raporlama aralığını belirtir.
*   `-R`: Test yönünü tersine çevirir (sunucu gönderir, istemci alır - download testi).
*   `--get-server-output`: İstemci tarafında sunucu tarafının raporunu da gösterir.

## `speedtest-cli`

Bir sunucunun genel internet bağlantı hızını (download/upload ve ping) popüler Speedtest.net hizmetini kullanarak ölçmek için kullanılan bir komut satırı aracıdır.

**Kurulum:**
Genellikle dağıtım depolarında bulunur:
```bash
# Debian/Ubuntu
sudo apt install speedtest-cli

# RHEL/CentOS/Fedora (EPEL veya başka bir depo gerekebilir)
sudo dnf install speedtest-cli 
```
Alternatif olarak, Python pip ile veya doğrudan GitHub'dan (`https://github.com/sivel/speedtest-cli`) indirilebilir, ancak depo sürümü genellikle daha kolaydır.

**Kullanım:**
Basitçe komutu çalıştırın:
```bash
speedtest-cli
```
Komut, en yakın Speedtest.net sunucusunu bulur, ping süresini ölçer, ardından indirme ve yükleme hızlarını test eder ve sonuçları gösterir.

`curl` ile programı hiç indirmeden, tek seferde test edip sonuç almak için (GitHub'daki betiği kullanarak - URL değişebilir):

```
eaydin@eaydin-vt ~ curl -s https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py | python -
```

Bu komut çalıştırdığınızda doğrudan sonucu göreceksiniz ve program diske yazılmamış olacaktır. Eğer kodu saklamak ve tekrar tekrar kullanmak isterseniz `wget` ile indirip sonra Python ile çalıştırabilirsiniz.

```
eaydin@eaydin-vt ~ wget https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py
```

Ardından Python ile test ettiğinizde şöyle bir sonuç görebilirsiniz.

```
eaydin@eaydin-vt ~/devel/calisma $ python speedtest.py 
Retrieving speedtest.net configuration...
Testing from VeriTeknik Bilisim Ltd. (94.103.47.241)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Turksat Uydu Haberlesme Kablo TV ve Isletme A.S. (Ankara) [19.19 km]: 1.912 ms
Testing download speed................................................................................
Download: 817.31 Mbit/s
Testing upload speed................................................................................................
Upload: 589.59 Mbit/s
```
