# RAID

Günümüz bilişim altyapıları, özellikle verinin en düşük seviyede dahi güvenilir bir biçimde yazılabilir ve okunabilir olmasına ihtiyaç duymaktadır. Herhangi bir sunucu üzerinde çalışılırken sabit disklerden birinin bozulması hem sistemin çalışmasını durdurabilir, hem de verilerde kurtarılamaz kayıplara sebep olabilir. Bu tip durumların önüne geçmek için birden fazla sabit diskin bir arada kullanılarak daha güvenilir diziler oluşturması işlemine RAID \(Redundant Array of Independent Disks\) denilir. İlk defa University of California araştırmacıları tarafından 1988'de yayımlanan "A Case for Redundant Arrays of Inexpensive Disks \(RAID\)" makalesinde anlatılmıştır. Bu makalede ucuz disklerin paralel kullanımları yardımıyla güvenilir ve verimli disk okuma/yazma işlemlerinin sağlanabileceği anlatılmaktadır. Makale isminden anlaşılacağı gibi ucuz diskler \(_inexpensive disks_\) hedef alınarak başlanılsa da, günümüzde disklerin ucuz olmasından daha ziyade sistemin güvenilir ve verimli olmasını hedeflendiğinden, _inexpensive_ sözcüğü yerine _independent _\(bağımsız\) getirilerek RAID kısaltmasına sadık kalınmıştır.

RAID yapıları sayesinde sunucular üzerindeki birden fazla disk, işletim sistemine sanki tek bir diskmiş gibi gösterilebilir, böylece hem güvenilirlik hem de çeşitli açılardan performans kazanımları sağlanılabilir. Bir sunucu üzerinde RAID yapısı kurabilmek için RAID kontrol kartına ihtiyaç duyulur. Çoğu sunucuda bu kart dahil olacağı gibi, bazı sistemlere sonradan takılabilir. Ayrıca işletim sistemleri, RAID kontrol kartı bulunmayan sistemler için yazılımsal RAID desteği de sağlamaktadır. Ancak yazılımsal RAID'in bazı matematiksel işlemleri daha yavaş yapacağı, ayrıca disk bozulmaları durumunda sistemi kapatmadan tamir etme işleminin yapılamayacağını göz önünde bulundurmakta fayda var.

RAID ile ilgili unutulmaması gereken noktalardan birisi, bir _yedekleme mekanizması_ olmadığı. RAID, yapılan işlemlerin güvenilir ve/veya verimli bir biçimde yatayda yayılmasını sağlamaktadır. Ancak bu yayılma _hatalar_ ve _istenmeyen_ durumları da kapsamaktadır. Bir dosyayı sildiğinizde RAID dizinindeki bütün disklerden ilgili elemanları silinmiş olur ve bunu geri döndüremezsiniz. Bu yüzden sisteminizin RAID ile çalışacağını kurguladığınız zaman, yedekleme ihtiyacınızın ortadan kalktığını düşünmeyin.

Birden fazla RAID çeşidi bulunmaktadır ve hepsinin kendince avantajları ve dezavantajları bulunur. Altyapınızı oluştururken hangi RAID tipini seçeceğinize karar vermek için altında yatan teknolojiyi anlamakta fayda var. Bu amaçla en sık kullanılan RAID yapılarını inceleyeceğiz.

## RAID 1

Oldukça sık kullanılan bu RAID yapısı, iki diske ihtiyaç duyar. Bir diske yazılan verinin _aynısı_ diğer diske de yazılır. Böylece disklerden birisi arızalandığında, verinin aynısı diğer diskte de olduğu için kayıp yaşanmaz. Bu işleme _mirroring_ denilir. Bu işlemde yazma açısında bir performans kazancı sağlanılmaz, teorik üst limit tek diskin yazma hızına eşittir. Okuma işlemlerinde hızlanma sağlanır. RAID kartları bir blok veriyi okuyacağı zaman verinin yarısını bir diskten, diğer yarısını diğer diskten okuyacağı için teorik olarak 2 kat okuma hızlanması sağlanır.

Ayrıca _harcanan_ disklerin yarısının toplam kullanılabilir kapasite olacağını göz önünde bulundurmak gerekir.

RAID 1 yapılarında 2'den fazla disk kullanılabilir \(RAID kartı desteklediği sürece\) ancak kullanılacak disk sayısı çift olmak zorundadır. Genellikle 2'den fazla disk kullanımında RAID 5, RAID 10 gibi yapılar tercih edilir.

Bütün RAID yapılarında olduğu gibi, RAID 1'de de kullanılacak iki diskin aynı boyutta olması \(hatta aynı marka/model olması\) tercih edilir. Eğer farklı boyutta diskler kullanılırsa, küçük boyutu olan diskin boyutu kadar alan kullanılabilir olur.

![](/guvenilirlik/disk/raid_images/raid1-book.png)

Yukarıdaki şekilde, işletim sisteminin `11001010` bitlerini yazma talebini RAID kartının nasıl gerçekleştirdiği görülmektedir. Kart, verinin aynısını her iki diske de yazmaktadır \(mirroring\). İşletim sistemi tarafından Disk A ve Disk B tek fiziksel diskmiş gibi görünür. Mirroring işlemi RAID kartının sorumluluğundadır. Yazılan veri okunacağı zamansa verinin yarısı Disk A'dan, diğer yarısı da Disk B'den okunur. Örneğin `1100` kısmı Disk A'dan, `1010` kısmı da Disk B'den okunabilir. Böylece paralel okuma sağlandığı için işletim sistemi açısından okuma hızı artmış olur.

Çoğunlukla büyük verinin depolanması gerektiği durumlarda farklı RAID yapıları seçilip, ilgili sunucunun işletim sistemini iki SSD \(veya benzer şekilde hızlı disk\) kullanılarak RAID 1 yapmak tercih edilen yöntemlerden birisidir. Böylece işletim sisteminin çalıştığı disklerden birinin bozulması durumunda işlemler durmaz, ayrıca büyük verinin depolandığı RAID dizilerinden ayrılmış olur.

## RAID 0

Tıpkı RAID 1 gibi iki diske ihtiyaç duyar, ancak bu sefer mirroring uygulanmaz. Yazılacak bloğun yarısı ilk diske, diğer yarısı da ikinci diske yazılır. Bu işleme _data striping_ denilir. Böylece hem okuma, hem de yazma işlemlerinde yaklaşık 2 kat hızlanma sağlanılır. Ayrıca _harcanan_ disklerin tamamının kapasitesi kullanılmış olur. Ancak disklerden birisinin bozulması durumunda veri kurtarılamaz olur. Bu yüzden verinin güvenilir olarak saklanması gerektiği durumlarda değil, hızlı işlem yapılması istenilen durumlarda kullanılır. Örneğin işletim sisteminizin `tmp` dizinini RAID 0'lık bir diziden oluşturursanız, bu dizine dosya yazarken ciddi hız kazancı elde edersiniz.

![](/guvenilirlik/disk/raid_images/raid0-small.png)

