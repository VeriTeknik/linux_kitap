# RAID Biçimleri

## RAID 1

Oldukça sık kullanılan bu RAID yapısı, iki diske ihtiyaç duyar. Bir diske yazılan verinin _aynısı_ diğer diske de yazılır. Böylece disklerden birisi arızalandığında, verinin aynısı diğer diskte de olduğu için kayıp yaşanmaz. Bu işleme _mirroring_ denilir. Bu işlemde yazma açısından bir performans kazancı sağlanılmaz, teorik üst limit tek diskin yazma hızına eşittir. Okuma işlemlerinde hızlanma sağlanır. RAID kartları bir blok veriyi okuyacağı zaman verinin yarısını bir diskten, diğer yarısını diğer diskten okuyacağı için teorik olarak 2 kat okuma hızlanması sağlanır.

Ayrıca _harcanan_ disklerin yarısının toplam kullanılabilir kapasite olacağını göz önünde bulundurmak gerekir.

RAID 1 yapılarında 2'den fazla disk kullanılabilir (RAID kartı desteklediği sürece) ancak kullanılacak disk sayısı çift olmak zorundadır. Pratikte hemen hiçbir zaman 2'den fazla disk ile RAID 1 yapılmaz, hatta çoğu RAID kartı da bunu desteklemez. Genellikle 2'den fazla disk kullanımında RAID 5, RAID 10 gibi yapılar tercih edilir.

Bütün RAID yapılarında olduğu gibi, RAID 1'de de kullanılacak iki diskin aynı boyutta olması (hatta aynı marka/model olması) tercih edilir. Eğer farklı boyutta diskler kullanılırsa, küçük boyutu olan diskin boyutu kadar alan kullanılabilir olur.

![](../.gitbook/assets/raid1-book.png)

Yukarıdaki şekilde, işletim sisteminin `11001010` bitlerini yazma talebini RAID kartının nasıl gerçekleştirdiği görülmektedir. Kart, verinin aynısını her iki diske de yazmaktadır (mirroring). İşletim sistemi tarafından Disk A ve Disk B tek fiziksel diskmiş gibi görünür. Mirroring işlemi RAID kartının sorumluluğundadır. Yazılan veri okunacağı zamansa verinin yarısı Disk A'dan, diğer yarısı da Disk B'den okunur. Örneğin `1100` kısmı Disk A'dan, `1010` kısmı da Disk B'den okunabilir. Böylece paralel okuma sağlandığı için işletim sistemi açısından okuma hızı artmış olur.

Çoğunlukla büyük verinin depolanması gerektiği durumlarda farklı RAID yapıları seçilip, ilgili sunucunun işletim sistemini iki SSD (veya benzer şekilde hızlı disk) kullanılarak RAID 1 yapmak tercih edilen yöntemlerden birisidir. Böylece işletim sisteminin çalıştığı disklerden birinin bozulması durumunda işlemler durmaz, ayrıca büyük verinin depolandığı RAID dizilerinden ayrılmış olur.

## RAID 0

Tıpkı RAID 1 gibi en az iki diske ihtiyaç duyar, ancak bu sefer mirroring uygulanmaz. Yazılacak bloğun yarısı ilk diske, diğer yarısı da ikinci diske yazılır. Bu işleme _data striping_ denilir. Böylece hem okuma, hem de yazma işlemlerinde yaklaşık 2 kat hızlanma sağlanılır. Ayrıca _harcanan_ disklerin tamamının kapasitesi kullanılmış olur. Ancak disklerden birisinin bozulması durumunda veri kurtarılamaz olur. Bu yüzden verinin güvenilir olarak saklanması gerektiği durumlarda değil, hızlı işlem yapılması istenilen durumlarda kullanılır. Örneğin işletim sisteminizin `tmp` dizinini RAID 0'lık bir diziden oluşturursanız, bu dizine dosya yazarken ciddi hız kazancı elde edersiniz.

![](../.gitbook/assets/raid0-small.png)

Yukarıdaki şekilde RAID kartının striping işlemini nasıl yaptığı görülmektedir. İşletim sistemi `11001010` bitlerini yazmak istemektedir, RAID kartı bu veriyi alıp ikiye böler ve yarısını Disk A'ya, diğer yarısını da Disk B'ye yazar. RAID 1 örneğine kıyasla hem disk kapasitesi kullanımının azalmadığı, hem de okuma/yazma işlemlerinin hızlanacağı bu örnekte görülebilmektedir.

