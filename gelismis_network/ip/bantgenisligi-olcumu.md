# Bantgenişliği Ölçümü

Bu bölümde iki sunucu arasındaki ağ kullanımının test edilmesi için birkaç teknikten bahsedeceğiz. Bu tekniklerin bazıları GNU/Linux dağıtımlarının çoğunda halihazırda yüklü olan araçları kullanırken, bazıları yeni programların yüklenmesini gerektirebilir.

## dd ve netcat

Daha önce gördüğümüz iki program. dd sistemlerde standart olarak gelen bir araç olmasına rağmen, netcat'i genellikle yüklemek gerekir. Ancak daha önce belirttiğimiz gibi, netcat günlük kullandığınız araçlar arasında olmazsa olmazlardan olduğu için genellikle sunuculara yüklemekte fayda var.

Diyelim ki 192.168.16.30 \(client\) makinasından 192.168.16.40 \(server\) sunucusuna veri transferinin hızını test etmek istiyoruz. Bu durumda 192.168.16.40 üzerinde belirli bir portu dinleyen bir netcat process'i çalıştırırız.

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

Bu testlerin tamamının TCP ile gerçekleştirildiğini hatırlatalım. Eğer UDP test etmek isterseniz, hem server hem de client tarafında `-u` parametresini kullanmanız gerekir.

## iperf

Bağlantı testi için standartlaşmış araçlardan birisi. Repolarda iki versiyonunu görebilirsiniz, iperf ve iperf3. Kullandıkları default portlar dahil bir takım farklılıklar var. Aşağıdaki ölçümleri iperf3 ile gerçekleştirdik ancak eski versiyonu ile de benzer sonuçlar elde edebilirsiniz. Sadece iki tarafta da \(server ve client\) aynı versiyonu kullanmaya özen göstermekte fayda var \(teknik olarak buna mecbur olmasanız da\).

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



