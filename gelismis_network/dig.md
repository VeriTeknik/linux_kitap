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