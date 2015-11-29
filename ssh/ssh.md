# SSH

Uzaktaki sunucuları yönetmek için kullanılan en yaygın yöntemlerden birisi ssh kullanımıdır. SSH, Secure Shell'in kısaltmasıdır ve adı üzerinde, güvenli bir bağlantı sağlar. Bağlanmak istediğiniz noktada SSH Sunucu dinliyor olduğu sürece, ve sizin kullandığınız uçbirimde SSH İstemci bulunduğu sürece sunucunuza güvenli bağlantı yapmaya yarar.

Güvenliden kasıt, sunucu tarafında belirlenen kimlik doğrulama (authentication) yöntemi dışında, bağlantınızın başkaları tarafından dinlenmesini engellemek için mesajları kriptolu iletmesidir. Bu açıdan SSH, Telnet ve muadili programların ağ üzerinden güvensiz iletişim biçimlerine alternatif olarak geliştirilmiştir.

## Tarihçe

SSH 1995 yılında Finlandiya'da Tatu Ylönen tarafından geliştirildi ancak uzun süre açık kaynak olarak dağıtılmadı. Bunun üzerine 1999 yılında Björn Grönvall tarafından OSSH adı altında açık kaynak kodlu lisansı olan bir SSH versiyonu geliştirildi. Bu kodları kendi sistemine uyarlayıp kısa sürede hızla geliştirlen OpenBSD geliştiricileri, bu versiyona OpenSSH ismini verdiler. Bugün kullandığınız hemen her sunucudaki SSH versiyonu OpenSSH'tır ve geliştirilmesi hala devam etmektedir.

OpenSSH her ne kadar çok yaygın olsa da, bugün yaygınca kullanılan SSHv2 protokolü haricinde, SSHv1'i de destekler. Ancak bazı gömülü sistemlerde işlemci ve bellek kullanımını aza indirmek için bu protokoller kullanılmak istenmez. Örneğin Dropbear bu sistemlerde (modemler, switchler vs.) yaygınca kullanılan bir SSH sunucusudur.

SSH Sunucuların bir listesine ve yapabildiklerine aşağıdaki Wikipedia sayfasından ulaşabilirsiniz.

https://en.wikipedia.org/wiki/Comparison_of_SSH_servers