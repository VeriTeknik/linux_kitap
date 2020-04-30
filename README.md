# Linux Kurulum ve Yönetimi

Linux Yönetimi kitabı VeriTeknik ekibinin vermiş olduğu Linux, Ağ ve Güvenlik  eğitimlerinde yardımcı bir kaynak olması amacıyla hazırlanmıştır, kitap bir çok konu hakkında derinlemesine bilgi içermekle birlikte, anlatılan konuların kolay ve sürdürülebilir metodları da izah edilmiştir, örneğin Apache ve PHP kısmında sürümlerin derlemesinden bahsedilirken, kitabın sonunda repolar kullanılarak uygulanan hızlı ve güvenilir çözümlere de yer verilmiştir. Bize göre iyi bir Linux uzmanı pratikliğin yanı sıra konulara derinlemesine hakim olmalıdır. Burada paylaştığımız bilgiler veri merkezimizde karşımıza çıkan bir çok problemin de pratik cevaplarını içermektedir.

İnternet'in kullanımının yoğunlaşması sonucunda açık kaynak kodlu, defalarca sınanmış ve hem kararlılığı hem de güvenliği konusunda ticari yazılımlara alternatif olmaya başlayan GNU/Linux işletim sistemleri, gelecekte de çok yaygın bir şekilde kullanılacaktır. Özellikle Sanayi 4.0 ile birlikte, İnternete bağlanan cihaz sayısı logaritmik olarak yükselmektedir ve bu sistemlerin birçoğunda gömülü sstem olarak Linux türevleri kullanılmaktadır. Ülkemizin Sanayi 4.0 treninde yerini alabilmesi temelde yatan işletim sistemini iyi bilmesi ile daha da kolay olacaktır, biz de toplumsal kalkınmanın paylaşımdan geçeceğine inanıyoruz.

**Kitap çoğunlukla Web Hosting ve Uygulama Hosting açısından sürümleri ele almıştır. Gömülü sistemlerin anlatıldığı bölümlerde ise daha çok I/O kullanım bilgilerine değinilecektir.** Kitabın amacı okuyucuya Hosting ortamının/web servislerinin kurulmasında ve işletilmesinde gerekli olacak bilgiyi vermektir.

