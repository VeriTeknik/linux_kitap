# Standart Girdi ve Çıktı

GNU/Linux ve benzeri POSIX sistemlerin en güçlü olduğu yönlerden birisi, programların standart girdi ve çıktılarının yönlendirilmesidir. Özellikle birden fazla programın işlemlerini birbirlerine kolayca gönderebilme gücü sağlayan bu yöntem, aslında UNIX'in oluşturulduğu dönemdeki temel felsefeleri en iyi yansıtan özelliklerinden biridir.

1978'de yayımlanan _The Bell System Technical Journal'ın UNIX Time-Sharing System_ dokümanının önsözünde Douglas McIlroy, UNIX'in tasarlanması ve kullanılmasında temel alınan kritik noktaları 4 madde halinde listeler. Bu maddelerden ilki ilerleyen yıllarda çok popüler olmuştur. Maddenin tam hali şu şekildedir:

> Make each program do one thing well. To do a new job, build afresh rather than complicate old programs by adding new "features".

Bu maddede McIlroy, bir program yazılırken, bu programın _tek_ _bir şeyi iyi yapması gerektiğini_** **vurgular. Yeni bir iş yapılacağı zaman, mevcut programa yeni "özellikler" eklemek yerine, yeni bir program yazılmasının daha doğru olduğunu savunur.

Önsözde paylaşılan maddelerden ikincisinin tam hali ise şu şekildedir:

> Expect the output of every program to become the input to another, as yet unknown, program. Don't clutter output with extraneous information. Avoid stringently columnar or binary input formats. Don't insist on interactive input.

Bu madde başlı başına çok fazla şey söyler, ve bir süre GNU/Linux sistemler üzerinde yazılmış programları kullanan herkes, aslında bu maddelerin çoğu programda uygulandığını görecektir. Öncelikle bu bölümde bahsedeceğimiz girdi ve çıktıların yönlendirilmesi gerekliliği, maddenin ilk cümlesinde ifade edilmiştir. McIlroy, her programın çıktısının aynı zamanda girdi olarak tasarlanması gerektiğini, hatta ne iş yaptığını bilmediği bir programın girdisi olarak kullanılabileceğinin gözönünde bulundurulması gerektiğini belirtir. Çıktıların gereksiz bilgilerle kirletilmemesi gerektiğini, kolon bazlı girdilerin veya binary girdilerin dayatılmasından kaçınılması gerektiğini ifade eder. Ayrıca, gerekmediğinde _etkileşimli girdiden_ kaçınılması gerektiğini belirtir. Çünkü etkileşimli girdi, bir programa standart girdi olarak veri göndermenin zorlaşması demektir.

Eğer girdi ve çıktı yönlendirmeleri ile ilgili hiç tecrübeniz yoksa, yukarıdaki satırlar size soyut gelebilir. Bu bölümü okuduktan sonra yukarıdaki anlayışın ne ifade ettiği biraz daha netleşecektir. Ayrıca kitabın ilerleyen bölümlerini okuduktan sonra tekrar dönüp bu felsefeyi okumanızda fayda olabilir.

## 

## 

## 

## 

## 



