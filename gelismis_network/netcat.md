# netcat

Netcat aslında çok az iş yapan, ancak yaptığı işi hem çok basit hem de çok verimli yaptığı için sistem yöneticilerinin, ağ yöneticilerinin ve hatta programcıların vazgeçilmez araçlarından biri olmuştur. Az tanımlı işi iyi yapmasıyla da Unix felsefesinin en iyi temsilcilerindendir.

Yaptığı iş sadece şu: TCP ve UDP kullanarak ağdan veri okuyup yazmak.

Bu kadar temel bir işi yapan program doğru kullanıldığında birçok ağ problemini teşhis etmede yardımcı olabilir, dosya transferi yapmanıza olanak sağlayabilir, basit proxy'ler kurmanıza veya port taramaları yapmanıza müsaade edebilir.

Sözü daha fazla uzatmadan birkaç örnekle başlayalım ve netcat'in yapabileceklerini örnekler üzerinden görelim.

## Bağlantı Kontrolü

Linux sunucunuzun herhangi başka bir sunucuya bağlantıyı sağlıklı gerçekleştirip gerçekleştiremediğini test edebilirsiniz. Örneğin elimizdeki sunucu veriteknik.com adresinin 81 portuna TCP üzerinden bağlantı sağlayabiliyor mu kontrol edelim.

```
eaydin@k9 ~ $ nc veriteknik.com 81
eaydin@k9 ~ $
```

Yukarıdaki komut sonucunda netcat herhangi bir bağlantı kuramadığı için sonlanmaktadır. Benzer testi açık olduğunu bildiğimiz bir sunucunun ssh portu üzerinde gerçekleştirelim.

```
eaydin@k9 ~ $ nc 192.168.16.30 22
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.4
```

Doğrudan bizi ssh server karşıladı, ve bir mesaj gönderdi. Bundan sonra nc sizden girdi bekler, yazdığınız girdiyi ise karşı tarafa gönderecektir. Örneğin **a** karakterini gönderelim. \("a" yazıp Enter tuşuna basıyoruz\)

```
eaydin@k9 ~ $ nc 192.168.16.30 22
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.4
a
Protocol mismatch.
eaydin@k9 ~ $
```

SSH server bize protokolün uyuşmadığını söyledi ve bağlantıyı sonlandırdı. Kısacası SSH ile aynı dili konuşamadık, ancak burada test ettiğimiz işlem, ilgili sunucunun 22. portuna TCP ile erişim sağlayabildiğimiz ve burada bizi bir servisin karşıladığı oldu.

Aynı işlemi veriteknik.com için 80 portuna da yapabiliriz. Normal şartlar altında bir web sunucu \(Apache, nginx vs.\) bağlantı sağlandıktan sonra tıpkı SSH'ta yaptığımız gibi bir paket gelmesini bekler, bu pakede göre de bir cevap gönderir. Eğer başarıyla 80 portundan bağlantı sağlarsak, yine **a** verisini gönderip gelen cevaba bakacağız.

```
eaydin@k9 ~ $ nc veriteknik.com 80
a
HTTP/1.1 400 Bad Request
Date: Thu, 08 Mar 2018 14:54:50 GMT
Server: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips
Content-Length: 226
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
</body></html>
```

Burada bizi Apache 2.4.6 web sunucusu karşıladı ve gönderdiğimiz pakedi anlamadığını ifade eden 400 Bad Request cevabını HTML olarak döndürdü.

Eğer bağlantıyı TCP olarak değil de, UDP olarak sağlamak isteseydik, -u parametresini kullanmamız yeterli olacaktı.

## Port Tarama

Biraz önce bir servisin çalışıp çalışmadığını kontrol ettik. Ancak bunun için hem tek bir port belirtmemiz gerekti, hem de bazı durumlarda veri göndermemiz gerekti. Örneğin ssh ile kurduğumuz bağlantıda ssh server doğrudan bize protokol bilgisini gönderdiği için bağlantı sağlayabildiğimizi anlayabildik, ancak Apache bizden bir input bekledi. Yine de benim TCP bağlantısını başarılı şekilde tamamlayıp tamamlayamadığımı anlayabilmem gerekiyor. Bunun için basit bir -z parametresi sağlıyor netcat.

```
eaydin@k9 ~ $ nc -z google.com 80
Connection to google.com port 80 [tcp/http] succeeded!
```

Eğer daha detaylı sonuç almak isterseniz, verbose modunu açabilirsiniz.

```
eaydin@k9 ~ $ nc -vvvvvz google.com 80
found 0 associations
found 1 connections:
     1:    flags=82<CONNECTED,PREFERRED>
    outif en0
    src 192.168.15.48 port 52114
    dst 216.58.214.206 port 80
    rank info not available
    TCP aux info available

Connection to google.com port 80 [tcp/http] succeeded!
```

Burada isteği gönderdiğimiz ağ kartı cihazının adı, IP adresleri ve kullanılan portlar gibi bilgiler yer alıyor.

Eğer port taramasını yaptığınız servis bir firewall arkasındaysa, bağlantının başarısız olduğu bilgisi gelmeyebilir, bu yüzden bağlantınız havada kalabilir. Örneğin Google'ın 81. portunu tarayacak olursak, cevap gelmeyecektir, ancak netcat de cevap gelmesini bekleyecektir.

```
eaydin@k9 ~ $ nc -vz google.com 81
```

