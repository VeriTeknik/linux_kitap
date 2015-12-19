# dig

Daha önce "Bir sayfanın İnternetteki Serüveni" başlığında karşılaştığımız ```dig``` komutu, bir IP adresi için DNS sorgusu yapmaya yarar. Sistem yöneticilerinin sıkça kullandığı bu aracın bilinmesi gereken birkaç parametresi bulunur.

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

Yukarıdaki çıktı, veritech.net adresinin A kayıtlarını göstermektedir.

