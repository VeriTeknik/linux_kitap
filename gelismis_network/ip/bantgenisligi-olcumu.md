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
*  Çıktıyı netcat ile 192.168.16.40'ın 5566 portuna yönlendir

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



