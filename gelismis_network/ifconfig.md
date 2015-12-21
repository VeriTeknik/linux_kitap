# ifconfig

Bugün GNU/Linux kullanan sistem yöneticilerinin çoğu eski alışkanlıklarından dolayı ağ cihazlarını (NIC) ayarlamak için ```ifconfig``` komutunu kullanırlar, ancak ifconfig uzun süredir geliştirilmeyen ve artık terk edilen (deprecated) bir yazılımdır. Dolayısıyla kullanmamak daha doğrudur.

ifconfig'in artık geliştirilmemesinin pek çok sebebi vardır, ve bunun yerine ```ip``` komutunun kullanımı tavsiye edilir. Örneğin netmask tanımlanmasına CIDR notasyonu desteklenmez, ```255.255.255.248``` yerine ```/29``` yazamazsınız.

Öte yandan, bazı eski sistemlerde veya gömülü sistemlerde ```ip``` komutu bulunmayabilir. Bunun için nasıl çalıştığı hakkında fikir sahibi olmakta fayda var.