Normal şartlar altında bir TCP bağlantısı karşını karşı taraf reddederse bunun sinyalini alırız, ancak firewall'lar özellikle bunu göndermediği için netcat bağlantıyı sonlandırmayıp bekliyor.

Eğer firewall arkasında bulunmayan ancak 81 portundan hizmet sunmayan bir sunucuya tarama isteği gönderirsek aşağıdakine benzer bir sonuç alırız.

```
eaydin@k9 ~ $ nc -vz 192.168.16.30 81
nc: connectx to 192.168.16.30 port 81 (tcp) failed: Connection refused
```

Belirtilecek portları aralık halinde de verebiliriz. Örneğin sunucunun 20-90 portları aralığını taramak istersek aşağıdaki gibi bir sonuç elde ederiz.

```
[root@emre ~]# nc -z 192.168.16.30 20-90
Connection to 192.168.16.30 22 port [tcp/ssh] succeeded!
Connection to 192.168.16.30 84 port [tcp/ctf-veriteknik] succeeded!
```

Bu sunucu üzerinde TCP 22 ve 84 portları cevap veriyormuş. Farkındaysanız portlara karşılık gelen servisler de parantez içerisinde yazıyor. Port 22 üzerinde SSH çalıştığını söylemiş netcat, ancak 84 portu için **ctf-veriteknik** yazıyor?

## Port Numaraları ve İlgili Servisler

TCP ve UDP iletişiminde kullanılan port numaraları genel bir standarda ulaşmış denilebilir. Örneğin 80 portu HTTP için kullanılmaktadır. Ancak tahmin edeceğiniz üzere bu genel bir kanıdır ve her sunucu 80 portundan HTTP yayını yapmak zorunda değildir. Sunucular bu _genel kanı_'dan haberdar olmadıkları için, çoğunlukla kullanılan standart portların bir listesine ihtiyaç duyarlar. Aslında bu liste daha ziyade sunucuyu yöneten/kullanan kişinin ilgili portlarla karşılaştığında genellikle ne işe yaradıklarını anlayabilmelerini sağlar. Örneğin bir port taramasında veya sistemin genel sağlığı incelendiğinde 873. port üzerinde çalışan bir servis dikkatimizi çekerse, bunun genellikle **rsync** için kullanıldığını bilemeyebiliriz. Bunun için GNU/Linux sunucular üzerinde özel bir dosyada TCP/UDP port sayılarına karşılık gelen _okunabilir_ servis isimleri yer almaktadır. Çoğunlukla bu dosya `/etc/services` yolunda bulunur. Netcat gibi programlar bir porta karşılık servis ismi sağlarken, buradaki karşılıklarını dikkate alırlar. Bu dosyadaki 84 TCP portuna karşılık gelen değeri değiştirirsek \(ya da, eğer yoksa eklersek\) artık netcat buradaki değeri dikkate alacaktır. Yukarıdaki örnekte de 84 TCP için ctf-veriteknik değerini yazdığımız için bu sonucu aldık.

Buradan şu çıkarımı yapabiliriz: "O zaman bu dosya üzerinde 22 TCP'ye karşılık gelen değeri **ssh** yerine **http** yapsaydık, farklı görünecekti". Gerçekten de öyle. Bu yüzden port taramalarında SSH değeri görsek de, bunu netcat \(veya benzeri programlar\) tablolara bakarak belirtmektedir, 22. porta veri gönderip aldığı cevabı analiz edip hangi servisin çalıştığını belirtmemektedir. Dolayısıyla eğer tarama yaptığınız sunucu 22. port üzerinde SSH yerine FTP çalıştırıyorsa, tarama sonucunuz sizi yanıltabilir.

## Port Dinleme

Biraz önce 84. portu taradığımızda en azından bir servisin dinlediğini öğrendik. Aslında bu makinada da netcat'e 84 TCP portunu _dinlemesini_ söyledik. Yani bir makina 84. portta server görevi görürken, diğer makina bu porta veri gönderiyordu \(client\).

Netcat ile dinleme işlemleri _listen_'ın kısaltması olan **-l** parametresiyle gerçekleştirilir.

Örneğin bir sunucu üzerinde 84 TCP portunu dinlemek istersek, aşağıdaki şekilde netcat'i çalıştırdığımızda, veri gelmesini bekleyecektir.

**NOT:** Dinleme işlemleri için root yetkisi gerekmektedir.

```
root@server ~ # nc -l 84
```

Şimdi başka bir makinadan bu porta veri gönderirsek, verinin karşı tarafa ulaştığını görebiliriz.

```
[eaydin@client ~]$ nc 192.168.16.30 84
test1
test2
```

Burada satırları yazarken ENTER tuşu ile gönderilmesini söylemiş olduk. Netcat satır sonlarını TCP paketleri halinde karşı tarafa gönderir. Sunucu tarafında bu verilerin ulaştığını görebiliriz.

```
root@server ~ # nc -l 84
test1
test2
```

Eğer client tarafında netcat programını sonlandırırsak, client server'a TCP bağlantısını sonlandıracağı bilgisini göndereceği için, server da sonlanacaktır. Eğer server'ın sürekli dinlemesini istiyorsak, yani bağlantıların TCP Fin paketiyle sonlandırıldığında bile server'ın yeni bağlantı beklemesini istiyorsak, -k parametresiyle başlatılması gerekir.

```
root@server ~ # nc -lk 84
```