RAID kartları RAID 0'ı 2 diskten fazla kartlarla rahatlıkla gerçekleştirebilir. Örneğin Dell PERC 9 kartları 32 diske kadar RAID 0 dizisi oluşturabilir. Ancak 32 disk'ten herhangi birisinin bozulması durumunda bütün dizinin bozulacağını unutmamak gerekir. İstatistiksel olarak disk sayısı arttıkça, bir diskin bozulma ihtimali de artacağından, bütün dizinin bozulması ihtimalini de artırmış olursunuz.

## RAID 10

RAID 1 ve RAID 0'ın birleştirilmiş halidir. En az 4 disk gerektirir. Diskler kendi içlerinde ikili gruplar halinde RAID 1 dizileri oluştururlar. Ortaya çıkan iki disk de RAID 0 ile birleştirilmiş olur. Böylece 4 diskin toplam kapasitesinin yarısı kadar kullanılabilir alan olur. Hem okuma hem de yazma işlemlerinde hızlanma sağladığı, üstelik güvenilirlik sağladığı için oldukça sık tercih edilen bir RAID yapısıdır. Aynı anda 2 diskin arıza vermesinde bile çalışabilir, ancak arıza veren disklerin farklı RAID 1 dizilerinde olması gerekir.

![](../.gitbook/assets/raid10-small.png)

Yukarıdaki şekilde RAID 10'un nested (iç içe geçmiş) yapısı görülmektedir. İşletim sistemi `11001010` verisini yazmak istediğinde, RAID kartı önce bunu RAID 0 gibi iki parçaya böler (striping), elde ettiği her parçayı ise RAID 1 olacak şekilde ayrı disklere yazar. Bu örnekten görülebileceği gibi sistem iki diskin bozulmasına tolerans göstermektedir, ancak bozulan diskler aynı RAID 1 dizisinde olmamalıdır. Örneğin aynı anda Disk C ve Disk D bozulursa, RAID yapısı bozulur. Öte yandan aynı anda Disk B ve Disk C bozulursa, sistem çalışmaya devam eder.

## RAID 4

Günümüzde neredeyse hiç kullanılmasa da, RAID 5'i anlayabilmek için RAID 4'ü öğrenmek gerekir. RAID 4, en az 3 disk gerektiren, 1 diskin bozulması durumunda çalışmayı sürdürebilen bir yapıya sahiptir. Okuma işlemlerinde yaklaşık iki kat hızlanma sağlar, yazma işlemlerindeyse hızlanma sağlamaz ve harcanan disklerin 2/3'ü kadar alan kullanılabilir olur. Burada belirttiğimiz 2/3 oranı aslında, 3 diskten oluşan RAID 4 dizileri için geçerlidir. Detayını birazdan göreceğiz.

RAID 4, tıpkı RAID 0'da gördüğümüz gibi _striping_ işlemi yapar, yani yazılacak veriyi parçalara bölerek farklı disklere yazar. Ancak bir diske daha, ilgili bitlerin _parity_'sini yazar. Burada _partiy_ ile kastedilen, bir mantıksal işlemin sonucudur.

Bitlerin yazılmaları işlemi sırasında, farklı disklere yazılan bitlerin XOR (exclusive or) operasyonuyla elde edilen çıktısını _parity disk_ olarak belirlenen diske yazar. Bu işlemi anlamak için AND, OR gibi mantıksal işlemlere hızlıca göz atıp, XOR'un nasıl bir farklılık sağladığını ve neden RAID 4 için XOR'un tercih edildiğine bakalım.

AND işlemi, yalnızca girdilerinin ikisinin de TRUE olduğu durumlarda TRUE değeri döndürür, diğer durumlarda FALSE döndürür. Örneğin `0 AND 1 = 0` olurken, `1 AND 1 = 1` sonucu elde edilir.

Öte yandan OR işlemi, girdilerinden en az birinin TRUE olduğu durumlarda TRUE değeri döndürür, diğer durumlarda (iki girdinin de FALSE olması durumunda) FALSE değeri döndürür.

XOR işlemi ise, yalnızca girdilerin değerleri birbirinden farklıysa TRUE değeri döndürür. Yani `1 XOR 1 = 0` olurken, `1 XOR 0 = 1` sonucu elde edilir.

Mantıksal işlemlerin bütün olası girdi ve çıktılarının yansıtıldığı tablolara _Truth Table_ ismi verilmektedir. Bu tabloları bir çeşit çarpım tablosu gibi düşünebilirsiniz. AND, OR ve XOR için Truth Table'lar oluşturacak olursak:

AND için Truth Table

| INPUT 1 | INPUT 2 | OUTPUT (AND) |
| ------- | ------- | ------------ |
| 0       | 0       | 0            |
| 0       | 1       | 0            |
| 1       | 0       | 0            |
| 1       | 1       | 1            |

OR için Truth Table

