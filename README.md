Döküman Hakkında
=======

Linux Yönetimi dökümanı VeriTeknik ekibinin verdiği eğitimlerde yardımcı olması amacıyla hazırlanmaktadır. 
**Kitap çoğunlukla Web Hosting ve Uygulama Hosting açısından sürümleri ele almıştır.** Kitabın amacı okuyucuya Hosting ortamının kurulmasında ve işletilmesinde gerekli olacak bilgiyi vermektir.

Döküman içeriği zamanla şekillenecek ve yapısını/hiyerarşisini değiştirecektir.

Döküman veya GNU/Linux hakkında herhangi sorunuz için bize [iletisim@veriteknik.com](mailto:iletisim@veriteknik.com)
adresinden ulaşabilirsiniz.

## Yazarlar Hakkında
### Cem Karaca
Üniversite yıllarında, İnternet'in gelişimi ile birlikte Sunucular, Ağ ve Linux ile tanışmıştır, LYNX Browser ile siyah ekranda başlayan serüveni, 2004 yılından bu yana Ankara'da Veri Merkezi işleterek devam etmektedir. Elektrik & Elektronik Yüksek Mühendisi olan yazarın 2003 yılında yayınlanmış "Pedagojik Destekli uzaktan talepli Video Sistemi (VoD)" yüksek lisans tezidir. IEEE indeksli makaleleri bulunan yazar, multimedia transferi için kullanılabilen RUDP protokolünü tasarlamıştır. Uzmanlık Alanları: C/C++, Gömülü Sistemler, Ağ ve Sürücü Programlama
###Mustafa Emre Aydın

Lisans eğitimi sırasında çok miktardaki bilimsel verinin tasniflenmesi ve anlamlandırılması üzerine çalışırken GNU/Linux sistemler üzerinde MPI ve Embarrasingly Parallel algoritmalar geliştirerek programlama ve sistem yönetimi deneyimi kazanmıştır. Temel bilimler ve sistem programcılığında Python'un yaygınlaşmasıyla "Wavelet Dönüşümüne Dayalı Dinamik Güç Spektrumu Eldesi" konulu yüksek lisans tez çalışmasının yanı sıra pek çok görüntü işleme ve güvenlik sistemini de bu dilde geliştirmiştir. Gömülü Linux sistemlerin yanı sıra sunucu ve ağ programlama yapan yazar, Doktora çalışmasına X-Işın Uydu Gözlemlerinin Hassas Zaman Ölçümü konusunda devam etmektedir.

## Notlar

### Komut Gösterimi

Dökümanda komutlar tek başına gösterildiklerinde başında $ veya # gibi işaretler olmadan kullanılmıştır. Örneğin:

```bash
which vi
```

Öte yandan, çıktısı belirtilecek komutlarda, girdinin ayırt edilebilmesi için, prompt'un tamamı gösterilmiştir. Örneğin:

```bash
eaydin@dixon ~ $ which vi
/usr/bin/vi
```

Çıktısı çok uzun olan ve yeni girdi kullanılan komutlarda, girdiler ve çıktılar ayrı komut blokları halinde gösterilmiştir.

### Çevrimdışı Okumak

Dökümanın PDF halinde yatay eksende çok uzun olan komutlar veya çıktıları tam olarak görülmemektedir. Bu durumun önüne geçmek için uzun komutlar **\** işareti ile alt satırdan devam etmiştir.

HTML okunduğunda bu problem bulunmamaktadır, HTML scroll-bar (kaydırma çubuğu) sunmaktadır.

Kitabı çevrimdışı HTML olarak okumak isterseniz, ```wget``` ile edinebilirsiniz.

```bash
wget -m -p --convert-links -P kitap https://veriteknik.gitbooks.io/linux-yonetimi/content/
```

Yukarıdaki komut, bulunduğunuz dizine ```kitap``` isminde bir dizin oluşturur ve kitabı indirir. Komutun açıklamasını [Gelişmiş Terminal Komutları](https://veriteknik.gitbooks.io/linux-yonetimi/content/gelismis_terminal/gelismis_terminal_komutlari.html) bölümündeki [wget](https://veriteknik.gitbooks.io/linux-yonetimi/content/gelismis_terminal/wget.html) kısmında öğrenebilirsiniz.

### Terimler

Terimler çoğunlukla Türkçe karşılıkları belirtilerek çoğunlukla İngilizce olarak kullanılmıştır. Okuyucuların teknik terimleri ve karşılaştıkları problemleri İngilizce kullanarak Google'lamaları tavsiye edilmektedir.

**Cem Karaca** & **M. Emre Aydın**


---


Copyright 2015 VeriTeknik Bilişim Ltd. Şti. izinsiz alıntı yapılamaz, kopyalanamaz ve çoğaltılamaz.