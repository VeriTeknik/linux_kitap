# netcat (`nc`)

`netcat` (veya kısaca `nc`), TCP ve UDP protokolleri üzerinden ağ bağlantıları kurarak veri okuma ve yazma işlemleri yapabilen, son derece esnek ve güçlü bir komut satırı aracıdır. Basitliği ve çok yönlülüğü nedeniyle "ağların İsviçre çakısı" olarak da adlandırılır. Sistem ve ağ yöneticileri tarafından ağ sorunlarını teşhis etmek, portları test etmek, basit sunucu/istemci uygulamaları oluşturmak, veri transferi yapmak gibi birçok amaçla kullanılır.

**Farklı Sürümler:** Linux dağıtımlarında `netcat`'in farklı implementasyonları bulunabilir:
*   **OpenBSD `nc`:** Birçok dağıtımda bulunan yaygın ve genellikle daha modern özelliklere sahip sürüm.
*   **GNU `netcat`:** Daha eski bir sürüm, bazı seçenekleri farklı olabilir.
*   **`ncat`:** Nmap projesinin bir parçası olan, SSL desteği gibi ek özellikler sunan gelişmiş bir versiyon.
Bu bölümdeki örnekler genellikle OpenBSD `nc` sürümüyle uyumludur, ancak bazı seçenekler (`-k` gibi) veya davranışlar kullandığınız sürüme göre farklılık gösterebilir. `man nc` veya `man ncat` komutları ile sisteminizdeki sürümün detaylarına bakabilirsiniz.

`netcat`'in temel işlevi TCP veya UDP kullanarak ağdan veri okuyup yazmaktır.

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

Eğer bağlantıyı TCP yerine UDP olarak test etmek isteseydik, `-u` parametresini kullanmamız yeterli olacaktı:
```bash
nc -u <hedef_sunucu> <udp_port>
```

## Port Tarama (`-z`)

Bir veya daha fazla portun açık olup olmadığını (bir servisin o portu dinleyip dinlemediğini) hızlıca kontrol etmek için `-z` (zero-I/O mode) seçeneği kullanılır. Bu modda `nc`, veri göndermeden sadece bağlantı kurmaya çalışır ve sonucu bildirir.

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

Eğer güvenlik duvarı (firewall) bağlantıyı engellemiyorsa ve hedef portta bir servis dinlemiyorsa, genellikle "Connection refused" (Bağlantı reddedildi) hatası alınır:
```bash
$ nc -vz 192.168.16.30 81
nc: connect to 192.168.16.30 port 81 (tcp) failed: Connection refused
```
Eğer güvenlik duvarı paketleri sessizce düşürüyorsa (DROP), `nc` bir süre yanıt bekleyip zaman aşımına uğrayabilir. `-w <saniye>` ile zaman aşımı süresi ayarlanabilir.

Port aralığı taraması da yapılabilir:

```
[root@emre ~]# nc -z 192.168.16.30 20-90
Connection to 192.168.16.30 22 port [tcp/ssh] succeeded!
Connection to 192.168.16.30 84 port [tcp/ctf-veriteknik] succeeded!
```

Bu sunucu üzerinde TCP 22 ve 84 portları cevap veriyormuş. Farkındaysanız portlara karşılık gelen servisler de parantez içerisinde yazıyor. Port 22 üzerinde SSH çalıştığını söylemiş netcat, ancak 84 portu için **ctf-veriteknik** yazıyor?

## Port Numaraları ve İlgili Servisler

TCP ve UDP iletişiminde kullanılan port numaraları genel bir standarda ulaşmış denilebilir. Örneğin 80 portu HTTP için kullanılmaktadır. Ancak tahmin edeceğiniz üzere bu genel bir kanıdır ve her sunucu 80 portundan HTTP yayını yapmak zorunda değildir. Sunucular bu _genel kanı_'dan haberdar olmadıkları için, çoğunlukla kullanılan standart portların bir listesine ihtiyaç duyarlar. Aslında bu liste daha ziyade sunucuyu yöneten/kullanan kişinin ilgili portlarla karşılaştığında genellikle ne işe yaradıklarını anlayabilmelerini sağlar. Örneğin bir port taramasında veya sistemin genel sağlığı incelendiğinde 873. port üzerinde çalışan bir servis dikkatimizi çekerse, bunun genellikle **rsync** için kullanıldığını bilemeyebiliriz. Bunun için GNU/Linux sunucular üzerinde özel bir dosyada TCP/UDP port sayılarına karşılık gelen _okunabilir_ servis isimleri yer almaktadır. Çoğunlukla bu dosya `/etc/services` yolunda bulunur. Netcat gibi programlar bir porta karşılık servis ismi sağlarken, buradaki karşılıklarını dikkate alırlar. Bu dosyadaki 84 TCP portuna karşılık gelen değeri değiştirirsek \(ya da, eğer yoksa eklersek\) artık netcat buradaki değeri dikkate alacaktır. Yukarıdaki örnekte de 84 TCP için ctf-veriteknik değerini yazdığımız için bu sonucu aldık.

