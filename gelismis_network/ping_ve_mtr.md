# ping ve mtr

## ping

Network üzerinden yapılan işlemlerin vazgeçilmezi ping-pong mekanizması muhtemelen yakından tanıdığınız bir yapıya sahiptir. Burada ping komutunun pratik birkaç kullanımını göstereceğiz.

### Tekrar Sayısı Belirtme

ping tek başına kullanıldığında sonsuza kadar işlemi sürdürür, ancak ```Ctrl+c``` ile programı durdurursanız, veya ```kill``` ile PID'yi öldürürseniz durur.

Ancak program daha çalıştırılırken ```-c``` parametresiyle kaç defa ping atıp sonlanacağını belirtebilirsiniz.

```bash
eaydin@dixon ~ $ ping -c 5 google.com
PING google.com (216.58.209.14) 56(84) bytes of data.
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=1 ttl=54 time=50.9 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=2 ttl=54 time=50.4 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=3 ttl=54 time=50.1 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=4 ttl=54 time=50.1 ms
64 bytes from sof01s12-in-f14.1e100.net (216.58.209.14): icmp_seq=5 ttl=54 time=51.1 ms

--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 50.154/50.575/51.153/0.475 ms
```

### Sadece İstatistiği Gösterme

Yukarıdaki örneğin sonunda, ping istatistiğimizin yansıtıldığını görebilirsiniz. ```-q``` komutu (quiet output) sadece ping işleminin başını ve sonundaki istatistiği gösterir. ```-c``` ile birleştirilmezse, programı durdurmadan istatistiği göremeyiz.

```bash
eaydin@dixon ~ $ ping -c 5 -q google.com 
PING google.com (216.58.209.14) 56(84) bytes of data.

--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 51.350/52.214/54.528/1.232 ms
```