Yukarıdaki şekilde RAID kartının striping işlemini nasıl yaptığı görülmektedir. İşletim sistemi `11001010` bitlerini yazmak istemektedir, RAID kartı bu veriyi alıp ikiye böler ve yarısını Disk A'ya, diğer yarısını da Disk B'ye yazar. RAID 1 örneğine kıyasla hem disk kapasitesi kullanımının azalmadığı, hem de okuma/yazma işlemlerinin hızlanacağı bu örnekte görülebilmektedir.

## RAID 10

RAID 1 ve RAID 0'ın birleştirilmiş halidir. En az 4 disk gerektirir. Diskler kendi içlerinde ikili gruplar halinde RAID 1 dizileri oluştururlar. Ortaya çıkan iki disk de RAID 0 ile birleştirilmiş olur. Böylece 4 diskin toplam kapasitesinin yarısı kadar kullanılabilir alan olur. Hem okuma hem de yazma işlemlerinde hızlanma sağladığı, üstelik güvenilirlik sağladığı için oldukça sık tercih edilen bir RAID yapısıdır. Aynı anda 2 diskin arıza vermesinde bile çalışabilir, ancak arıza veren disklerin farklı RAID 1 dizilerinde olması gerekir.

![](/guvenilirlik/disk/raid_images/raid10-small.png)

Yukarıdaki şekilde RAID 10'un nested \(iç içe geçmiş\) yapısı görülmektedir. İşletim sistemi `11001010` verisini yazmak istediğinde, önce RAID kartı bunu RAID 0 gibi iki parçaya böler \(striping\), elde ettiği her parçayı ise RAID 1 olacak şekilde ayrı disklere yazar. Bu örnekten görülebileceği gibi sistem iki diskin bozulmasına tolerans göstermektedir, ancak diskler aynı RAID 1 dizisinden olmamalıdır. Örneğin aynı anda Disk C ve Disk D bozulursa, RAID yapısı bozulur. Öte yandan aynı anda Disk B ve Disk C bozulursa, sistem çalışmaya devam eder.

## RAID 4

Günümüzde neredeyse hiç kullanılmasa da, RAID 5'i anlayabilmek için RAID 4'ü öğrenmek gerekir. RAID 4, en az 3 disk gerektiren, 1 diskin bozulması durumunda çalışmayı sürdürebilen bir yapıya sahiptir. Okuma işlemlerinde yaklaşık iki kat hızlanma sağlar, yazma işlemlerindeyse hızlanma sağlamaz ve harcanan disklerin 2/3'ü kadar alan kullanılabilir olur. Burada belirttiğimiz 2/3 oranı aslında, 3 diskten oluşan RAID 4 dizileri için geçerlidir. Detayını birazdan göreceğiz.

RAID 4, tıpkı RAID 0'da gördüğümüz gibi _striping_ işlemi yapar, yani yazılacak veriyi parçalara bölerek farklı disklere yazar. Ancak bir diske daha, ilgili bitlerin _parity_'sini yazar. Burada _partiy_ ile kastedilen, bir mantıksal işlemin çıktısıdır.

Bitlerin yazılmaları işlemi sırasında, farklı disklere yazılan bitlerin XOR \(exclusive or\) operasyonuyla elde edilen çıktısını _parity disk_ olarak belirlenen diske yazar. Bu işlemi anlamak için AND, OR gibi mantıksal işlemlere hızlıca göz atıp, XOR'un nasıl bir farklılık sağladığını ve neden RAID 4 için XOR'un tercih edildiğine bakalım.

AND işlemi, yalnızca girdilerinin ikisinin de TRUE olduğu durumlarda TRUE değeri döndürür, diğer durumlarda FALSE döndürür. Örneğin `0 AND 1 = 0` olurken, `1 AND 1 = 1` sonucu elde edilir.

Öte yandan OR işlemi, girdilerinden en az birinin TRUE olduğu durumlarda TRUE değeri döndürür, diğer durumlarda \(iki girdinin de FALSE olması durumunda\) FALSE değeri döndürür.

XOR işlemi ise, yalnızca girdilerin değerleri birbirinden farklıysa TRUE değeri döndürür. Yani `1 XOR 1 = 0` olurken, `1 XOR 0 = 1` sonucu elde edilir.

Mantıksal işlemlerin bütün olası girdi ve çıktılarının yansıtıldığı tablolara _Truth Table_ ismi verilmektedir. Bu tabloları bir çeşit çarpım tablosu gibi düşünebilirsiniz. AND, OR ve XOR için Truth Table'lar oluşturacak olursak:

---

AND için Truth Table

| INPUT 1 | INPUT 2 | OUTPUT \(AND\) |
| :--- | :--- | :--- |
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

---

OR için Truth Table

| INPUT 1 | INPUT 2 | OUTPUT \(OR\) |
| :--- | :--- | :--- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

---

XOR için Truth Table

| INPUT 1 | INPUT 2 | OUTPUT \(XOR\) |
| :--- | :--- | :--- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

---

XOR işleminin matematiksel açıdan birkaç özelliği vardır ve bu yönleriyle AND ve OR'dan ayrılır. Birincisi, işlemin çift yönlü olmasıdır. Bu şu anlama gelir: `A XOR B = C` ise, her zaman `C XOR B = A` veya `C XOR A = B` sonucu elde edilecektir. Bu durum OR ve AND için her zaman geçerli değildir.

Örneğin Truth Table'lardan bakarak görebileceğimiz gibi, `0 XOR 1 = 1`, ve yine `1 XOR 1 = 0` olduğu için işlem çift yönlüdür. Öte yandan aynı girdileri OR için kullanacak olsayık, `0 OR 1 = 1`, ancak `1 OR 1 = 1`olduğu için farklı sonuç elde edecektik. Kısacası OR mantıksal işlemi XOR gibi çift yönlü değildir. Benzer şekilde AND operatörünün de çift yönlü olmadığı gösterilebilir. `1 AND 0 = 0` iken, `0 AND 0 = 0` sonucu elde edilir.

XOR'un çift yönlü olma özelliği bize şu avantajı sağlar: Diske yazacağımız veriyi iki parçaya bölersek \(_striping_\) ve her iki parçayı XOR'layıp çıktısını \(_parity_\) ayrı bir diske yazarsak, disklerden herhangi birisi zarar gördüğünde diğer bitler yardımıyla kayıp veriyi yeniden oluşturabiliriz.

Örneğin diskimize `11001010` bit'lerinden oluşan 1 Byte'lık bir veriyi yazmak istersek, RAID 4 veriyi aşağıdaki şekilde parçalayacaktır.

![](/guvenilirlik/disk/raid_images/raid4-small.png)

Burada görüldüğü üzere 1 Byte'lık veri iki parçaya bölünmüştür. Buradaki her bir nibble[^1] diğer yarısıyla XOR'lanarak elde edilen _parity bit_'ler üçüncü disk olan Disk C'ye yazdırılmaktadır. Bu yüzden şekilde parity bit'ler yeşil kutucuk ile gösterilmiştir. Aslında işletim sistemi diske yazmak için `0110` bit'lerini asla göndermemiştir, bunu RAID kartı hesaplar ve parity diskine yazar.