Buradan şu çıkarımı yapabiliriz: "O zaman bu dosya üzerinde 22 TCP'ye karşılık gelen değeri **ssh** yerine **http** yapsaydık, farklı görünecekti". Gerçekten de öyle. Bu yüzden port taramalarında SSH değeri görsek de, bunu netcat \(veya benzeri programlar\) tablolara bakarak belirtmektedir, 22. porta veri gönderip aldığı cevabı analiz edip hangi servisin çalıştığını belirtmemektedir. Dolayısıyla eğer tarama yaptığınız sunucu 22. port üzerinde SSH yerine FTP çalıştırıyorsa, tarama sonucunuz sizi yanıltabilir.

## Çıkış Portunu Belirtmek

Normal şartar altında, uzak bir sunucuya bağlanırken, sunucunun portu belliyken, istemcinin _çıkış portu_ belirtilmeyebilir. Örneğin bir internet sayfasına 80. porttan bağlanmanız, istemcinin çıkış için kendi üzerindeki 80. portu kullanacağı anlamına gelmez. İstemci _çıkış portunu_ belirtmediği sürece, işletim sistemi yüksek sayılı port numaraları arasından uygun olanı kendisi belirler ve istemciyi bu port üzerinden veri göndermeye yönlendirir. Bu portlara _Ephemeral Ports_ denilmektedir. Ancak ağımızda ve sistemimizde test yaparken, netcat'in _çıkış portunu \(source port\)_ belirleyebiliriz. Bunu -p parametresiyle yaparız. Böylece örneğin sistemimizin 9091 portundan çıkış yapabilmesinin mümkün olup olmadığını test etmek için, çıkış portu olarak belirtmek yeterli olacaktır.

```
nc -p 9091 192.168.16.30 80
```

Yukarıdaki komut, 192.168.16.30 sunucusunun 80 portuna, kendi 9091 portundan çıkış yaparak bağlantı sağlamaktadır.

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

Eğer client tarafında netcat programını sonlandırırsak, client server'a TCP bağlantısını sonlandıracağı bilgisini göndereceği için, server da sonlanacaktır. Eğer sunucunun, bir istemci bağlantısı kapandıktan sonra yeni bağlantıları kabul etmeye devam etmesini istiyorsak, `-k` (keep listening) seçeneği kullanılır (Bu seçenek OpenBSD `nc`'de bulunur, diğer sürümlerde farklı olabilir veya olmayabilir).
```bash
sudo nc -lk 84
```

## Kullanım Senaryoları

**Uyarı:** Aşağıdaki dosya transferi ve veri yönlendirme örneklerinde `netcat` veriyi **şifrelemeden** gönderir. Güvenilmeyen ağlarda hassas veriler için bu yöntemler kullanılmamalıdır. Bunun yerine SSH (scp, sftp, rsync over ssh) veya `socat` gibi SSL/TLS destekli araçlar tercih edilmelidir. `openssl` ile şifreleme örneği aşağıda gösterilmiştir.

Netcat'in temel olarak ne iş yaptığını gördük. Başta bahsettiğimiz gibi aslında çok basit bir iş yapıyor, ancak sunucu/network yapınızda test sağlamak için kullanışlı olurken, standart girdi/çıktı yönlendirmeleriyle birçok problemi hızla çözmenize olanak sağlıyor.

Örneğin iki sunucunuz arasında VPN tünelleri veya güvenliik duvarları ayarlamışsanız, kurguladığınız yapıda iletişim problemi olup olmadığını, iki tarafta netcat ile server/client testleri yaparak tespit edebilirsiniz.

Sık kullanılan bu denetleme mekanizması dışında, birtakım farklı problemlere de çözüm sağlamaktadır. Bu bölümde bizim sıkça kullandığımız ve internette karşılaştığımız enteresan kullanım senaryolarından kısaca bahsedeceğiz.

### Dosya Transferi

İki sunucu arasında hızlıca dosya transfer etmek istediğinizde, çeşitli sebeplerden dolayı SFTP, FTP, HTTPD gibi standart protokolleri kullanamayabilirsiniz. Bazen ortamdaki güvenlik duvarları buna izin vermez, bazen boş yere makinalara bu araçların sunucu ve istemcilerini kurmak istemeyebilirsiniz. Bunun için dosyayı gönderecek tarafta netcat'i client olarak, dosyayı alacak tarafta da netcat'i server olarak çalıştırıp dosya transferini gerçekleştirebilirsiniz.

Örneğin `yedekler.tar.gz` dosyasını A sunucusundan B sunucusuna aktarmak istersek, ve bu sunucular arasında 9955 portunda iletişim sağlamamız mümkünse, önce B sunucusunda 9955 portunu dinleyip, gelen her şeyi `yedekler.tar.gz` isimli bir dosyaya yönlendirmemiz gerekir.

```
root@B.server ~ # nc -l 9955 > yedekler.tar.gz
```

Şimdi A sunucusundan dosyayı gönderebiliriz.

```
eaydin@A.client ~ $ nc -w 5 B.server < yedekler.tar.gz
```

