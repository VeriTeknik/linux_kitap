# Network Ayarları

RedHat tabanlı sistemler üzerinde network ayarları ```/etc/sysconfig/network-scripts``` dizinindeki dosyalarda bulunur. Bu dizindeki dosyalar **network** servisi tarafından okunur ve ilgili cihazlar gerekli ayarlamaları alır. Dolayısıyal bu dosyaları düzenledikten sonra network servisini yeniden başlatmak gerekir.

```bash
service network restart
```

Network kartlarının adreslerini ```/etc/sysconfig/network-scripts/ifcfg-eth0``` gibi dosyalardan düzenleyebilirsiniz. Sisteminizdeki network kartının ismine göre, ```ifcfg-eth0``` ismi değişiklik gösterebilir. Örneğin Dell T20 sunucularda bu isim doğrudan ```ifcfg-em1``` şeklinde olur.

Aşağıda bir Fedora Sanal Sunucunun üzerindeki ifcfg-eth0 dosyasının içeriği görülüyor.

```bash
[root@localhost network-scripts]# cat ifcfg-eth0
# Advanced Micro Devices [AMD] 79c970 [PCnet32 LANCE]
DEVICE=eth0
ONBOOT=yes
BOOTPROTO=dhcp
HWADDR=00:0c:29:5a:96:3a
```

**DEVICE**: Adından da anlaşılacağı gibi, ayarlarını yaptığımız network cihazının ismini gösteriyor.

**ONBOOT**: Cihazın reboot sırasında devreye girip girmeyeceğini belirtiyor.

**BOOTPROTO**: Cihaz ayağa kalktığında, nasıl bir protokol izleyeceğini belirtir. Buradaki örnekte *dhcp* tanımlanmış.

**HWADDR**: Cihazımızın fiziksel adresi (MAC-Adresi). Kullanılması zorunlu değildir, birden fazla NIC (Network Interface Card) bulunduğunda kullanılması şart olabilir. **HWADDR** yerine **MACADDR** ifadesi de kullanılabilirdi, ancak ikisi bir arada kullanılmamalıdır.

Yukarıdaki örnekten, DHCP ile IP alan bir sistemi görmüş olduk. Eğer bu sisteme kendimiz Network bilgilerini tanımlamak isteseydik, dosyanın içeriği farklı olmalıydı.

Örneğin aşağıdaki dosya içeriği ile *eth0* cihazının ağ bilgileri farklı olacaktır.

```bash
[root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0
# Advanced Micro Devices [AMD] 79c970 [PCnet32 LANCE]
DEVICE=eth0
ONBOOT=yes
BOOTPROTO=static
IPADDR=172.20.10.3
NETMASK=255.255.255.240
GATEWAY=172.20.10.1
HWADDR=00:0c:29:5a:96:3a
```

Dosyanın içeriğini yukarıdaki gibi değiştirdikten sonra, yeni değerlerin network servisi tarafından okunabilmesi için, daha önce gördüğümüz ```service network restart``` komutu ile servisin yeniden başlatılması gerekiyor.

Yukarıdaki örnekte ```BROADCAST``` veya ```NETWORK``` gibi değerler tanımlamadık, ancak bu bilgileri de sağlayabilirdik. Bir network tanımı yapılması için IP adresi ve NETMASK bilgisinin yanısıra GATEWAY, BROADCAST veya NETWORK bilgilerinden birinin sağlanması yeterlidir.

