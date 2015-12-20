# dig

Daha önce "Bir sayfanın İnternetteki Serüveni" başlığında karşılaştığımız ```dig``` komutu (*Domain Information Groper*), bir IP adresi için DNS sorgusu yapmaya yarar. Sistem yöneticilerinin sıkça kullandığı bu aracın bilinmesi gereken birkaç parametresi bulunur.

## Temel Kullanım ve Sonuçları

En temel kullanım, bir alan adının DNS kayıtlarını sorgulamak için şu şekilde gerçekleştirilir.

```bash
eaydin@dixon ~ $ dig veritech.net

; <<>> DiG 9.9.5-3ubuntu0.6-Ubuntu <<>> veritech.net
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 54367
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;veritech.net.			IN	A

;; ANSWER SECTION:
veritech.net.		14399	IN	A	94.103.32.32

;; Query time: 1122 msec
;; SERVER: 127.0.1.1#53(127.0.1.1)
;; WHEN: Sat Dec 19 18:44:04 EET 2015
;; MSG SIZE  rcvd: 57
```

Yukarıdaki çıktı, veritech.net adresinin A kayıtlarını göstermektedir. İlgilendiğimiz kısım ```ÀNSWER SECTION``` ile başlayan kısımdır. Zaten bu kısımın altındaki satırın başında ```;``` işareti olmadığını görebilirsiniz. Noktalı virgül işaretleri, ilgili satırın bir açıklama satırı olduğunu ifade etmesi bakımından yazılmaktadır.

Cevaptaki diğer bölümler, programın versiyonu hakkında bilgi vermekte, sorgu hakkında bir takım istatistikler paylaşmaktadır.

```QUESTION SECTION``` kısmında sorgunun aslında ```veritech.net IN A``` için yapıldığı görülebilir. Bu yüzden cevap olarak ```veritech.net. 14399 IN A 94.103.32.32``` şeklinde gelmiştir cevap. Kısacası **A** kaydı sorulmuş, cevap olarak da bu bilgi gelmiştir.

## Sorgulanabilecek Kayıt Tipleri

dig ile DNS kaydı sorgularken parametre belirtmezsek, doğrudan **A** kaydı sorgulanır. Örneğin MX kaydı sorgulamak isteseydik, bunu özellikle belirtmemiz gerekecekti.

```bash
eaydin@dixon ~ $ dig -t mx veritech.net

; <<>> DiG 9.9.5-3ubuntu0.6-Ubuntu <<>> mx veritech.net
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 20388
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;veritech.net.			IN	MX

;; ANSWER SECTION:
veritech.net.		14399	IN	MX	5 posta.veriportal.com.

;; Query time: 173 msec
;; SERVER: 127.0.1.1#53(127.0.1.1)
;; WHEN: Sat Dec 19 19:20:17 EET 2015
;; MSG SIZE  rcvd: 77
```

Yukarıdaki örnekten ```-t mx``` ile **MX** kaydını tanımlamış oluyoruz. Kullanım kolaylığı bakımından, ```-t``` parametresini belirtmenize gerek yoktur. Kısacası doğrudan ```dig mx veritech.net``` komutu yukarıdakiyle aynı sonucu verecektir.

Ayrıca, DNS kayıtları sorgularken belirtilen kayıt tipleri, büyük/küçük karakter duyarlı değildir.

Sorguladımız alanadı hakkındaki bütün kayıtlara erişmek isteseydik, tip olarak ```ANY``` tanımlayabilirdik.

```bash
eaydin@dixon ~ $ dig any veritech.net

; <<>> DiG 9.9.5-3ubuntu0.6-Ubuntu <<>> any veritech.net
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 13298
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;veritech.net.			IN	ANY

;; ANSWER SECTION:
veritech.net.		14399	IN	SOA	ns1.rackdc.com. hostmaster.veritech.net. 2015120202 14400 3600 1209600 86400
veritech.net.		14399	IN	NS	ns2.rackdc.com.
veritech.net.		14399	IN	NS	ns1.rackdc.com.
veritech.net.		14399	IN	MX	5 posta.veriportal.com.
veritech.net.		14399	IN	A	94.103.32.32

;; Query time: 421 msec
;; SERVER: 127.0.1.1#53(127.0.1.1)
;; WHEN: Sat Dec 19 19:40:14 EET 2015
;; MSG SIZE  rcvd: 183
```