| INPUT 1 | INPUT 2 | OUTPUT (OR) |
| ------- | ------- | ----------- |
| 0       | 0       | 0           |
| 0       | 1       | 1           |
| 1       | 0       | 1           |
| 1       | 1       | 1           |

XOR için Truth Table

| INPUT 1 | INPUT 2 | OUTPUT (XOR) |
| ------- | ------- | ------------ |
| 0       | 0       | 0            |
| 0       | 1       | 1            |
| 1       | 0       | 1            |
| 1       | 1       | 0            |

XOR işleminin matematiksel açıdan birkaç özelliği vardır ve bu yönleriyle AND ve OR'dan ayrılır. Birincisi, işlemin çift yönlü olmasıdır. Bu şu anlama gelir: `A XOR B = C` ise, her zaman `C XOR B = A` veya `C XOR A = B` sonucu elde edilecektir. Bu durum OR ve AND için her zaman geçerli değildir.

Örneğin Truth Table'lardan bakarak görebileceğimiz gibi, `0 XOR 1 = 1`, ve yine `1 XOR 1 = 0` olduğu için işlem çift yönlüdür. Öte yandan aynı girdileri OR için kullanacak olsayık, `0 OR 1 = 1`, ancak `1 OR 1 = 1`olduğu için farklı sonuç elde edecektik. Kısacası OR mantıksal işlemi XOR gibi çift yönlü değildir. Benzer şekilde AND operatörünün de çift yönlü olmadığı gösterilebilir. `1 AND 0 = 0` iken, `0 AND 0 = 0` sonucu elde edilir.

XOR'un çift yönlü olma özelliği bize şu avantajı sağlar: Diske yazacağımız veriyi iki parçaya bölersek (_striping_) ve her iki parçayı XOR'layıp çıktısını (_parity_) ayrı bir diske yazarsak, disklerden herhangi birisi zarar gördüğünde diğer bitler yardımıyla kayıp veriyi yeniden oluşturabiliriz.

Örneğin diskimize `11001010` bit'lerinden oluşan 1 Byte'lık bir veriyi yazmak istersek, RAID 4 veriyi aşağıdaki şekilde parçalayacaktır.

![](../.gitbook/assets/raid4-small.png)

Burada görüldüğü üzere 1 Byte'lık veri iki parçaya bölünmüştür. Buradaki her bir nibble diğer yarısıyla XOR'lanarak elde edilen _parity bit_'ler üçüncü disk olan Disk C'ye yazdırılmaktadır. Bu yüzden şekilde parity bit'ler yeşil kutucuk ile gösterilmiştir. Aslında işletim sistemi diske yazmak için `0110` bit'lerini asla göndermemiştir, bunu RAID kartı hesaplar ve parity diskine yazar.

Böyle bir senaryoda veriyi okumak istediğimizde, ilk nibble'ı Disk A'dan, ikinci nibble'ı da Disk B'den okuyacağımız için 2 kat hızlanma sağlanmış olur. Aslında yazarken de tıpkı RAID 0'da olduğu gibi bu disklere 2 kat hızlı yazılmaktadır ancak hem XOR parity'sinin hesaplanması işlemi, hem de bunun sonucunun Disk C'ye yazılması işlemi beklendiği için, bu örnekteki 1 Byte'lık verinin yazılma hızında takıldığımız nokta, Disk C'nin yazma hızı olur. Bu yüzden RAID 4 sistemlerde yazma hızı açısından bir kazanım sağlanılmaz.

Bu örnekteki RAID dizimizde bir felaket senaryosu düşünelim. Disklerden herhangi birisi bozulsa bile, XOR'un çift yönlü işlem özelliği sayesinde kalan iki disk ile kayıp diski yeniden oluşturabiliriz. Örneğin Disk B'nin bozulması durumunda,`Disk A XOR Disk C` işlemi, yani `1100 XOR 0110 = 1010` olacağından Disk B oluşturulabilir. Gördüğünüz gibi sistem 1 diskin bozulmasına müsaade etmektedir. Burada yapılan işlemin sırası önemli değildir. Yani `A XOR C` yerine `C XOR A` yapabilirdik, yine aynı sonucu elde ederdik. Bu da XOR işleminin _değişme özelliği (commutative property)_ sayesindedir.

XOR'un bir diğer özelliği ise _birleşme özelliği (associative property)_ olarak geçer. Birleşme özelliği kısaca şu anlama gelmektedir. `A XOR ( B XOR C) = (A XOR B) XOR C`. Bu özellik sayesinde RAID dizimiz üzerinde işlem yapacağımız girdi sayısını 2'den fazla yapabiliriz demektir. Eğer yukarıdaki örneğimize dördüncü bir disk ekleseydik, 3 disk'e veriyi striping ile parçalayarak yazabilir, 3 diski XOR'layarak sonucu parity disk'e yazabilirdik. Üstelik XOR'un değişme özelliği sayesinde işlemlerin sırası bile önemli olmamaktadır.