Böyle bir senaryoda veriyi okumak istediğimizde, ilk nibble'ı Disk A'dan, ikinci nibble'ı da Disk B'den okuyacağımız için 2 kat hızlanma sağlanmış olur. Aslında yazarken de tıpkı RAID 0'da olduğu gibi bu disklere 2 kat hızlı yazılmaktadır ancak hem XOR parity'sinin hesaplanması işlemi, hem de bunun sonucunun Disk C'ye yazılması işlemi beklendiği için, bu örnekteki 1 Byte'lık verinin yazılma hızında takıldığımız nokta, Disk C'nin yazma hızı olur. Bu yüzden RAID 4 sistemlerde yazma hızı açısından bir kazanım sağlanılmaz.

Bu örnekteki RAID dizimizde bir felaket senaryosu düşünelim. Disklerden herhangi birisi bozulsa bile, XOR'un çift yönlü işlem özelliği sayesinde kalan iki disk ile kayıp diski yeniden oluşturabiliriz. Örneğin Disk B'nin bozulması durumunda,`Disk A XOR Disk C` işlemi, yani `1100 XOR 0110 = 1010` olacağından Disk B oluşturulabilir. Gördüğünüz gibi sistem 1 diskin bozulmasına müsaade etmektedir. Burada yapılan işlemin sırası önemli değildir. Yani `A XOR C` yerine `C XOR A` yapabilirdik, yine aynı sonucu elde ederdik. Bu da XOR işleminin _değişme özelliği \(commutative property\)_ sayesindedir.

XOR'un bir diğer özelliği ise _birleşme özelliği \(associative property\)_ olarak geçer. Birleşme özelliği kısaca şu anlama gelmektedir. `A XOR ( B XOR C) = (A XOR B) XOR C`. Bu özellik sayesinde RAID dizimizde üzerinde işlem yapacağımız girdi sayısını 2'den fazla yapabiliriz demektir. Eğer yukarıdaki örneğimize dördüncü bir disk ekleseydik, 3 disk'e veriyi striping ile parçalayarak yazabilir, üç diski XOR'layarak sonucu parity disk'e yazabilirdik. Üstelik XOR'un değişme özelliği sayesinde işlemlerin sırası bile önemli olmamaktadır.

Bunun için aşağıdaki daha kapsamlı örneğe bakalım.

![](/guvenilirlik/disk/raid_images/raid4-single-small.png)

Burada ilk örnekteki gibi 2 diske veriyi yazıp üçüncüyü parity amacıyla kullanmak yerine, 3 diske veriyi yazıp dördüncüyü parity amacıyla kullanıyoruz. Örneğin daha anlaşılabilir olabilmesi için daha önceki örneklerimizden daha büyük veri yazılıyor burada. 12 bit'lik veri segmentlerinin nasıl parçalandığı renkleriyle görülüyor. Disk A, B ve C veri yazmak için kullanılıyor, Disk D ise parity'nin saklanması için. Burada görebileceğimiz gibi Disk D üzerindeki bir parity'nin hesaplanması için `A XOR B XOR C` işlemi yapılması gerekiyor. Benzer şekilde, eğer herhangi bir disk bozulursa, kalan disklerdeki veri XOR'lanarak veri kurtarma işlemi yapılabiliyor. Artık veri okuma işini 3 diskten yapabildiğimiz için, bu senaryoda 3 kat okuma hızlanması sağlanıyor. Her ne kadar yazma işleminde orijinal veriyi 3'e böldüğümüz için 3 kat hızlandığımız algısı oluşsa da, bütün yazma işlemleri parity bitin hesaplanması ve Disk D'ye yazılmasını beklemek zorunda olduğundan, yazma hızımız _en fazla_ Disk D'nin yazma hızı kadar olabilir.

Bu bölümün başında bahsettiğimiz gibi, her ne kadar RAID 4'ün \(ve birazdan göreceğimiz RAID 5'in\) harcanan disklerin 2/3'ünün kullanılabilir alan sağladığı ve 2 kat okuma hızlanması sağladığı genel kanı olsa da, bu sadece 3 disk kullanılması durumunda geçerlidir. Son örneğimizde görebileceğimiz üzere 4 disk kullanıldığında 3 kat okuma hızlanması sağlanılmaktadır, ayrıca harcanan disklerin 3/4'ü kadar alan kullanılabilir durumdadır. Bu yüzden RAID 4 için harcanan disk karşılığında elde edilen verimlilik hesabını kısaca şöyle belirtmek gerekir.

N := Harcanan Disk Sayısı olmak üzere  
S := Bir diskin boyutu olmak üzere

Okuma hızı = \(N - 1\) kat hızlanma sağlar.  
Kullanılabilir Alan = \(N - 1\)\*S

Örneğin 1 TB'lık 6 tane disk ile RAID 4 dizisi oluşturmak istersek,  
$$6-1=5$$ kat okuma hızlanması sağlanılır.  
$$(6-1)*1TB= 5TB $$ kullanılabilir alan sağlanır.

## RAID 5

RAID 5'in aslında RAID 4'ten neredeyse hiçbir farkı yoktur. Yine benzer şekilde XOR işlemlerini kullanarak parçalanmış verinin partiy diskte karşılığının tutulmasını sağlar. Yine N-1 kat okuma hızlanması sağlar ve yine en fazla tek diskin bozulmasını destekler. RAID 5'in tek farkı, parity disk kavramını ortadan kaldırması ve parity bit'lerinin bütün diskler üzerine yayılmasını sağlamasıdır. RAID 4'te gördüğümüz 4 diskli örneği RAID 5'e uyarlayacak olursak tam olarak aşağıdaki durumla karşılaşırız.

![](/guvenilirlik/disk/raid_images/raid5-small.png)

Burada görüleceği üzere, tek bir parity disk yoktur, parity bit'leri \(yeşil kutularla gösterilenler\) bütün disklere yayılmış durumdadır. Burada temel amaç, tek diske binecek yazma stresini bütün disklere yaymaktır. Her ne kadar RAID dizilerinde elimizden geldiğince aynı diskleri kullansak da, aynı markanın aynı modeli bile bazı durumlarda okuma/yazma hızlarında ve ömürlerinde farklılık gösterebilmektedir. RAID 4'teki yazma hızı limiti her halde parity disk'in yazma limitine takılırken, RAID 5'te bu limit parity bitlerinin hangi diskte bulunduğuna göre farklılık göstermektedir. Bu sayede diskler üzerindeki stres dağıtılmakta, ayrıca darboğaza denk gelme olasılığı azaltılmaktadır.

Bugün neredeyse hiçbir sunucu RAID 4 ile kurulmaz, RAID 5 kullanılır.

## Stripe Size, Stripe Width ve Veriye Erişim Biçimleri

