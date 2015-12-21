# nslookup

"Name Server Lookup" anlamına gelen nslookup, BIND paketleri içerisinde en eskilerindendir. Bir dönem Internet Systems Consortium (ISC) nslookup'ın kullanımını kaldırmayı planlasa da (deprecate) 2004'te bu kararlarından vazgeçmiştir ve geliştirmeye devam etmiştir.

Bugün nslookup ile yapabileceğiniz her şeyi ve daha fazlasını dig ile gerçekleştirebilirsiniz, bu yüzden dig kullanımını tavsiye ederiz. ISC'nin nslookup desteğini kaldırıp dig kullanımına teşvik etme kararı, dig'in isim çözümlemede işletim sistemi kaynaklarına göre davranmasına, nslookup'ın ise bu işi kendi içinde halletmesine bağlanıyordu. İlerleyen versiyonlarda nslookup'ın bu bağımlılığını kaldırdılar.

Her ne kadar dig kullanımını tavsiye etsek de, bağlandığınız bazı sistemlerde dig bulunmayabilir, nslookup kullanmanız gerekebilir. Bu sebeple burada temel kullanımını göreceğiz.

## Interactive Mode

nslookup iki türlü kullanılabilir, birisi etkileşimli (interactive) modudur, diğeri de etileşimsiz (non-interactive) modudur.

nslookup programını parametresiz çalıştırdığınızda, etkileşimli modda başlar. Ardından sorgunuzu belirtebilirsiniz. Programdan çıkmak için ```exit``` kuomutunu kullanabilir, veya ```CTRL+D``` kısayolunu kullanabilirsiniz.

```bash
eaydin@dixon ~ $ nslookup
> veritech.net
Server:		127.0.1.1
Address:	127.0.1.1#53

Non-authoritative answer:
Name:	veritech.net
Address: 94.103.32.32
> exit

eaydin@dixon ~ $ 
```

Etkileşimli mod bir sistem yöneticisi için çok pratik olmadığından pek kullanılmaz. Örneğin yazdığınız scriptlerin zaman zaman çıktıları çözümlemesi gerekebilir, bu tip durumlar için etkileşimsiz mod kullanılmalıdır.

## Non-Interactive Mode

Yukarıdaki sorgunun aynısını terminalden şu şekile yapabilirdik.

```bash
eaydin@dixon ~ $ nslookup veritech.net
Server:		127.0.1.1
Address:	127.0.1.1#53

Non-authoritative answer:
Name:	veritech.net
Address: 94.103.32.32
```

**A** kaydı dışında bir sorgu yapmak istiyorsak, ```-query```, ```-type```, ```-q``` veya ```-ty```  parametresiyle belirtebiliriz.

```bash
eaydin@dixon ~ $ nslookup -query=mx veritech.net
Server:		127.0.1.1
Address:	127.0.1.1#53

Non-authoritative answer:
veritech.net	mail exchanger = 5 posta.veriportal.com.

Authoritative answers can be found from:
```