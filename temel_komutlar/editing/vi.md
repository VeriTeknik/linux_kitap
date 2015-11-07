# vi

vi, terminalde çok satırlı düzenleme yapabilen metin editörlerinin atalarından birisi olarak görülür. 2009'da yapılan bir anket Linux üzerine kullanılan en yaygın metin editörünün %36 ile vi olduğunu göstermektedir. Kompleks işlemleri destekleyen ve hemen her sistemde önyüklü olarak gelen vi haricinde bir diğer popüler metin editörü ise emacs'tir.

## Tarihçe

vi editörü aslında oldukça eskiye dayanan bir takım editörlerin evrilmesiyle ortaya çıkmıştır. Editörün çıkış noktası *ed* isimli editördür. *ed* Ken Thompson tarafından Unix işletim sisteminin üç temel yapıtaşından biri olarak geliştirilmiştir: assembler, editör ve shell.

*ed*'in kullanımı zamanla pek "pratik" bulunmadığından, "ölümsüzler" (immortals) tarafından kullanılabileceği espirisiyle, "ölümlüler" (mortals) için *em* geliştirildi: *editor for mortals*. *em*'nin görece yaygınlaşmasının ardından Bill Joy ve Chuck Haley kodu kendilerince şekillendirip *en* isimli bir versiyonunu geliştirdiler. Daha sonra Bill Joy tek başına *en*'yi "extend" edip *ex*'i piyasaya sürdü. *ex*'e eklenen tam-ekran görsel modu, metin editörlerinin dönüm noktası oldu. Her ne kadar bu fikir piyasadaki başka yazılımlardan alınmış olsa da, en yaygın kullanıma ulaşanı kendisi olmuştu. Zamanla *ex*'in görsel modu *(visual mode)* o kadar çok kullanıldı ki, doğrudan *vi* komutu ile *ex* programı tam-ekran görsel modunda çalıştırılır oldu.

Yıllar sonra Bram Moolenaar tarafından *vi* kodu geliştirildi ve *vim* *(Vi IMproved)* açığa çıktı. Günümüz klavye ve sistemlerine en yatkın olanı olduğu gerekçesiyle biz de örneklerimizde *vim* kullanacağız.