RAID yapılarını incelerken verilerin parçalanması durumunu \(_striping_\) gördük. Özellikle RAID 0, RAID 5, RAID 10 gibi sıkça kullanılan yapılarda veri parçalanarak disklere yayılmaktadır. Bu durum iki terimin doğmasına neden olur.

Verileri kaç parçaya böldüğümüz, kısacası stripe'ların toplam genişliği _stripe width_ olarak adlandırılır. Eğer elimizde 2 diskten oluşan bir RAID 0 dizisi varsa, stripe width'imiz 2'dir. Genellikle stripe width tanımlanırken parity bit'lerin yazıldığı diskler de sayılır. Bu yüzden örneğin 7 disk'ten oluşan bir RAID 4 dizisinin stripe width'i 7'dir, 6 değildir. Stripe width arttıkça okuma \(ve bazen yazma\) hızımızın artacağı aşikar.

Stripe size ise başka bir kavramı ifade eder ve sistemin verimliliği açısından doğru seçilmesi önemlidir. Hatırlarsanız RAID 0 bölümündeki örneğimizde 1 Byte'lık verimizi iki tane nibble'a parçalamıştık, böylece her bir diske 4 bit'lik veri yazmıştık. Bu örnekte 4 bit, her bir diske yazılan atomik veri boyutu olduğundan, stripe size'ımız 4 bit olacaktır. Kısacası stripe size, yazma işlemi sırasında RAID kartının veriyi disklere bölerken kullanacağı en küçük parçacık birimine karşılık gelir. Bu yüzden ismi stripe _size_'dır, her bir parçacığın _boyutu_. Bazen stripe size yerine_ stripe length, chunk size, block size_ gibi terimler kullanılabilir, tamamı aynı anlamı ifade etmektedir.

Her ne kadar örneklerimizde stripe size'ı 4 bit olarak kullanmış olsak da, pratikte RAID kartları bu kadar küçük değerler kullanmazlar. Değerler genellikle 2kB mertebesinden başlar ve MB mertebesine kadar gider \(2kB, 4kB, 8kB, 16kB şeklinde ikilik olarak artar\). Stripe size'ın ne kadar büyük veya küçük seçileceği tamamen disklere yazılacak verilerin büyüklüğü, okuma/yazma sıklığı, bir veriye ulaşıldığında komşu verilerin ne kadar sık kullanılacağı, sıralı okuma yapılıp yapılmayacağı gibi bir çok parametreye bağlı olduğundan, RAID kartlarında bu seçenek ayarlanabilir olarak bırakılır. Bu yüzden stripe size, verinin _konumlandırılması_ ve _aktarılması_ işlemlerinin hızını etkiler.

Diskinizden bir veriyi okumak istediğinizde, diskin manyetik kafasının öncelikle ilgili verinin bulunduğu fiziksel bölgeye gitmesi gerekir. Bu zaman alan bir süreçtir ve _verinin konumlandırılması_'ndan kastımız budur. Bir sözlükten bir kelimeyi bulma işlemine benzetilebilir. Önce sözlüğün içinde ilgili harfe, sonra da ilgili kelimeye ulaşmak gerekir.

Disk üzerinde veriyi bulduktan sonra, veriyi okumaya başlarız. _Verinin aktarılması_'ndan kastımız da budur. Artık veri konumlandırılmıştır, şimdi verinin işletim sistemine transfer edilmesi gerekmektedir. Sözlük analojimizde bu, ilgili kelimeyi bulduktan sonra artık okumaya başlamaya karşılık gelir.

Yapılan işleme göre disk üzerinde bazen rastgele bölgeler okumamız gerekebilir. Bazen bir dosyayı bütün olarak okumamız gerekebilir ancak okuma sırası önemli değildir, bazense dosyayı sırayla \(baştan sona\) okumamız gerekebilir. Sözlük analojimizde karşılıklarını düşünecek olursak, sırasıyla _bahar_, _zeytin_ ve _bahçe_ sözcüğünün anlamlarına bakmak istediğimizde, sözlüğün başı ve sonu arasında adresleme işlemleri yapmamız gerekir. Bu yüzden 3 kere adresleme işlemi yaparız. Eğer bu sözcüklerin anlamlarını öğrenme sıramız önemli değilse, o zaman üç farklı sözlük kullanabiliriz ve üç farklı kişi sözlüklere bakabilir. Böylece her birini konumlandırırken de, anlamlarını okurken de hızlanma sağlarız. Eğer elimizde 3 okuyucu yoksa, hiç değilse _bahar_ ve _bahçe_ sözcüklerini birbirlerine yakın oldukları için ilk başta okuyabilir, arından _zeytin_ sözcüğüne geçebiliriz. Ancak bütün bunlar tamamen verilerin nasıl okunması istendiğine bağlıdır.

Bu örneklerde verinin büyüklüğü de önemlidir. Veriyi bir kez konumlandırdıktan sonra, veriyi okumak da zaman alacaktır ve veriyi konumlandırma süresi ile okuma süresi arasındaki dengeyi de gözetmek gerekir. Eğer sözlüğümüzdeki kelimelerin açıklaması çok uzunsa, örneğin 20 sayfasa, _bahar-zeytin-bahçe_ sıralamasından _bahar-bahçe-zeytin_ sıralamasına geçmek neredeyse hiç avantaj  sağlamayabilir bile, çünkü bir kelimeyi okumak, kelimeyi sözlükte bulmaktan hatırı sayılır miktarda uzun olacaktır.

Stripe size küçüldükte, yazılan dosyalar daha küçük parçalara bölündüğü için ortalama bir dosyanın çok diske yazılması olasılığı artar. Dosyalar çok fazla diske yazılmış olacağı için diskler üzerinde _konumlandırılması_ zorlaşır. Ancak bir kez konumlandırıldı mı, birden fazla disk tarafından okunacağı için okuma işlemi hızlanır.

Stripe size'ın büyütülmesi ise tam tersi etkiyi yaratır. Yazılacak dosya boyutlarına göre bir dosyanın az sayıda diske yayılması \(hatta tek diske yayılması\) olasılığı artar. Bu durumda ilgili dosyaya erişmek daha kolay olacaktır çünkü tek diskteki tek sektöre ulaşmak çok daha hızlı olur, ancak bu dosyayı paralel olarak okuyamayız, bu durumda okuma/yazma işlemlerimiz tek diskin performansıyla sınırlandırılmış olur.