Bunun için aşağıdaki daha kapsamlı örneğe bakalım.

![](../.gitbook/assets/raid4-single-small.png)

Burada ilk örnekteki gibi 2 diske veriyi yazıp üçüncüyü parity amacıyla kullanmak yerine, 3 diske veriyi yazıp dördüncüyü parity amacıyla kullanıyoruz. Örneğin daha anlaşılabilir olabilmesi için daha önceki örneklerimizden daha büyük veri yazılıyor burada. 12 bit'lik veri segmentlerinin nasıl parçalandığı renkleriyle görülüyor. Disk A, B ve C veri yazmak için kullanılıyor, Disk D ise parity'nin saklanması için. Burada görebileceğimiz gibi Disk D üzerindeki bir parity'nin hesaplanması için `A XOR B XOR C` işlemi yapılması gerekiyor. Benzer şekilde, eğer herhangi bir disk bozulursa, kalan disklerdeki veri XOR'lanarak veri kurtarma işlemi yapılabiliyor. Artık veri okuma işini 3 diskten yapabildiğimiz için, bu senaryoda 3 kat okuma hızlanması sağlanıyor. Her ne kadar yazma işleminde orijinal veriyi 3'e böldüğümüz için 3 kat hızlandığımız algısı oluşsa da, bütün yazma işlemleri parity bit'in hesaplanması ve Disk D'ye yazılmasını beklemek zorunda olduğundan, yazma hızımız _en fazla_ Disk D'nin yazma hızı kadar olabilir.

Bu bölümün başında bahsettiğimiz gibi, her ne kadar RAID 4'ün (ve birazdan göreceğimiz RAID 5'in) harcanan disklerin 2/3'ünün kullanılabilir alan sağladığı ve 2 kat okuma hızlanması sağladığı genel kanı olsa da, bu sadece 3 disk kullanılması durumunda geçerlidir. Son örneğimizde görebileceğimiz üzere 4 disk kullanıldığında 3 kat okuma hızlanması sağlanılmaktadır, ayrıca harcanan disklerin 3/4'ü kadar alan kullanılabilir durumdadır. Bu yüzden RAID 4 için harcanan disk karşılığında elde edilen verimlilik hesabını kısaca şöyle belirtmek gerekir.

N := Harcanan Disk Sayısı olmak üzere\
S := Bir diskin boyutu olmak üzere

Okuma hızı = (N - 1) kat hızlanma sağlar.\
Kullanılabilir Alan = (N - 1)\*S

Örneğin 1 TB'lık 6 tane disk ile RAID 4 dizisi oluşturmak istersek,\
&#x20;kat okuma hızlanması sağlanılır.\
&#x20;kullanılabilir alan sağlanır.

## RAID 5

RAID 5'in aslında RAID 4'ten neredeyse hiçbir farkı yoktur. Yine benzer şekilde XOR işlemlerini kullanarak parçalanmış verinin partiy diskte karşılığının tutulmasını sağlar. Yine N-1 kat okuma hızlanması sağlar ve yine en fazla tek diskin bozulmasını destekler. RAID 5'in tek farkı, parity disk kavramını ortadan kaldırması ve parity bit'lerinin bütün diskler üzerine yayılmasını sağlamasıdır. RAID 4'te gördüğümüz 4 diskli örneği RAID 5'e uyarlayacak olursak tam olarak aşağıdaki durumla karşılaşırız.

![](../.gitbook/assets/raid5-small.png)

Burada görüleceği üzere, tek bir parity disk yoktur, parity bit'leri (yeşil kutularla gösterilenler) bütün disklere yayılmış durumdadır. Burada temel amaç, tek diske binecek yazma stresini bütün disklere yaymaktır. Her ne kadar RAID dizilerinde elimizden geldiğince aynı diskleri kullansak da, aynı markanın aynı modeli bile bazı durumlarda okuma/yazma hızlarında ve ömürlerinde farklılık gösterebilmektedir. RAID 4'teki yazma hızı limiti her halde parity disk'in yazma limitine takılırken, RAID 5'te bu limit parity bitlerinin hangi diskte bulunduğuna göre farklılık göstermektedir. Bu sayede diskler üzerindeki stres dağıtılmakta, ayrıca darboğaza denk gelme olasılığı azaltılmaktadır.

Bugün neredeyse hiçbir sunucu RAID 4 ile kurulmaz, RAID 5 kullanılır.