## Sorgulanacak Sunucuyu Tanımlamak

Yukarıdaki sonuçlara bakıldığında ```;; SERVER: 127.0.1.1#53(127.0.1.1)``` şeklinde bir satır görülebilir. Bu satır, sorgulanan sonuçların hangi sunucudan geldiği bilgisini paylaşmaktadır.

**NOT:** Farkındaysanız buradaki adres ```127.0.0.1``` değil, ```127.0.1.1``` şeklindedir. Bu, bazı Debian türevi dağtımlardaki programların buglarını gidermek için getirilmiş bir çözümdür. Bu bug hakkında detaylı bilgi için Debian Bug Reports içindeki #316099[](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=316099) numaralı olana bakılabilir.

dig programına özellikle belirtmediğimiz takdir de, ```/etc/resolv.conf``` dosyasında tanımlı nameserver'ları sırayla deneyecektir. Ancak alanadı problemlerini rahat tespit etmek için programa sorguları özellikle tanımladığımız DNS sunucusuna göndermesini söyleyebiliriz.

Aşağıdaki sorgu, veritech.net kayıtlarını 8.8.8.8 üzerindeki Google DNS'e sormaktadır.

```bash
eaydin@dixon ~ $ dig veritech.net @8.8.8.8

; <<>> DiG 9.9.5-3ubuntu0.6-Ubuntu <<>> veritech.net @8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 27998
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;veritech.net.			IN	A

;; ANSWER SECTION:
veritech.net.		14399	IN	A	94.103.32.32

;; Query time: 125 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Sat Dec 19 19:49:38 EET 2015
;; MSG SIZE  rcvd: 57
```

Artık sonuçlardaki ```SERVER``` ifadesinin değiştiğini görebilirsiniz.

## DNS Yolunu Takip Etmek

Bir alanadının çözümlenmesi için hangi yollardan geçtiği, nerede problem oluştuğunu veya oluşabileceğini tespit etmek için faydalı olabilir. Örneğin veriteknik.com.tr adresinin DNS çözümlemesinde nasıl bir yol izlendiğini takip etmek için ```+trace``` parametresini kullanabiliriz.

```bash
eaydin@dixon ~ $ dig veriteknik.com.tr +trace

; <<>> DiG 9.9.5-3ubuntu0.6-Ubuntu <<>> veriteknik.com.tr +trace
;; global options: +cmd
.			15458	IN	NS	j.root-servers.net.
.			15458	IN	NS	c.root-servers.net.
.			15458	IN	NS	l.root-servers.net.
.			15458	IN	NS	i.root-servers.net.
.			15458	IN	NS	b.root-servers.net.
.			15458	IN	NS	g.root-servers.net.
.			15458	IN	NS	k.root-servers.net.
.			15458	IN	NS	a.root-servers.net.
.			15458	IN	NS	d.root-servers.net.
.			15458	IN	NS	e.root-servers.net.
.			15458	IN	NS	h.root-servers.net.
.			15458	IN	NS	m.root-servers.net.
.			15458	IN	NS	f.root-servers.net.
.			15458	IN	RRSIG	NS 8 0 518400 20151229050000 20151219040000 62530 . Z4xO7IiTUisysfpLALocUhTyvGYRdW2F8QH7YWbhs8eRdpYX66zhq2zG dlInr7/svswd7fJpYBCsZMkDEYUobWF2Rb5C5yFJHPzXQvNiTypjieiv arEpCk3EIdySO8ccHves/1eOK3vVTU6yjV/4GZlkmdOArTnHER4IHjnH lMA=
;; Received 397 bytes from 127.0.1.1#53(127.0.1.1) in 980 ms

tr.			172800	IN	NS	ns1.nic.tr.
tr.			172800	IN	NS	ns2.nic.tr.
tr.			172800	IN	NS	ns3.nic.tr.
tr.			172800	IN	NS	ns4.nic.tr.
tr.			172800	IN	NS	ns5.nic.tr.
tr.			86400	IN	NSEC	trade. NS RRSIG NSEC
tr.			86400	IN	RRSIG	NSEC 8 1 86400 20151229170000 20151219160000 62530 . gQLhyuxvM0ZTESagT7Ysv5annQgU3OrHrn8bOq3E+fZ/aSp9wdhhhQMx E2Bgmy2ZD+XLm9R216lVaGcRP+EBHbRGJukEpw5LpzCvn5ywuGsbVq+S dDJtdyZxbiwIoXZneESCeKNitBLAPAEaf/b8g3t8IdGwvLsn2sPboWqt Qag=
;; Received 406 bytes from 198.97.190.53#53(h.root-servers.net) in 5809 ms

veriteknik.com.tr.	43200	IN	NS	ns1.rackdc.com.
veriteknik.com.tr.	43200	IN	NS	ns2.rackdc.com.
;; Received 92 bytes from 193.140.100.200#53(ns4.nic.tr) in 818 ms

veriteknik.com.tr.	14400	IN	A	94.103.32.80
veriteknik.com.tr.	14400	IN	NS	ns1.rackdc.com.
veriteknik.com.tr.	14400	IN	NS	ns2.rackdc.com.
;; Received 196 bytes from 94.103.32.130#53(ns1.rackdc.com) in 16 ms
```