Aşağıdaki örnek, aynı disklerle oluşturulmuş iki tane RAID 0 dizisini göstermektedir. Her dizinin 4 elemanı \(diski\) vardır. İkisine de aynı dosyalar yazılır. Dosyalar farklı renklerle gösterilmiştir. Yani kırmızı dosya, pembe, dosya, mavi dosya ve yeşil dosya olmak üzere 4 tane dosya var. Soldaki konfigürasyonda stripe size 4kB seçilmiştir, sağdaki konfigürasyonda ise aynı dosyalar aynı disklere 64kB stripe size ile yazılmıştır.![](/guvenilirlik/disk/raid_images/stripe size.png)Şekildeki bloklar stripe size'ı işaret etmektedir. Bu örnekte kırmızı dosya 4kB boyuta, pembe dosya 44 kB, mavi dosya 120 kB ve yeşil dosya 212 kB boyuta sahiptir. Örnekten görülebileceği gibi her iki konfigürasyonda da kırmızı dosya tek diske yazılır, çünkü stripe size 4kB'dan küçük olmadığı sürece, 4kB'lık dosyayı parçalayamayız. İlginç olan, pembe dosyanın durumudur. 4kB stripe size kullanıldığında pembe dosya 4 diske yayılırken, 64kB stripe size kullanıldığında bütün dosya tek diske yazılmıştır. Pembe dosyayı soldaki RAID dizisinden okumak istersek, 4 kat hızlı okuyacağız demektir, ancak dosyanın konumunun hesaplanması için 4 farklı diskte adresleme işlemi yapılması gerekmektedir. Kısacası dosyaya _ilk erişim_ daha yavaş olacaktır, ancak bir kez eriştik mi hızla okuyacağız demektir. Öte yandan sağdaki RAID dizisinde pembe dosyayı hemen buluruz, ancak okuma hızımız tek diskin okuma hızından öteye gidemez.

Farkındaysanız yeşil dosya her iki senaryoda da 4 diske yayılmış durumda. Bunun birkaç sebebi olabilir, birincisi seçilen stripe size yeterince büyük değildir, ikincisi ise dosyanın beraber barındırıldığı diğer dosyalardan farklı bir yapıya sahip olması olabilir. Örneğin bir sürü küçük dosyamız varsa ancak bir tane devasa dosyamız varsa bile, RAID dizisi üzerindeki konumlaması diğerlerinden farklı olacaktır. Bu bile performansı etkileyen etmenleden birisidir. Bu yüzden genellikle benzer yapıdaki dosyaların benzer disk yapılarına yazılması tercih edilebilir, ancak problemi her zaman bu kadar sınırlandırarak uygulamak pratikte mümkün olmaz.

Bir diğer nokta, dosyaların büyüklüğünden ziyade erişim biçimidir. Rastgele okuma işlemleri ile sıralı okuma işlemleri hem disk üzerinde ciddi farklılıklar yaratır, hem de diske verilerin yazılma biçimlerini buna göre belirlemek faydalı olur. Bütün diskler sıralı okuma işlemini rastgele okuma işleminden daha hızlı yaparlar, ancak örneğin SSD'lerin rastgele okuma ile sıralı okuma işlemleri arasındaki hız farkı çok daha azdır. Bu durumda rastgele okuma işlemini çok yapacak birisi \(veritabanı kullananlar, oyun oynayanlar vs.\) manyetik diskler yerine SSD kullanmayı tercih edebilir. Öte yandan sıralı okuma işlemi örneğin bazı sıkıştırılmış dosyaların okunmasında kullanılır, veya güvenlik kameralarının verilerinin depolanması gibi konularda kullanılır. Hemen hemen hiçbir durumda bir video stream edilirken rastgele parçalarını yükleme ihtiyacı duymayız, videoyu baştan sona doğru _lineer_ olarak yüklemeyi tercih ederiz. Böyle bir durumda SSD yerine manyetik disk kullanımı tercih edilebilir olur \(fiyat/performans açısından\).

Video ve veritabanı erişimi örneklerindeki rastgele erişim ve sıralı erişim problemleri RAID Stripe Size ile de ilişkilidir. Yukarıdaki örnekte eğer pembe dosya sıralı okunması istenilen bir dosyaysa, tek diskte yer alması _dosyanın nerede bulunduğunu_ hızla bulmamızı sağlayacağı için erişim süresini çok kısaltır, zaten sıralı okuma yapılacağı için de disklerden paralel olarak verinin alınması bir anlam sağlamaz dolayısıyla büyük stripe size ile saklanması akıllıca olacaktır. Ancak eğer bu bir video dosyası değil de bir veritabanı dosyasının parçasıysa, birden fazla işlem bu dosyanın farklı noktalarına erişmek isteyecektir dolayısıyla bu kadar büyük bir dosyanın farklı disklere yayılması tercih edilebilir. Öte yandan bunun için bir genel geçer kural olduğunu söylemek yanlış olacaktır. Eğer sıralı okuma ihtiyacınız yoksa ve büyük dosyalar barındıracaksanız \(örneğin büyük backup dosyaları\) dosyaların transfer hızı, konumlandırma hızından önemli olacağı için küçük stripe size kullanarak çok diske yayılmalarını sağlamak verimli olabilir.

## Cache Mekanizmaları

RAID kontrol kartları tıpkı küçük birer bilgisayar gibi çeşitli bileşenlerden oluşurlar. Bunlar arasında bir işlemci ve bir tampon bellek \(_cache_\) de bulunur. Söz konusu tampon bellek, standart diskler kadar yavaş değildir, üzerinde çok daha küçük bir alan bulunur ancak bu bölgeye veri yazıp okumak çok daha hızlı olur. Verilerin yazılma ve okunma işlemlerinde performans kazancı sağlamak için RAID kartı bu tampon belleği kullanır, ancak bu bölgeyi kullanma stratejisi farklılıklar gösterebilir. RAID kartının tampon belleği kullanma stratejisi sistemden beklenen güvenilirlik seviyesinden, okuma/yazma işlemlerinin yoğunluğunun oranına, sıralı veri okuma ihtiyacının olup olmayışından yazılan verinin yakın zamanda okunup okunmayacağına göre değişkenlik gösterir. Bu ihtiyaçlara yönelik hemen her RAID kartı üreticisinin desteklediği ortak stratejiler bulunur ve bunlara RAID Cache Mekanizmaları \(_RAID Caching Mechanisms_\) denilebilir.

### Write-Through Cache

Aslında ismi hemen hemen ele veriyor kendisini. Bu mekanizma, yazma işlemlerinin tamamının _cache aracılığıyla_ yapılacağını belirtir. Kısacası veriyi RAID kartı önce cache'ye yazar, ardından disklere yazar. Disklere yazma işlemi tamamlandıktan sonra veriler cache'den silinmez, ve işletim sistemine "yazma işlemini bitirdim" sinyali gönderilir.

![](/guvenilirlik/disk/raid_images/cache-write-through1-small.png)

Yukarıdaki şekilde gerçekleşen adımlar aşağıdaki gibidir:

1. İşletim sistemi bir veriyi yazmak istediğini RAID kartına söyler. RAID kartı bunu kendi üzerindeki CPU'ya yönlendirir.
2. CPU yazma işlemini Cache'ye yapar.
3. Cache'ye yazma işlemi tamamlandıktan sonra, Cache'deki bu yeni veri Disklere yazılır.
4. Diskler yazma işlemini tamamladığı sinyalini RAID kartına yönlendirir.
5. RAID kartı yazma işleminin tamamlandığını işletim sistemine bildirir.

