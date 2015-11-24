# Network Ayarları

Debian sistemler üzerinde kalıcı network ayarları yapmak Red Hat sistemlerdekine benzer olsa da, farklı dosya yapıları barındırır. Red Hat'teki gibi her NIC kendi dosyasına sahip değildir, bütün NIC'lerin ortak bir dosyası vardır: ```/etc/network/interfaces```

Red Hat'tekinden farklı olarak, bu dosyayı yöneten servisin adı **network** değil, **networking**dir.

```bash
service networking start
```

veya

```bash
/etc/init.d/networking start
```

```bash
# cat /etc/network/interfaces 
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
	address 94.103.47.78
	netmask 255.255.255.128
	broadcast 94.103.47.127
	network 94.103.47.0
	gateway  94.103.47.1
```

Yukarıda, çalışmalarımız için kullanacağımız test makinalarından birinin ```interfaces``` dosyasının içeriği görülüyor.

Eğer ```eth0``` cihazının bu şekilde static IP almasını isteseydik, ancak ```eth1``` cihazının DHCP ile IP almasını isteseydik, dosya içeriği aşağıdaki gibi olurdu.

```bash
# cat /etc/network/interfaces 
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
	address 94.103.47.78
	netmask 255.255.255.128
	broadcast 94.103.47.127
	network 94.103.47.0
	gateway  94.103.47.1

auto eth1
iface eth1 inet dhcp
```

**Problem:**

Birden fazla ethernet kartı üzerine farklı bloklardan IP'ler ekleyip tamamının aynı gateway üzerinden çıkmasını isteyebilirsiniz. Örneğin eth0 ve eth1 kartlarımız var, eth0 94.103.37.0/25 IP'lerini alacak, eth1 ise 192.168.25.128/25 IP'lerini alacak şekilde düzenlenebilir. Bu durumda ikisinin de internete 94.103.37.1 IP'si üzerinden çıkmasını isteyebilirsiniz. Bu gibi bir problemin çözümü için aşağıdaki makaleyi inceleyebilirsiniz.

http://www.plugged.in/linux/add-multiple-gateways-multiple-nics-ubuntu-server.html