Yukarıdaki çıktıyı inceleyecek olursak:

1. Önce dig programı, kendi sistemimizde tanımlı kök sunucuları öğreniyor. Bu sunuculara veriteknik.com.tr adresine nasıl gidileceğini soruyor. Kök sunucuların adreslerini kendi bilgisayarımızdaki veritabanından öğrendik. Kök sunucular bize bu bilginin nic.tr'de olduğunu söyledi çünkü **.tr** uzantılı alanadlarının nic.tr tarafından yönetildiği biliniyor. Sonuçta ilgili IP adreslerini veritabanından verdi.
2. h.root-servers.net kök sunucusundan aldığımız bilgiye göre nic.tr'nin 5 tane sunucusu var. Bu sunuculara veriteknik.com.tr adresinin nerede bulunduğunu sorduk. İçlerinden biri bize ns1.rackdc.com ve ns2.rackdc.com adreslerine sormamız gerektiğini belirtti.
3. ns4.nic.tr sunucusundan aldığımız bilgiye göre, veriteknik.com.tr adresi rackdc ns sunucularındaydı. Bu sunuculardan ns1.rackdc.com ise bize veriteknik.com.tr A kaydının 94.103.32.80 adresinde bulunduğunu belirtti.

Yukarıdaki çıktı bir hiyerarşi içerisinde olmaktadır. Eğer zincirin bir noktası kırılırsa, diğer noktalar da kimi zaman uzun vadede, kimi zaman kısa vadede zarar görebilir. Örneğin veriteknik.com.tr adresine daha önce hiç girmemiş olsaydık ve sunucularımız, ağımız kendisinin IP adresini bilmiyor olsaydı, ona ulaşmak için izleyeceğimiz yolu yukarıdaki gibi kök sunuculara sormamız gerekecekti. Kök sunucular hiçbir zaman doğrudan üzerinde veriteknik.com.tr adresini bulundurmaz. Bu durum tahmin edeceğiniz gibi son derece verimsiz olurdu, bu yüzden bir DNS hiyerarşisi bulunmaktadır. Bu hiyerarşi yüzünden alanadınızın DNS adreslerini değiştirdiğinizde, genellikle bu bilginin diğer DNS sunucularına yayılmasının 24 saat süreceği belirtilir. Bu yayılma sırasında spesifik olarak bir sunucunun güncellenip güncellenmediğini sorgulamak için daha önce öğrendiğimiz @ ile soruglama yöntemini kullanabilirsiniz.

Eğer yukarıdaki sorgu hiyerarşisi içerisindeki bir nokta zarar görürse sistem aksar. Bu aksamanın yaşanmaması için birden fazla DNS sunucusu kullanılır. nic.tr'nin 5 DNS sunucusunun bulunmasının sebebi budur. Böylece birisi zarar gördüğünde diğerine sorgu gönderebiliriz. Ancak 14 Aralık 2015 tarihinde yaşanan bir problem bu durumu sekteye uğrattı. 