Burada önemli olan nokta, yazılan verinin hala Cache'de olmasıdır. Bu işlemin ardından, eğer işletim sistemi bir okuma isteği gönderirse, ve okumak istediği veriler "henüz yeni cache'ye yazılmış veriler"se, o zaman okuma işleminde ciddi hız kazancı sağlanır.

![](/guvenilirlik/disk/raid_images/cache-write-through2-small.png)

Yukarıdaki şekil, yazma işlemi gerçekleştirildikten sonra, işletim sisteminin "biraz önce yazılan veriyi" istediği takdirde izleyeceği yolları göstermektedir. Kırmızı oklar yazma işlemi, yeşil oklar ise okuma işlemini göstermektedir. Buradan görüleceği üzere, yeni yazılan veri hala cache üzerinde olduğu sürece, okuma işleminde disklere ihtiyaç olmayacaktır. Bu durum çok ciddi bir performans artışına sebep olur. Ancak istenilen verinin cache'de olmaması durumunda tabii ki disklerden veri okunacaktır.

Write-Through Cache'leme mekanizması

### Write-Around Cache

Bu mekanizma da yine adından anlaşılabileceği üzere, yazma işlemlerinde "cache'nin etrafından dolanmaktadır". Kısacası yazma işlemleri cache kullanılmadan yapılmakta, ancak \(eğer ayarlanmışsa\) okuma işlemlerinde cache kullanılmaktadır. Bu durum özellikle okumanın yoğun olduğu sistemlerde verimli olabilir, çünkü cache'nin tamamı okuma işlemine ayrılmış olur. Ancak burada okunan verinin henüz yazılmış veri olmaması gerekmektedir. Eğer yeni yazdığımız veriyi okuyacaksak, bu verinin cache'de kalması hızlı erişim için ciddi avantajlar sağlar.

![](/guvenilirlik/disk/raid_images/cache-write-around1-small.png)

Yukarıdaki örnekte gerçekleşen adımlar şu şekildedir:

1. İşletim sistemi yazma isteğini RAID kartına iletir.
2. RAID kartı CPU'su gelen veriyi doğrudan diske yazar.
3. Disk, yazma işleminin tamamlandığını RAID kartına bildirir.
4. RAID kartı yazma işleminin tamamlandığını işletim sistemine bildirir.

Görüldüğü üzere, cache hiç kullanılmamıştır. Dolayısıyla ilk yazma işleminde cache boş olacaktır. Şimdi bu senaryoda veri okunacağı zaman gerçekleşecek işlemlere bakalım.

![](/guvenilirlik/disk/raid_images/cache-write-around2-small.png)

1. İşletim sistemi okuma isteğini RAID kartına gönderir.
2. RAID kartı ilgili bloğun cache'de olup olmadığını kontrol eder.
3. Cache, üzerinde bu bilgi olmadığı için disklerden okur ve cache'ye yazar.
4. Cache okunan bilginin kopyasını CPU'ya iletir.
5. RAID kartı işletim sistemine bilgiyi iletir.

Bu işlemler sonucunda, cache'de artık bir miktar veri bulunmaktadır. Bundan sonra tekrar işletim sistemi istek gönderdiğinde, eğer ilgili veri biraz önce cache'ye yazılan veriyse, okuma işlemi hızlanacaktır.

![](/guvenilirlik/disk/raid_images/cache-write-around3-small.png)

Görüldüğü üzere, tekrar okuma işleminde \(eğer daha önce okunan veri talep edilmişse\) disklere gerek kalmaz.

### Write-Back Cache

Cache mekanizmaları arasındaki en verimli, ancak en güvensiz yöntemlerden birisidir. Bu yöntem temel olarak RAID kartının cache'sine güvenir, ve gelen yazma isteğini cache'ye yazmayı tamamladığı anda işletim sistemine "yazmayı tamamladım" sinyalini gönderir. Ardından cache'deki veriyi disklere yazma işlemini gerçekleştirir.

![](/guvenilirlik/disk/raid_images/cache-write-back-small.png)

1. İşletim sistemi yazma talebini RAID kartına gönderir.
2. CPU yazılacak bilgiyi cache'ye yazar.
3. RAID kartı işletim sistemine "verileri diske yazdım" sinyali gönderir.
4. RAID kartı cache'deki veriyi disklere yazar.

Bu mekanizmanın avantajı ortada, işletim sistemi açısından yazma işlemi son derece hızlı gerçekleşir çünkü RAID kartının cache'sine yazma hızı kendisi için limit olur. Eğer İşletim sisteminin yazma talepleri, cache ile diskler arasındaki bağlantının yetişemeyeceği hıza çıkarsa, cache'nin dolmasından dolayı performans en fazla Write-Through Cache mekanizmasında olduğu haline kadar gerileyebilir.

Bu mekanizma çok uygun görünse de, verinin disklere güvenli bir biçimde yazılmadan önce işletim sisteminin verinin yazdığını varsaymasını sağlar. Eğer cache'deki veri tamamen disklere yazılmadan önce sistemin elektiriği kesilirse, cache üzerindeki veri disklere aktarılamayacaktır. Bu durum ciddi veri kayıplarına neden olur.

RAID kartı ve benzeri kartlardaki cache tampon bellekleri genellikler bilgisayarların RAM'lerine benzer yapıya sahiptirler. Bu tip belleklere _kırılgan_ oldukları için _volatile memory_ denilir. Sisteme elektrik sağlandığı takdirde bellekteki veriler güvendedir, ancak elektrik kesildiği anda bellekteki veriler tamamen kaybolur. Bu tip belleklerin son derece hızlı olmasının yanında getirdiği bir dezavantajdır. Özellikle RAID kartlarında cache üzerindeki elektiriğin kesilmesi durumunda bütün sistemi etkileyecek veri kayıplarına neden olacağı için, bu tip kartlara pil konulur. Olası bir elektrik kesintisinde pil cache'yi besler, böylece cache üzerindeki veriler _pilin ömrü yettiği sürece_ silinmez. RAID kartı sisteme yeniden elektrik verildiğini tespit ettiği takdirde cache'deki verileri disklere yazmaya çalışır, böylece veri kaybı yaşanmaz.

Bu senaryo her ne kadar ideal gibi görünse de, bütün şartlar beklendiği gibi olmamaktadır. Bazı durumlarda pilin ömrü çok kısa olabilmekte, bazen sistem yeniden ayağa kalktığında işletim sistemi çok fazla disk okuma/yazma işlemine ihtiyaç duyduğu için darboğazlara sebep olabilmektedir. Ayrıca beklenmedik elektrik kesintileri en çok disklere zarar vermektedir. Disklerin elektrik kesintisi sonucu kurtarılamaz durumda zarar görmesi rastlanan durumlardandır.

Özellikle RAID kartındaki pilin kullanımı konusunda kart üreticileri çeşitli yöntemler geliştirmiştir. Örneğin çoğu üretici write-back mekanizmasını desteklediği halde, RAID kartının pili olmaması durumunda bu özelliği kullanılmaz hale getirmekte ve write-through modunda çalışmaktadır. Bazı üreticilerse pilin kalan ömrünü ölçüp, eğer elektrik kesintisinde 24 saat dayanabilecek durumda değilse write-back mekanizması devredışı bırakmaktadır.

