# SSH

Uzaktaki sunucuları yönetmek için kullanılan en yaygın yöntemlerden birisi ssh kullanımıdır. SSH, Secure Shell'in kısaltmasıdır ve adı üzerinde, güvenli bir bağlantı sağlar. Bağlanmak istediğiniz noktada SSH Sunucu dinliyor olduğu sürece, ve sizin kullandığınız uçbirimde SSH İstemci bulunduğu sürece sunucunuza güvenli bağlantı yapmaya yarar.

Güvenliden kasıt, sunucu tarafında belirlenen kimlik doğrulama (authentication) yöntemi dışında, bağlantınızın başkaları tarafından dinlenmesini engellemek için mesajları kriptolu iletmesidir. Bu açıdan SSH, Telnet ve muadili programların ağ üzerinden güvensiz iletişim biçimlerine alternatif olarak geliştirilmiştir.

## Tarihçe

SSH 1995 yılında Finlandiya'da Tatu Ylönen tarafından geliştirildi ancak uzun süre açık kaynak olarak dağıtılmadı. Bunun üzerine 1999 yılında Björn Grönvall tarafından OSSH adı altında açık kaynak kodlu lisansı olan bir SSH versiyonu geliştirildi. Bu kodları kendi sistemine uyarlayıp kısa sürede hızla geliştiren OpenBSD geliştiricileri, bu versiyona [OpenSSH](http://www.openssh.com) ismini verdiler. Bugün kullandığınız hemen her sunucudaki SSH versiyonu OpenSSH'tır ve geliştirilmesi hala devam etmektedir.

OpenSSH her ne kadar çok yaygın olsa da, eski sürümleri hem modern SSHv2 protokolünü hem de artık güvensiz kabul edilen eski SSHv1 protokolünü destekleyebiliyordu. **Günümüzde SSHv1 protokolü kesinlikle kullanılmamalı ve tüm sunucu yapılandırmalarında devre dışı bırakılmalıdır.** Modern OpenSSH sürümleri genellikle varsayılan olarak SSHv1'i desteklemez veya devre dışı bırakır.

Bazı gömülü sistemlerde veya özel durumlarda, kaynak kullanımı daha düşük olan alternatif SSH sunucuları (örn. [Dropbear](https://matt.ucc.asn.au/dropbear/dropbear.html)) kullanılabilir, ancak OpenSSH standart ve en yaygın kullanılan çözümdür.

SSH Sunucuların bir listesine ve yapabildiklerine aşağıdaki Wikipedia sayfasından ulaşabilirsiniz.

[https://en.wikipedia.org/wiki/Comparison\_of\_SSH\_servers](https://en.wikipedia.org/wiki/Comparison\_of\_SSH\_servers)

![](http://imgs.xkcd.com/comics/im\_an\_idiot.png)

_Kaynak:_ [https://xkcd.com/530/](https://xkcd.com/530/)
