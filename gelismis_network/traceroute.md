# traceroute

traceroute komutu bir sunucuya erişene kadar gidilen yolu tespit etmeye yarar. Bunun için bu yol boyunca uğradığı bütün sunuculara ICMP paketleri gönderir. Nasıl çalıştığını anlamak için ping komutunda karşımıza çıkan (ancak üstünde durmadığımız) TTL (Time To Live) mekanizmasını anlamak gerekir.

## TTL

TTL basitçe, gönderdiğimiz paketin kaç noktadan geçmesine izin verdiğimizi ifade eder. Ping programını incelerken kullandığımız örneklerde genellikle bu değerin 54 olduğunu gördük. Bu şu anlama geliyor, "bu paket en fazla 54 noktadan geçebilir, daha fazlasına iletilmemeli."

Ağ'daki (internet veya yerel ağ fark etmez) her nokta (sunucu, router vb.) kendisine gelen paketin TTL'ine bakar, eğer bu değer 1'den büyükse, değeri 1 azaltıp bir sonraki  noktaya iletir, eğer değer 1'se, o zaman paketi göz ardı, eder, ICMP hata pakedi gönderir ve bu bilgi içerisinde kendi IP adresi de yer alır.

Öyleyse uzaktaki bir sunucuya ping atarken, eğer TTL değerimizi aradaki router sayısından daha küçük tutarsak, bu paket hedef sunucuya asla ulaşmaz, ancak arada bir noktadan cevap alırız.

Örneğin google.com sunucusuna ping atmak istersek, ancak TTL değerini 1 tutarsak,

```bash
eaydin@dixon ~ $ ping -t 1 google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
From 192.168.100.1 icmp_seq=1 Time to live exceeded
```

TTL değerimizi 2'ye çıkardığımızda,

```bash
eaydin@dixon ~ $ ping -t 2 google.com
PING google.com (216.58.208.110) 56(84) bytes of data.
From 81.212.171.130.static.turktelekom.com.tr (81.212.171.130) icmp_seq=1 Time to live exceeded
```