### Read Ahead Cache

Bu cache'leme stratejisi yazma ile ilgili değil, okuma işlemleri ile ilgilidir. Normal şartlar altında işletim sistemi bir _parçanın_ okunmasını talep ettiğinde, RAID kartı ilgili veriyi disklerden okuyup cache'ye yazar, sonra işletim sistemine sunar. İşletim sistemi tekrar aynı veriyi isterse, bu verinin cache'de olması durumunda diskleri tekrar kullanmayıp cevap vermiş olur. Bu haliyle aslında read ahead yapmamış olur. Genellikle RAID kartlarında _No Read Ahead_ seçeneği bu anlama gelmektedir.

Öte yandan, eğer verilerimizde çoğunlukla sıralı okuma yapıyorsak, veya bir bloğa ulaştığımızda çoğunlukla o bloktan sonra gelen bloğa erişme ihtiyacı duyuyorsak, işletim sistemi herhangi bir parçayı RAID kartından istediğinde, RAID kartı akıllı davranıp "ondan sonra gelen parçaları da" okuyup cache'ye alabilir. Aşağıdaki örnek fikir verecektir.

![](/guvenilirlik/disk/raid_images/cache-readahead-small.png)

1. İşletim sistemi RAID kartından **A** verisini istemektedir.
2. RAID kartı Cache'ye bakar, burada **A** bulunmadığı için Disklerden kopyalama işlemi başlatılır.
3. Bu noktada Read Ahead açıksa, Disklerden sadece **A** verisi değil, **A** verisinden sonra gelen birkaç veri daha cache'ye kopyalanır. Bu örnekte **ABCDEF **kopyalanmakta.
4. Cache **A **verisinin kopyalanma işinin bittiğini söyler.
5. RAID kartı ilgili veriyi işletim sistemine gönderir.

Eğer işletim sistemi bir sonraki okuma işlemini \(veya cache boşaltılıncaya kadar herhangi bir zamanda\) **B** veya **C** verisini talep edecek olursa, bu verileri diskten okumakla zaman kaybetmemiş oluruz, cache bu bilgileri sağlar.

Bu senaryo bazı durumlarda dezavantaj yaratabilir. Birincisi, eğer sıralı okuma yapıyorsak bile, belki genellikle **A**'yı okumak istediğimizde, sonrasında **B** ve **C**'yi okumak bizim için yetiyor. Ancak cache'de **DEF** verilerini de saklayarak boş yere cache'de yer işgal etmiş olduk. Bazı RAID kartları read ahead cache mekanizması yapılırken, "ne kadar ilerideki veriyi tampon belleğe aktaracağımızı" seçmemizi de sağlamaktadır. Ancak bu her kartın sunduğu bir özellik olmayabilir.

Bir diğer nokta, boş yere okunan veriler cache'de yer işgal etmekten ötürü, fiziksel disklerden de boş yere okuma işlemine sebep oldu. Disklerin okuma hızı limitleri var ve biz boş yere disk hızını işgal etmiş olduk. Ayrıca, disklerin fiziksel işlem yaptığını unutmamak lazım. **AB** okumak yerine **ABCDEF** okuyarak diski 4 kat kullanmış olacağımız için disklerin ömrünü etkileyeceğini, fiziksel problemlere daha kısa sürede sebep olabileceğini göz önünde bulundurmak gerekir.

### Adaptive Read Ahead

Read ahead'in getirdiği problemleri bir nebze çözmek için bazı RAID kartı üreticileri adaptive read ahead algoritması kullanmaktadır. Biraz önceki senaryo örneğinde gidecek olursak, işletim sistemi RAID kartından **A** verisini istediğinde, RAID kartı disklerden sadece **A** verisini okuyup cache'lemekte ve işletim sistemine sunmaktadır. Bu noktada herhangi bir read ahead işlemi yapılmamaktadır. Daha sonra eğer işletim sistemi **B** verisini talep ederse, RAID kartı "daha önce **A** verisi de talep edilmişti, öyleyse muhtemelen **CDEF** verilerini de talep edecek" kararını verip bu sefer disklerden **BCDEF** verilerini okuyup cache'ye yazar. Bu sayede eğer gerçekten sıralı okuma yapılıyorsa **CDEF** talepleri geldiğinde veriler çok daha hızlı sağlanacaktır.

Read ahead mekanizmasına oranla dezavantajı, **A** verisinde yaşadığımız yavaşlığın **B** verisinde tekrar etmiş olması oldu. Ancak eğer sistemimizde rastgele okumalar ve sıralı okumalar kısmen bir arada yapılıyorsa, o zaman  adaptive read ahead cache mekanizması bizim için daha uygun olabilir.

## Karşılaşılabilecek Diğer Terimler

RAID ayarları yaparken veya hakkında okurken karşılaşabileceğiniz bazı terimleri bu bölüm dahilinde kullanma ihtiyacı hissetmedik. RAID mekanizmasının anlatımında doğrudan ihtiyaç duyulmayan ancak yardımcı bir takım terimler oldukları için, karşılaştığınızda yabancılık çekmemek adına bazılarını yazma ihtiyacı hissettik.

**Physical/Virtual Disk:** Adından anlaşılacağı üzere fiziksel ve sanal disklerdir. Fiziksel diskler, RAID kartına bağladığınız disklerdir. Bu diskler kullanılarak oluşturduğunuz bir RAID dizisine ise sanal disk adı verilir. Örneğin 3 diskten bir RAID 5 dizisi oluşturmak isterseniz, 3 fiziksel disk kullanıp bir sanal disk oluşturmuş olursunuz. Eğer bu RAID 5 dizisini 4 diske genişletmek isterseniz de, 4. diski ilgili sanal diske eklemeniz gerekir. Bu durumda sistemdeki fiziksel disk sayısı artmış olur ancak sanal disk sayısı sabit kalır.

**JBOD:** Just A Bunch Of Disks. RAID kartları veya storage'lar üzerine bir takımlar diskler taktığımızda ancak bunlar üzerinde _herhangi bir RAID mekanizması_ kurmadığımızda, bu diskler sadece bir yığın disk olarak dururlar ve sistem tarafından erişilebilir olurlar. Bu yüzden bunlara kısaca JBOD denilmektedir.

**Hot Swap:** Bu aslında storage veya sunucunuzun desteklediği bir yapıdır. Sistem çalışırken sisteme yeni diskler takılıp sökülebilmesini ifade etmektedir. Kısacası sistem _hot_ iken _disk swap_ yapabildiğimizin göstergesidir. Genellikle fiziksel RAID kontrol kartı bulunan sunucular ve hemen her storage bunu destekler. Özellikle disklerden birinin bozulması durumunda RAID yapısının tamir edilirken sistemin kesintiye uğramaması için önemlidir.

