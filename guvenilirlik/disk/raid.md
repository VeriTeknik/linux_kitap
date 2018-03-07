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



[^1]: Bilgisayar bilimlerinde 4bit'ten, yani yarım Byte'tan oluşan birime bir _nibble_ denilir.

