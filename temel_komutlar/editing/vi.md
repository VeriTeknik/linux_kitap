# vi

vi, terminalde çok satırlı düzenleme yapabilen metin editörlerinin atalarından birisi olarak görülür. 2009'da yapılan bir anket Linux üzerine kullanılan en yaygın metin editörünün %36 ile vi olduğunu göstermektedir. Kompleks işlemleri destekleyen ve hemen her sistemde önyüklü olarak gelen vi haricinde bir diğer popüler metin editörü ise emacs'tir.

## Tarihçe

vi editörü aslında oldukça eskiye dayanan bir takım editörlerin evrilmesiyle ortaya çıkmıştır. Editörün çıkış noktası *ed* isimli editördür. *ed* Ken Thompson tarafından Unix işletim sisteminin üç temel yapıtaşından biri olarak geliştirilmiştir: assembler, editör ve shell.

*ed*'in kullanımı zamanla pek "pratik" bulunmadığından, "ölümsüzler" (immortals) tarafından kullanılabileceği espirisiyle, "ölümlüler" (mortals) için *em* geliştirildi: *editor for mortals*. *em*'nin görece yaygınlaşmasının ardından Bill Joy ve Chuck Haley kodu kendilerince şekillendirip *en* isimli bir versiyonunu geliştirdiler. Daha sonra Bill Joy tek başına *en*'yi "extend" edip *ex*'i piyasaya sürdü. *ex*'e eklenen tam-ekran görsel modu, metin editörlerinin dönüm noktası oldu. Her ne kadar bu fikir piyasadaki başka yazılımlardan alınmış olsa da, en yaygın kullanıma ulaşanı kendisi olmuştu. Zamanla *ex*'in görsel modu *(visual mode)* o kadar çok kullanıldı ki, doğrudan *vi* komutu ile *ex* programı tam-ekran görsel modunda çalıştırılır oldu.

Yıllar sonra Bram Moolenaar tarafından *vi* kodu geliştirildi ve *vim* *(Vi IMproved)* açığa çıktı. Günümüz klavye ve sistemlerine en yatkın olanı olduğu gerekçesiyle biz de örneklerimizde *vim* kullanacağız.

CentOS işletim sistemiyle birlikte gelen *vi* doğrudan *vim* olurken Debian ve Ubuntu türevlerinde durum böyle değildir. Eğer bu sistemlerde de *vim* kullanmak isterseniz aşağıdaki komutla önce yüklemeniz gerekir.

```bash
apt-get install vim
```

Ardından *vi* çalıştırıldığında doğrudan *vim* çalışacaktır. Yukarıdaki komutların ne anlama geldiğini merak ediyorsanız, *Temel Dağıtımlar* bölümünün *Debian* kısmının *aptitude Paket Yöneticisi* başlığını inceleyebilirsiniz.

## Çalışma Modları

vi'nın bir takım çalışma modları vardır, bu aslında *ed*'den kalan bir alışkanlıktır. vi ilk çalıştırıldığında *komut* modunda bulunur, *düzenleme* moduna geçmek için ayrıca işlem yapmanız gerekir. *komut* modundayken tuşlara basarsanız yazı yazamazsınız, ancak kopyalama, silme, dosya içinde gezinme benzeri işlemleri gerçekleştirebilirsiniz.

Dosyamızı vi ile aşağıdaki gibi açtığımızda, *komut* modundan *düzenleme* moduna geçmek için **i** tuşuna basmamız yeterlidir.

```bash
vi crc8.py
```

Dosyayı açtıktan sonra **i** tuşuna basarsanız terminalinizin en alt satırında **--INSERT--** yazdığını görebilirsiniz. Bu, *düzenleme* modunda bulunduğunuzu gösterir. Bu durumdayken tuşlara bastığınızda, imlecin bulunduğu yere karakter eklemesi gerçekleşecektir. *düzenleme* moduna geçmek için **i** tuşunu kullanmak yerine, klavyelerin artık pek kullanılmayan **INSERT** tuşunu da kullanabilirsiniz.

Düzenleme modundan çıkmak için klavyenizin **ESC** tuşuna basmanız gerekir. Düzenleme modundan çıkılınca tekrar komut moduna dönülür, aşağıdaki **--INSERT--** yazısının kaybolduğunu görebilirsiniz.

##Temel Komutlar

*vi* editöründen çıkmak için *komut* modundayken **:** tuşuna bastıktan sonra sırasıyla bir takım komutlar girmek gerekir.

Örneğin **:q** komutu *programdan çıkmak istediğimiz* anlamına gelir (quit). Öte yandan, dosya içeriğinde bir değişiklik yapmışsak, bunun için ya içeriği kaydetmek, ya da bu içerikten vazgeçmemiz gerekir. Yaptığımız içeriği kaydetmek için, dosyaya yazmamız (write) gerekir, bu yüzden komut **:w** şeklindedir. Eğer değişikliği yazıp, ardından *vi*'dan çıkmak istiyorsak, **:wq** yazıp **ENTER** tuşuna basmamız yeterlidir. Yapılan değişikliklerden vazgeçmek istiyorsak, programdan çıkmak ve bunu mecburi kılmak istiyoruz demektir, bunun için **:q!** yeterli olacaktır.

Komut modundayken, programa yapmak istediklerimizi bildirmek için **:** tuşuna basmak zorunda değiliz, bu sadece bazı durumlarda geçerlidir. Örneğin mevcut satırın sonuna gitmek için **$** tuşuna basmamız yeterlidir. Benzer şekilde mevcut dosyanın en sonuna gitmemiz için **Shift+g** tuşları yeterlidir.

**$** sembolü **reguar expressions** (düzenli ifadeler) içerisinde "sondaki" karaktere işaret ettiğinden satır sonuna bu işaretle gidilir, örneğin dosya sonuna gitmek için **Shift+g** yerine **:$** işareti de kullanılabilir.

Yukarıdaki örnekte **:$** dosya sonundaki satıra işaret ettiğine göre, **$** yerine yazacağımız sayı ilgili satıra işaret edebilir. Gerçekten de 17. satıra gitmek için *vi* editöründe **:17** yazıp **ENTER** tuşuna basmamız yeterli olacaktır.

*vi* editörü aynı zamanda çeşitli süslemeler de destekler. Örneğin dosyaların satır sayılarını görmek için **:set number** komutu uygulanabilir. Benzer şekilde satır sayılarını kaldırmak için **:set nonumber** komutunu kullanabilirsiniz.

Editörü iki parçaya bölmek mümkün. **:split** komutu ile mevcut ekran ikiye bölünür. **:vsplit** ile dikey olarak bölünür. Eğer bölerken dosya adı belirtmek istiyorsak bu da mümkün, **:vsplit dosyaadi** yazdığımızda *dosyaadi* ayrılmış kısımda açılacaktır. 

Veya istersek **:new** ile yeni bir buffer açabiliriz. Tabii bu bufferdan çıkarken kaydetmek için ismini belirtmek gerekir. Örneğin yeni açtığımız buffer'ı kaydedip çıkmak için **:wq yenibuffer.txt** komutu kullanılabilir.

Açılan pencereler arasında gezmek için ister **CTRL+w+w**, istersek **CTRL+w+yön** kullanılabilir.