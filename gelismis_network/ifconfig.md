# ifconfig

Bugün GNU/Linux kullanan sistem yöneticilerinin çoğu eski alışkanlıklarından dolayı ağ cihazlarını (NIC) ayarlamak için ```ifconfig``` komutunu kullanırlar, ancak ifconfig uzun süredir geliştirilmeyen ve artık terk edilen (deprecated) bir yazılımdır. Dolayısıyla kullanmamak daha doğrudur.

Programın geliştirilmesinin durdurulduğu ilk olarak Debian mail listinde paylaşıldı. Bugün CentOS 7 gibi sistemlerde bu komut bulunmamaktadır.

Duyuruyu şuradan görebilirsiniz: https://lists.debian.org/debian-devel/2009/03/msg00780.html

ifconfig'in artık geliştirilmemesinin pek çok sebebi vardır, ve bunun yerine ```ip``` komutunun kullanımı tavsiye edilir. Örneğin netmask tanımlanmasına CIDR notasyonu desteklenmez, ```255.255.255.248``` yerine ```/29``` yazamazsınız.

Öte yandan, bazı eski sistemlerde veya gömülü sistemlerde ```ip``` komutu bulunmayabilir. Bunun için nasıl çalıştığı hakkında fikir sahibi olmakta fayda var.