Farkındaysanız A üzerinde B.server yazdık. Bu B'ni hostname'i veya IP adresi olmalıdır. B'nin ise A'nın IP adresini bilmesine gerek yok.

A üzerinde çalıştırdığımız komuttaki `-w` parametresi, timeout süresini belirtmektedir. Ayrıca dosya transferi tamamlandığı anda, B tarafı bağlantıyı kapatır, çünkü `-k` parametresiyle çalıştırmadık.

### Standart Çıktı Yönlendirme ile Veri Transferi

Biraz önceki örneğimizde, A sunucusu üzerinde yedekler.tar.gz dosyası hali hazırda mevcuttu. Bazı durumlarda bu dosya mevcut olmayabilir. Örneğin yedekler.tar.gz'nin bir MySQL veritabanı çıktısı olmasını isteyebiliriz, ancak MySQL dump'ını disk üzerine yazmamız mümkün olmayabilir. Çok büyük veritabanlarının yedeği alınırken, hem çalışan disk üzerinde gereksiz yazma işlemine sebep olmak istemeyebiliriz, hem de disk üzerinde büyük veritabanıyla aynı boyutlarda yer olmayabilir. Bu gibi durumlarda, veritabanının çalıştığı sunucuda \(A sunucusu\) mysqldump \(veya benzer komut çıktısını\) doğrudan netcat ile farklı sunucuya aktarabiliriz.

Yine istemci tarafında, ilk örneğimizde olduğu gibi belirli bir portu dinleyen ve gelen veriyi standart çıktıya yönlendiren bir netcat sunucusuna ihtiyacımız var.

```
root@B.server ~ # nc -l 9955 > database.sql
```

Şimdi MySQL'in çalıştığı tarafta mysqldump komutunu olduğu gibi netcat'e yönlendiriyoruz. \(Burada kullanıcı adı ve şifre vermek yerine, my.cnf dosyasını ayarlayabilirsiniz.\)

```
eaydin@A.client ~ $ mysqldump -u kullanici_adi -p veritabani_ismi | nc B.server 9955
```

### Veriyi Sıkıştırarak Göndermek

Eğer veri çok büyükse, ağ üzerinde hızlanma sağlamak isterseniz ve veri sıkıştırılmaya uygunsa, araya gzip koyarak sıkıştırma sağlayabilirsiniz. Bu durumda karşı tarafta sıkıştırılmış veriyi açmanız gerekecektir. Biraz önceki mysqldump örneğinden gidecek olursak, önce dump'ı alıp, sonra veriyi sıkıştırıp, sonra da netcat ile gönderebiliriz. Ancak bu sefer veriyi alan tarafta da gzip ile "açma" işlemi yapmamız gerekecektir.

```
root@B.server ~ # nc -l 9955 | gzip -d -c > database.sql
```

Burada gzip'in -d parametresi, sıkıştırılmış veriyi açacağımız \(_decompress_\) anlamına gelmekte. -c ise veriyi standart çıktıya yönlendirmektedir. Bunun yerine doğrudan dosya ismi de verebilirdik.

Veriyi gönderen taraf da artık sıkıştırarak gönderebilir.

```
eaydin@A.client ~ $ mysqldump -u kullanici_adi -p veritabani_ismi | gzip | nc B.server 9955
```

### Veriyi Şifreleyip Göndermek

Buraya kadar yaptığımız örneklerde iki netcat arasındaki veri ağdaki başkası tarafından dinlenilirse anlaşılabilir durumda olacaktır. Bu durum güvenlik açısında tehdit oluşturuyorsa, veriyi doğrudan openssl ile şifreleyip gönderebiliriz.

Dosyayı alacak \(sunucu\) tarafında aşağıdaki gibi bir komut dizisi yazılabilir.

```
root@B.server ~ # nc -l 9955 | gzip -d -c | openssl enc -aes-256-cbc -pass pass:GIZLI_S1FR3 -d > guvenli_transfer.sql
```

Gönderiren de ters sırada işlemleri yapmak gerekecektir.

```
eaydin@A.client ~ $ mysqldump -u kullanici_adi -p veritabani_ismi | openssl enc -aes-256-cbc -pass pass:GIZLI_S1FR3 -e | gzip | nc B.server 9955
```

Veri MySQL sunucusu üzerinde önce şifreleniyor (`openssl enc -e`), sonra sıkıştırılıyor (`gzip`), ardından netcat ile diğer sunucuya gönderiliyor. Diğer sunucu önce sıkıştırılmış veriyi açıyor (`gzip -d`), ardından şifreyi çözüyor (`openssl enc -d`) ve dosyayı yazıyor.

Burada sıkıştırma işlemini yapmak zorunda değiliz tabii ki, özellikle birden fazla işlemin uç uca eklenmesine güzel örnek sağladığı için ve işlemlerin sırasının önemini vurgulamak için yaptık. Şifreleme için `GIZLI_S1FR3` yerine çok daha güçlü bir parola veya anahtar dosyası kullanılmalıdır.