Veri merkezimizde edindiğimiz bilgiler güncellendikçe, biz de kitapta değişiklikler yapmaktayız, eğer kitabın basılı sürümü elinizdeyse, en son sürüme ve örnek kodlara [https://www.gitbook.com/book/veriteknik/linux-yonetimi](https://www.gitbook.com/book/veriteknik/linux-yonetimi) adresinde de ücretsiz olarak ulaşabilirsiniz.

Döküman veya GNU/Linux hakkında herhangi sorunuz için bize [iletisim@veriteknik.com](mailto:iletisim@veriteknik.com)  
adresinden ulaşabilirsiniz.

## Yazarlar Hakkında

### Cem Karaca

Üniversite yıllarında, İnternet'in gelişimi ile birlikte Sunucular, Ağ ve Linux ile tanışmıştır, LYNX Browser ile siyah ekranda başlayan serüveni, 2004 yılından bu yana Ankara'da Veri Merkezi işleterek devam etmektedir. Elektrik & Elektronik Yüksek Mühendisi olan yazarın 2003 yılında yayınlanmış "Pedagojik Destekli uzaktan talepli Video Sistemi \(VoD\)" yüksek lisans tezidir. IEEE indeksli makaleleri bulunan yazar, multimedia transferi için kullanılabilen RUDP protokolünü tasarlamıştır. Uzmanlık Alanları: MCF, C/C++, Gömülü Sistemler, Ağ ve Sürücü Programlama.

İnternet üzerinde kredi kartı işlemleri, transferi ve depolaması üzerine bir standart olan PCI-DSS üzerine uzun yıllardır yönetimli servisler sağlamaktadır. VeriTeknik'teki çalışma arkadaşları ile birlikte PCIChecklist'i kurmuş ve gerişim siber güvenlik alanında 1.2 Milyon TL yatırım almıştır. Halen aktif olarak veri merkezi hizmetlerine devam eden yazar aynı zamanda PCICHecklist ile siber güvenlik üzerine çalışmalarına devam etmektedir.

### Mustafa Emre Aydın

Lisans eğitimi sırasında çok miktardaki bilimsel verinin tasniflenmesi ve anlamlandırılması üzerine çalışırken GNU/Linux sistemler üzerinde MPI ve Embarrasingly Parallel algoritmalar geliştirerek programlama ve sistem yönetimi deneyimi kazanmıştır. Temel bilimler ve sistem programcılığında Python'un yaygınlaşmasıyla "Wavelet Dönüşümüne Dayalı Dinamik Güç Spektrumu Eldesi" konulu yüksek lisans tez çalışmasının yanı sıra pek çok görüntü işleme ve güvenlik sistemini de bu dilde geliştirmiştir. Gömülü Linux sistemlerin yanı sıra sunucu ve ağ programlama yapan yazar, Doktora çalışmasına X-Işın Uydu Gözlemlerinin Hassas Zaman Ölçümü konusunda devam etmektedir.

## VeriTeknik

VeriTeknik, ağ iletişimi ve altyapısı üzerine uzmanlaşmış mühendislik çözümleri sunmaktadır. Veri merkezleri, dağıtık sunucu sistemleri, ağ güvenliği ve ağ yazılımları üzerinde özgün çalışmalar yapmaktadır. 2004 yılından bu yana kazanmış olduğumuz deneyimi sektördeki diğer meslektaşlarımızla ve siz değerli okuyucularımızla paylaşmak, ülkemizin açık kaynak kodlu sistemlerde bir nebze de olsa gelişme sağlamasını görmekten mutluluk duyarız. VeriTeknik, Ankara ve Amsterdam ofislerinde faaliyet göstermektedir, toplamda 10 ülkede veri merkezleri ile çalışmaktadır. Tübitak Pardus Çözüm ortağı olan VeriTeknik aynı zamanda Ultra Electronics ve Realsec'in Türkiye, Hollanda, Doğu Avrupa ve Türki Cumhuriyetler resmi distribütörüdür.

## Teşekkürler

Katkılarından dolayı çalışma arkadaşlarımız Yaşar Celep ve Ömrüm Çetin'e, bu kitabın oluşmasına vesile olan Hazine Müsteşarlığı'na ve Nuri Akman'a teşekkür ederiz.

## Notlar

### Komut Gösterimi

Dökümanda komutlar tek başına gösterildiklerinde başında $ veya \# gibi işaretler olmadan kullanılmıştır. Örneğin:

```bash
which vi
```

Öte yandan, çıktısı belirtilecek komutlarda, girdinin ayırt edilebilmesi için, prompt'un tamamı gösterilmiştir. Örneğin:

```bash
eaydin@dixon ~ $ which vi
/usr/bin/vi
```

Çıktısı çok uzun olan ve yeni girdi kullanılan komutlarda, girdiler ve çıktılar ayrı komut blokları halinde gösterilmiştir.

### Çevrimdışı Okumak İçin

Dökümanın PDF halinde yatay eksende çok uzun olan komutlar veya çıktıları tam olarak görülmemektedir. Bu durumun önüne geçmek için uzun komutlar **\** işareti ile alt satırdan devam etmiştir.

HTML okunduğunda bu problem bulunmamaktadır, HTML scroll-bar \(kaydırma çubuğu\) sunmaktadır.

Kitabı çevrimdışı HTML olarak okumak isterseniz, `wget` ile edinebilirsiniz.

```bash
wget -m -p --convert-links -P kitap https://veriteknik.gitbooks.io/linux-yonetimi/content/
```

Yukarıdaki komut, bulunduğunuz dizine `kitap` isminde bir dizin oluşturur ve kitabı indirir. Komutun açıklamasını [Gelişmiş Terminal Komutları](https://veriteknik.gitbooks.io/linux-yonetimi/content/gelismis_terminal/gelismis_terminal_komutlari.html) bölümündeki [wget](https://veriteknik.gitbooks.io/linux-yonetimi/content/gelismis_terminal/wget.html) kısmında öğrenebilirsiniz.

### Terimler

Terimler Türkçe karşılıkları belirtilerek çoğunlukla İngilizce olarak kullanılmıştır. Okuyucuların teknik terimleri ve karşılaştıkları problemleri İngilizce kullanarak Google'lamaları tavsiye edilmektedir.

**Cem Karaca** & **M. Emre Aydın** & **Tunç Yıldırım**

---

... Veri her yerde ...

Copyright 2015-2020 VeriTeknik Bilişim, Basın ve Yayın Ltd. Şti. İzinsiz alıntı yapılamaz, kopyalanamaz ve çoğaltılamaz.