**Prepare Remove: **Hot swap destekleyen bir sistemde, bir diski sökmeden önce bu diski sökeceğinizi cihaza bildirebilirsiniz. Böylece kartlar o disk üzerinde işlem yapmayı durdurur, diskin spin işlemleri yavaşlatılarak duraklatılır. Eğer böyle bir işlemi kartınız destekliyorsa, fiziksel diskin sağlığı ve açısından yapmanızda fayda var.

**Hot Standby:** Bir RAID dizisi kurarken, disklerden birisinin arızalanması durumunda yedekte duran bir diskin otomatik olarak kullanılmasını sağlayabilirsiniz. Örneğin 2 diskten oluşan bir RAID 1 diziniz varsa, 3. bir disk takıp bu dizi için kendisini _hot standby_ olarak ayarlarsanız, RAID 1 disklerinden birisi bozulduğu takdirde, sistem hot stanby diskini kullanarak sağlıklı çalışmaya devam edecektir. Bu durumda mühendisin tek  sorumluluğu bozuk diski sökmek \(ve problemin neden kaynaklandığını incelemek\) olacaktır. Bazı durumlarda diski sunucu üzerinde bırakıp, otomatik tamir ayarı yapılmaması durumuna c_old standby_ denilmektedir. Öte yandan cold standby terimi bazı durumlarda "müdahale için sistemin kapatılması gerektiği" anlamına da gelmektedir. Kısacası hot swap desteklemeyen sistemler için rafta bekletilen disklere de cold standby denildiğiyle karşılaşabilirsiniz.

## RAID Mekanizmaları Hakkında Bir Takım Notlar

Bu bölümde RAID mekanizmalarının nasıl çalıştığını, farklılıklarını ve özellikle bir RAID kartı üzerinde konfigürasyon yapıldığında karşılaşılabilecek önemli ayarların ne işe yaradığını, bazı iç mekanizmaların çalışma prensiplerini irdeledik. Konu her ne kadar burada anlatıldığından çok daha kapsamlı olsa da, bu bölümde öğrendikleriniz sisteminizi kurarken bilinçli RAID yapıları oluşturmanıza yeterli olacaktır.

Burada anlatılanlar dışındaki RAID yapıları, burada anlatılan temel felsefelere dayanmaktadır ve bu bölümdeki kavramları anladıysanız diğer sistemler hakkında okuma yaptığınızda problem yaşamayacaksınızdır.

Örneğin RAID 4 ve RAID 5'in nasıl çalıştığını anladıysanız, RAID 6'nın "bir tane daha parity disk sağlayabilmek için XOR dışında bir matematiksel işlem daha sağladığı" bilgisi yeterli olacaktır. Bu sayede RAID 6'nın RAID 5'ten farkının 2 disk çökmesine izin verdiği ortaya çıkar. XOR işlemini zaten parity disklerinden birinde kullandığımız için farklı bir işlem kullanılır \(Galois Field Computation\) ve bu kitabın kapsamı dışında olduğu için anlatılmamıştır, ancak felsefe aynı.

Benzer şekilde RAID 10'un nasıl bir şey olduğunu, RAID 5'in nasıl bir şey olduğunu anladıysak, RAID 50'nin nasıl oluşturulduğunu rahat bir biçimde anlayabiliriz.

Her ne kadar burada RAID kartları açısında bir anlatım sunduysak da, storage cihazlarının da aslında RAID kartı ve bir sürü disk barındıran sunucular gibi davrandığını unutmamak gerekir. Kısacası burada öğrendiklerimiz storage cihazlarını konfigüre ederken de karşımıza çıkmaktadır.

Hem yazma, hem de okuma cache'leme mekanizmalarında kart üreticileri farklı stratejiler veya farklı ayarlar sunabilmektedir. Örneğin bazı RAID kartları veya storage cihazları RAID kartının cache'si dışında, içindeki disklerin cache'lerinin kullanılm politikaları hakkında ayarlar sunmaktadır. Burada kullanılan stratejiler de bu bölümde anlattıklarımızla paralellik göstermektedir.

RAID yapıların birden fazla diske \(çoğunlukla ilişkilendirilmiş\) veri yazmaktan sorumlu olduğunu unutmayın. Özellikle veri yazmak, veri okumaktan çok daha hassas bir işlemdir ve işlemi yarıda kalması veya bozuk yapılması, tamir edilemez hasarlara sebep olabilir. Bu yüzden RAID kartınızın veya storage cihazınızın hangi özellikleri desteklediğini, hangi fiziksel olanaklara imkan tanıdığına hakim olun. RAID kartınızda pil olup olmadığını, varsa pilin ömrünün ne kadar olduğunu, dolayısıyla kullandığınız cache'leme mekanizmasına göre ne kadar süreli bir elektrik kesintisini etkilenmeden atlatabileceğinizi bilmenizde fayda var. Ayrıca bazı RAID kartları veya storage cihazları partnership \(ortaklık\) kurabilmektedir. Böylece aynı marka/model iki storage cihazı birbirine bağlandığı takdirde, ortak cache alanları kullanabilmekte, veya cache'leri mirror edip birinin bozulması durumunda diğeri üzerinden işlemlerine devam edebilmektedir.

RAID mekanizmaları genellikle sistem kurulduğunda üzerine çalışılan, sonra problem çıkmadıkça uzun süre giriş yapılmayan bölümlerdir. Dolayısıyla kontorl mekanizmanızın uzaktan erişimi varsa giriş bilgilerini not edin, sisteminize nasıl bir yapı kurduğunuzu hatırlayın, ve destekliyorsa bozulma/alarm durumlarında size uyarı mesajları \(email\) gönderebildiğinden emin olun, uyarı mekanizmalarını test edin.

RAID yapınızı kurmadan önce, büyüme/küçülme planlarınızı göz önünde bulundurun ve kartlarınızın planlarınızı destekleyip desteklemediğini gözden geçirin. Örneğin birçok kart RAID 1 için 2'den fazla disk desteklememektedir. Bazı kartlar RAID 1'den RAID 5 veya RAID 10 gibi yapılara doğrudan göç etmenize olanak sağlamaktadır. Bu kartlarda RAID 1 dizinize yeni bir disk ekleyip "bu diziyi RAID 5'e çevir" dediğinizde gerekli işlemlere başlar ve veri kaybı yaşamazsınız. Öte yandan bazı kartlar buna izin vermez, yeni bir RAID 5 dizisi oluşturu verilerinizi _elle_ bu yeni diziye taşımanız gerekebilir.

Son olarak, daha önce belirttiğimiz gibi, RAID'in bir _yedekleme_ mekanizması olmadığını unutmayın. Geçmişe dönük verilerinizin yedeğini alın, ve bunu RAID sistemler üzerinde tutun. Ancak RAID'in amacı yedekleme yapmak değildir, güvenilir ve verimli ortamlar sağlamaktır.

[^1]: Bilgisayar bilimlerinde 4bit'ten, yani yarım Byte'tan oluşan birime bir _nibble_ denilir.

