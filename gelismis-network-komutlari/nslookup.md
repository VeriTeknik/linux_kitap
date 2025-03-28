# `nslookup` (Eski DNS Sorgu Aracı)

`nslookup` (Name Server Lookup), DNS sorguları yapmak için kullanılan eski bir komut satırı aracıdır. `BIND` paketinin bir parçası olarak uzun süre standart olmuştur. Ancak günümüzde, daha esnek, daha güçlü ve daha detaylı çıktı veren **`dig` komutunun kullanımı genellikle tercih edilir.**

`nslookup` hala birçok sistemde bulunur ve basit sorgular için kullanılabilir, ancak `dig`'in sunduğu kontrol ve bilgi seviyesine ulaşamaz. Bu bölümde temel kullanımı, özellikle eski sistemlerle karşılaşıldığında veya `dig`'in bulunmadığı durumlarda faydalı olması amacıyla anlatılacaktır.

## Interactive Mode

nslookup iki türlü kullanılabilir, birisi etkileşimli (interactive) modudur, diğeri de etileşimsiz (non-interactive) modudur.

nslookup programını parametresiz çalıştırdığınızda, etkileşimli modda başlar. Ardından sorgunuzu belirtebilirsiniz. Programdan çıkmak için `exit` kuomutunu kullanabilir, veya `CTRL+D` kısayolunu kullanabilirsiniz.

```bash
eaydin@dixon ~ $ nslookup
> veritech.net
Server:        127.0.1.1
Address:    127.0.1.1#53

Non-authoritative answer:
Name:    veritech.net
Address: 94.103.32.32
> set type=mx
> plugged.in
Server:        127.0.1.1
Address:    127.0.1.1#53

Non-authoritative answer:
plugged.in    mail exchanger = 10 mail.plugged.in.

Authoritative answers can be found from:
> exit
```

Etkileşimli mod, birden fazla sorgu yapmak veya sunucu/tip ayarlarını değiştirmek için kullanılabilir, ancak betikler (scripting) için uygun değildir. Betikler ve tek seferlik sorgular için etkileşimsiz mod kullanılır.

## Etkileşimsiz (Non-Interactive) Mod

Komut satırında sorgulanacak alan adı (ve isteğe bağlı olarak sorgu tipi veya sunucu) belirtilir.

```bash
eaydin@dixon ~ $ nslookup veritech.net
Server:        127.0.1.1
Address:    127.0.1.1#53

Non-authoritative answer:
Name:    veritech.net
Address: 94.103.32.32
```

**A** kaydı dışında bir sorgu yapmak istiyorsak, `-query`, `-type`, `-q` veya `-ty` parametresiyle belirtebiliriz.

```bash
eaydin@dixon ~ $ nslookup -query=mx veritech.net
Server:        127.0.1.1
Address:    127.0.1.1#53

Non-authoritative answer:
veritech.net    mail exchanger = 5 posta.veriportal.com.

Authoritative answers can be found from:
```

Bütün NS kayıtlarına erişmek için, `dig`'de olduğu gibi tip olara **ANY** kullanabiliriz.

```bash
eaydin@dixon ~ $ nslookup -type=any veritech.net
Server:        127.0.1.1
Address:    127.0.1.1#53

Non-authoritative answer:
veritech.net
    origin = ns1.rackdc.com
    mail addr = hostmaster.veritech.net
    serial = 2015120202
    refresh = 14400
    retry = 3600
    expire = 1209600
    minimum = 86400
veritech.net    nameserver = ns2.rackdc.com.
veritech.net    nameserver = ns1.rackdc.com.
veritech.net    mail exchanger = 5 posta.veriportal.com.
Name:    veritech.net
Address: 94.103.32.32

Authoritative answers can be found from:
```

### Sorgulanacak DNS Sunucusunu Belirtmek

Özellikle bir DNS sunucusu tanımlamak istiyorsak, parametre olarak bu bilgiyi de vermemiz gerekir. `dig`'de **@** ile başlayan argümana karşılık gelmektedir.

```bash
eaydin@dixon ~ $ nslookup veritech.net 8.8.8.8
Server:        8.8.8.8
Address:    8.8.8.8#53

Non-authoritative answer:
Name:    veritech.net
Address: 94.103.32.32
```

### Port Belirtme

Standart 53 portu dışında bir port kullanmak istersek, `-port` parametresiyle tanımlayabiliriz.

```bash
nslookup -port=72 veritech.net 192.168.47.3
```

### Reverse DNS

nslookup, parametre olarak IP adresi aldığında, rDNS sorgusu yapar.

```bash
eaydin@dixon ~ $ nslookup 94.103.32.253
Server:        127.0.1.1
Address:    127.0.1.1#53

Non-authoritative answer:
253.32.103.94.in-addr.arpa    name = posta.veriportal.com.

Authoritative answers can be found from:
```
