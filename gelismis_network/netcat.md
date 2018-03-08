# netcat

Netcat aslında çok az iş yapan, ancak yaptığı işi hem çok basit hem de çok verimli yaptığı için sistem yöneticilerinin, ağ yöneticilerinin ve hatta programcıların vazgeçilmez araçlarından biri olmuştur. Az tanımlı işi iyi yapmasıyla da Unix felsefesinin en iyi temsilcilerindendir.

Yaptığı iş sadece şu: TCP ve UDP kullanarak ağdan veri okuyup yazabilir.

Bu kadar temel bir işi yapan program doğru kullanıldığında birçok ağ problemini teşhis etmede yardımcı olabilir, dosya transferli yapmanıza olanak sağlayabilir, basit proxy'ler kurmanıza veya port taramaları yapmanıza müsaade edebilir.

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
 ✘ eaydin@k9 ~ $ nc -vz google.com 81
 
```

Normal şartlar altında bir TCP bağlantısı karşını karşı taraf reddederse bunun sinyalini alırız, ancak firewall'lar özellikle bunu göndermediği için netcat bağlantıyı sonlandırmayıp bekliyor.

Eğer firewall arkasında bulunmayan ancak 81 portundan hizmet sunmayan bir sunucuya tarama isteği gönderirsek aşağıdakine benzer bir sonuç alırız.

```
eaydin@k9 ~ $ nc -vz 192.168.16.30 81
nc: connectx to 192.168.16.30 port 81 (tcp) failed: Connection refused
```



