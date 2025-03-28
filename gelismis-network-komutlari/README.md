# Gelişmiş Network Komutları

Bu bölüm, Linux sistemlerde ağ yapılandırmasını incelemek, sorunları teşhis etmek ve ağ trafiğini analiz etmek için kullanılan temel ve bazı gelişmiş komut satırı araçlarını ele almaktadır.

İncelenecek konular şunlardır:

*   [ip](ip.md): Modern Linux sistemlerinde ağ arayüzlerini, IP adreslerini, yönlendirme tablolarını ve tünelleri yönetmek için kullanılan ana araç (`ifconfig`, `route` gibi eski araçların yerini almıştır).
*   [ifconfig](ifconfig.md): Ağ arayüzlerini yapılandırmak ve görüntülemek için kullanılan eski bir araç (çoğunlukla `ip` komutu tercih edilir).
*   [route](route.md): IP yönlendirme tablosunu görüntülemek ve yönetmek için kullanılan eski bir araç (çoğunlukla `ip route` komutu tercih edilir).
*   [ping](ping.md): Ağ üzerindeki bir ana bilgisayarın erişilebilirliğini ve yanıt süresini test etmek için ICMP ECHO_REQUEST paketleri gönderir.
*   [traceroute](traceroute.md): Bir paketin hedef ana bilgisayara ulaşana kadar geçtiği yönlendiricileri (hop) ve gecikme sürelerini gösterir.
*   [dig](dig.md): DNS sorguları yapmak ve nameserver'lardan detaylı bilgi almak için kullanılan güçlü bir araçtır.
*   [nslookup](nslookup.md): DNS sorguları yapmak için kullanılan daha eski bir araçtır (`dig` genellikle daha fazla özellik sunar).
*   [netcat](netcat.md): TCP veya UDP üzerinden veri okuma/yazma işlemleri yapabilen çok yönlü bir ağ aracıdır ("ağların İsviçre çakısı"). Port tarama, basit sunucu/istemci oluşturma, veri transferi gibi birçok amaçla kullanılır.
*   [nmap](nmap.md): Ağ keşfi ve güvenlik denetimi için kullanılan güçlü bir araçtır. Açık portları, çalışan servisleri ve işletim sistemlerini tespit etmek için kullanılır.
*   [tcpdump](tcpdump.md): Ağ arayüzlerindeki paketleri yakalamak ve analiz etmek için kullanılan güçlü bir komut satırı aracıdır.
*   [Wireshark (Giriş)](wireshark.md): `tcpdump` benzeri paket yakalama ve analizi için kullanılan, ancak grafiksel bir arayüze sahip popüler bir araçtır (Bu bölümde temel kullanımı anlatılacaktır).
*   [Bantgenişliği Ölçümü](bantgenisligi-oelcuemue.md): Ağ bağlantısının hızını test etmek için kullanılabilecek araçlar (örn. `iperf`, `speedtest-cli`).

Bu araçlar, ağ sorunlarını giderme ve ağ performansını anlama konusunda sistem yöneticileri için temel becerilerdir.
