# aptitude Paket Yöneticisi

Debian (ve Ubuntu) sistemlerde, Red Hat/CentOS için gördüğümüz rpm'in yum karşılığı olan paket yöneticisi aptitude isminde bir yazılımdır. Aptitude kullanıcı için tasarlanmış, başka programlar tarafından paketlerin yönetimi için apt-get ve apt-cache gibi programlar geliştirilmiştir. Ancak apt-get gibi programların yaygın kullanımlarından dolayı çoğu sistem yöneticisi aptitude programını neredeyse hiç kullanmaz.

apt-get, apt-cache araçları aptitude programı ile aynı repoları kullandığından, birini öğrenmek sorun teşkil etmez. Biz de bu bölümde yaygın kullanıma aşina olabilmek adına apt-get, apt-cache ve benzer araçlarla (kısaca apt) deb paket yönetimini göreceğiz.

## Paket Listeleri

apt paket listelerini yum'dan farklı bir mekanizmayla tutar. Her arama yaptığınızda sunuculara bağlanmaz. Böylelikle daha hızlı sonuç verir ancak her defasında sonuçlarınız güncel olmayabilir. Güncel sonuç almak için listelerinizi güncellemeniz gerekir.

apt ile paket listenizi güncellemek için **update** komutunu kullanabilirsiniz.

```bash
apt-get update
```

Artık paket listeleri içerisinde arama yapabiliriz. Örneğin adında **htop** geçen bir paket ismi ararsak şöyle arama yapabiliriz.

```bash
# apt-cache search htop
aha - ANSI color to HTML converter
htop - interactive processes viewer
libauthen-oath-perl - Perl module for OATH One Time Passwords
```

Tahmin edeceğiniz gibi bu çok uzun bir liste getirecektir, çünkü paket isminde, açıklamasında **top** geçen bütün paketleri listeler. Ancak burada regular expression kullanabilirdik, örneğin