**.tr** uzantılı alanadlarının (Top Level Domain, TLD) yönetiminin hangi IP adreslerinde olduğu ve kurum bilgilerini IANA'nın veritabanından öğrenebilirsiniz: https://www.iana.org/domains/root/db/tr.html

Bütün TLD'lerin listesine ve kimin tarafından yönetildiğini öğrenmek için: https://www.iana.org/domains/root/db

## Kök Sunucular

```+trace``` ile DNS yolunu takip ederken, kök sunuculardan sorgulamaya başladık. Bu sunucular Dünya üzerinde 13 tanedir ve 12 bağımsız organizasyon tarafından yönetilirler. İnternette isim çözümlemenin belkemiği sayılan sunucuların IP adresleri bilinir ve bu adreslere sorgular gönderilir. Bu adreslerin karşılığı kök sunucular haricindeki DNS sunucularda "hint file" adı verilen dosyalarda tutulur. Bu dosyanın güncel haline erişmek için aşağıdaki link izlenebilir:

http://www.internic.net/domain/named.root

DNS yolunu izlerken kök sunucuların isimlerini görmüştük. ```a.root-servers.net```, ```b.root-servers.net``` ... gibi isimleri bulunur. Yukarıda linkini verdiğimiz hint dosyası incelenirse, bu sunucuların isimlerinin eskiden farklı olduğu görülebilir. Örneğin e.root-servers.net için "FORMERLY NS.NASA.GOV" ifadesi bulunur. Gerçekten de eskiden kök sunucuların isimleri bulundukları kurumlarla ilişkilendirilmişti ancak sonradan bir isim standardı getirildi. Bugün 13 kök sunucuyu şu kurumlar yönetmektedir.

| **Hostname** | **IP Adresi** | **Yönetici Kurum** |
| -- | -- | -- |
| a.root-servers.net | 198.41.0.4, 2001:503:ba3e::2:30 | VeriSign, Inc. |
| b.root-servers.net | 192.228.79.201, 2001:500:84::b | University of Southern California (ISI) |
| c.root-servers.net | 192.33.4.12, 2001:500:2::c | Cogent Communications |
| d.root-servers.net | 199.7.91.13, 2001:500:2d::d | University of Maryland |
| e.root-servers.net | 192.203.230.10 | NASA (Ames Research Center) |
| f.root-servers.net | 192.5.5.241, 2001:500:2f::f | Internet Systems Consortium, Inc. |
| g.root-servers.net | 192.112.36.4	| US Department of Defense (NIC) |
| h.root-servers.net | 198.97.190.53, 2001:500:1::53 | US Army (Research Lab) |
| i.root-servers.net | 192.36.148.17, 2001:7fe::53 | Netnod |
| j.root-servers.net | 192.58.128.30, 2001:503:c27::2:30 | VeriSign, Inc. |
| k.root-servers.net | 193.0.14.129, 2001:7fd::1 | RIPE NCC |
| l.root-servers.net | 199.7.83.42, 2001:500:3::42 | ICANN|
| m.root-servers.net | 202.12.27.33, 2001:dc3::35 | WIDE Project |

Yukarıdaki listenin güncel haline https://www.iana.org/domains/root/servers adresinden erişilebilir.

### Kök Sunucu IP Değişikliği

Kök sunucular da zaman zaman IP adreslerini değiştirme ihtiyacı hissederler. Bu tip durumlar uzun süre önce anons edilir ve DNS sunucuların hint dosyalarını güncellemeleri için süre tanınır.

Örneğin d-root sunucusu, yani University of Maryland tarafından yönetilen sunucu, 3 Ocak 2013'te IPv4 adresini değiştireceğini, ancak IPv6 adresinin değişmeyeceğini duyurmuştur. Bu duyuruda, geçişten sonra eski IPv4 adresinin 6 ay daha kullanılacağını, ancak sonunda terk edileceğini de belirtmiştir. Dolayısıyla DNS yöneticilerine hint dosyalarını en kısa sürede güncellemelerini tavsiye et

## Daha Fazla Bilgi

dig programı alanadları için tanılanmış standartlara uygunluk gösterir. Bu standartlar hakkındaki detaylar [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)'te tanımlanmıştır.


