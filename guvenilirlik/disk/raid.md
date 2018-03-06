# RAID

Günümüz bilişim altyapıları, özellikle verinin en düşük seviyede dahi güvenilir bir biçimde yazılabilir ve okunabilir olmasına ihtiyaç duymaktadır. Herhangi bir sunucu üzerinde çalışılırken sabit disklerden birinin bozulması hem sistemin çalışmasını durdurabilir, hem de verilerde kurtarılamaz kayıplara sebep olabilir. Bu tip durumların önüne geçme için birden fazla sabit diskin bir arada kullanılarak daha güvenilir diziler oluşturması işlemine RAID \(Redundant Array of Independent Disks\) denilir. İlk defa University of California araştırmacıları tarafından 1988'de yayımladıkları "A Case for Redundant Arrays of Inexpensive Disks \(RAID\)" makalesinde anlatılmıştır. Bu makalede ucuz disklerin paralel kullanımları yardımıyla güvenilir ve verimli disk okuma/yazma işlemlerinin sağlanabileceği anlatılmaktadır. Makale isminden anlaşılacağı gibi ucuz diskler \(_inexpensive disks_\) hedef alınarak başlanılsa da, günümüzde teknoloji disklerin ucuz olmasından daha ziyade sistemin güvenilir ve verimli olmasını hedeflediğinden, _inexpensive_ sözcüğü yerine _independent _\(bağımsız\) getirilerek RAID kısaltmasına sadık kalınmıştır.

RAID yapıları sayesinde sunucular üzerindeki birden fazla disk, işletim sistemine sanki tek bir diskmiş gibi gösterilebilir, böylece hem güvenilirlik hem de çeşitli açılardan performans kazanımları sağlanılabilir. Bir sunucu üzerinde RAID yapısı kurabilmek için RAID kontrol kartına ihtiyaç duyulur. Çoğu sunucuda bu kart dahil olacağı gibi, bazı sistemlere sonradan takılabilir. Ayrıca işletim sistemleri, RAID kontrol kartı bulunmayan sistemler için yazılımsal RAID desteği de sağlamaktadır. Ancak yazılımsal RAID'in bazı matematiksel işlemleri daha yavaş yapacağı, ayrıca disk bozulmaları durumunda sistemi kapatmadan tamir etme işleminin yapılamayacağını göz önünde bulundurmakta fayda var.

RAID ile ilgili unutulmaması gereken noktalardan birisi, bir _yedekleme mekanizması_ olmadığı. RAID yapılan işlemlerin güvenilir ve/veya verimli bir biçimde yatayda yayılmasını sağlamaktadır. Ancak bu yayılma _hatalar_ ve _istenmeyen_ durumları da kapsamaktadır. Bir dosyayı sildiğinizde RAID dizinindeki bütün disklerden ilgili elemanları silinmiş olur ve bunu geri döndüremezsiniz. Bu yüzden sisteminizin RAID ile çalışacağını kurguladığınız zaman, yedekleme ihtiyacınızın ortadan kalktığını düşünmeyin.

Birden fazla RAID çeşidi bulunmaktadır ve hepsinin kendince avantajları ve dezavantajları bulunur. Altyapınızı oluştururken hangi RAID tipini seçeceğinize karar vermek için altında yatan teknolojiyi anlamakta fayda var. Bu amaçla en sık kullanılan RAID yapılarını inceleyeceğiz.

## RAID 1

Oldukça sık kullanılan bu RAID yapısı, iki diske ihtiyaç duyar. Bir diske yazılan verinin _aynısı_ diğer diske de yazılır. Böylece disklerden birisi arızalandığında, verinin aynısı diğer diskte de olduğu için kayıp yaşanmaz. Bu işleme _mirroring_ denilir. Bu işlemde yazma açısında bir performans kazancı sağlanılmaz, teorik üst limit tek diskin yazma hızına eşittir. Öte yandan okuma açısında hızlanma sağlanır. RAID kartları bir blok veriyi okuyacağı zaman verinin yarısını bir diskten, diğer yarısını diğer diskten okuyacağı için teorik olarak 2 kat okuma hızlanması sağlanır.

Ayrıca _harcanan_ disklerin yarısının toplam kullanılabilir kapasite olacağını göz önünde bulundurmak gerekir.

RAID 1 yapılarında 2'den fazla disk kullanılabilir \(RAID kartı desteklediği sürece\) ancak kullanılacak disk sayısı çift olmak zorundadır. Genellikle 2'den fazla disk kullanımında RAID 5, RAID 10 gibi yapılar tercih edilir.

Bütün RAID yapılarında olduğu gibi, RAID 1'de de kullanılacak iki diskin aynı boyutta olması \(hatta aynı marka/model olması\) tercih edilir. Eğer farklı boyutta diskler kullanılırsa, küçük boyutu olan diskin boyutu kadar alan kullanılabilir olur.

![](/guvenilirlik/disk/raid_images/raid1-book.png)

Yukarıdaki şekilde, işletim sisteminin 11001010 bitlerini yazma talebini RAID kartının nasıl gerçekleştirdiği görülmektedir. Kart verinin aynısını her iki diske de yazmaktadır \(mirroring\). İşletim sistemi tarafından Disk A ve Disk B tek fiziksel diskmiş gibi görünür. Mirroring işlemi RAID kartının sorumluluğundadır.

## RAID 0

Tıpkı RAID 1 gibi iki diske ihtiyaç duyar, ancak bu sefer mirroring uygulanmaz. Yazılacak bloğun yarısı ilk diske, diğer yarısı da ikinci diske yazılır. Bu işleme _data striping_ denilir. Böylece hem okuma, hem de yazma işlemlerinde yaklaşık 2 kat hızlanma sağlanılır. Ayrıca _harcanan_ disklerin tamamının kapasitesi kullanılmış olur. Ancak disklerden birisinin bozulması durumunda veri kurtarılamaz olur. Bu yüzden verinin güvenilir olarak saklanması gerektiği durumlarda değil, hızlı işlem yapılması istenilen durumlarda kullanılır. Örneğin işletim sisteminizin tmp dizinini RAID 0'lık bir diziden oluşturursanız, bu dizine dosya yazarken ciddi hız kazancı elde edersiniz.

![](/guvenilirlik/disk/raid_images/raid0-small.png)

Yukarıdaki şekilde RAID kartının striping işlemini nasıl yaptığı görülmektedir. İşletim sistemi 11001010 bitlerini yazmak istemektedir, RAID kartı bu veriyi alıp ikiye böler ve yarısını Disk A'ya, diğer yarısını da Disk B'ye yazar. RAID 1 örneğine kıyasla hem disk kapasitesi kullanımının azalmadığı, hem de okuma/yazma işlemlerinin nasıl hızlanacağı bu örnekte görülebilmektedir.

## RAID 10

RAID 1 ve RAID 0'ın birleştirilmiş halidir. En az 4 disk gerektirir. Disklerden kendi içlerinde ikili gruplar halinde RAID 1 dizileri oluştururlar. Ortaya çıkan iki disk de RAID 0 ile birleştirilmiş olur. Böylece 4 diskin toplam kapasitesinin yarısı kadar kullanılabilir alan olur. Hem okuma hem de yazma işlemlerinde hızlanma sağladığı, üstelik güvenilirlik sağladığı için oldukça sık tercih edilen bir RAID yapısıdır. Aynı anda 2 diskin arıza vermesinde bile çalışabilir, ancak arıza veren disklerin farklı RAID 1 dizilerinde olması gerekir.

![](/guvenilirlik/disk/raid_images/raid10-small.png)

Yukarıdaki şekilde RAID 10'un nested \(iç içe geçmiş\) yapısı görülmektedir. İşletim sistemi 11001010 verisini yazmak istediğinde, önce RAID kartı bunu RAID 0 gibi iki parçaya böler \(striping\), elde ettiği her parçayı ise RAID 1 olacak şekilde ayrı disklere yazar. Bu örnekten görülebileceği gibi sistem iki diskin bozulmasına tolerans göstermektedir, ancak diskler aynı RAID 1 dizisinden olmamalıdır. Örneğin aynı anda Disk C ve Disk D bozulursa, RAID yapısı bozulur. Öte yandan aynı anda Disk B ve Disk C bozulursa, sistem çalışmaya devam eder.

## RAID 4

Günümüzde neredeyse hiç kullanılmasa da, RAID 5'i anlayabilmek için RAID 4'ü öğrenmek gerekir. RAID 4, en az 3 disk gerektiren, 1 diskin bozulması durumunda çalışmayı sürdürebilen bir yapıya sahiptir. Okuma işlemlerinde yaklaşık iki kat hızlanma sağlar, yazma işlemlerindeyse hızlanma sağlamaz ve harcanan disklerin 2/3'ü kadar alan kullanılabilir olur.

RAID 4, tıpkı RAID 0'da gördüğümüz gibi _striping_ işlemi yapar, yani yazılacak veriyi parçalara bölerek farklı disklere yazar. Ancak bir diske daha, ilgili bitlerin _parity_'sini yazar. Burada _partiy_ ile kastedilen, bir mantıksal işlemin çıktısıdır.

Bitlerin yazılmaları işlemi sırasında, farklı disklere yazılan bitlerin XOR \(exclusive or\) operasyonuyla elde edilen çıktısını _parity disk_ olarak belirlenen diske yazar. Bu işlemi anlamak için AND, OR gibi mantıksal işlemlere hızlıca göz atıp, XOR'un nasıl bir farklılık sağladığını ve neden RAID 4 için XOR'un tercih edildiğine bakalım.

AND işlemi, yalnızca girdilerinin ikisinin de TRUE olduğu durumlarda TRUE değeri döndürür, diğer durumlarda FALSE döndürür. Örneğin 0 AND 1 = 0 olurken, 1 AND 1 = 1 sonucu elde edilir.

Öte yandan OR işlemi, girdilerinden en az birinin TRUE olduğu durumlarda TRUE değeri döndürür, diğer durumlarda \(iki girdinin de FALSE olması durumunda\) FALSE değeri döndürür.

XOR işlemi ise, girdilerin değerleri birbirinden farklıysa TRUE değeri döndürür. Yani 1 XOR 1 = 0 olurken, 1 XOR 0 = 1 sonucu elde edilir.

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

XOR işleminin matematiksel açıdan birkaç özelliği vardır ve bu yönleriyle AND ve OR'dan ayrılır. Birincisi, işlemin çift yönlü olmasıdır. Bu şu anlama gelir: A XOR B = C ise, her zaman C XOR B = A veya C XOR A = B sonucu elde edilecektir. Bu durum OR ve AND için her zaman geçerli değildir.

Örneğin Truth Table'lardan bakarak görebileceğimiz gibi, 0 XOR 1 = 1, ve yine 1 XOR 1 = 0 olduğu için işlem çift yönlüdür. Öte yandan aynı girdileri OR için kullanacak olsayık, 0 OR 1 = 1, ancak 1 OR 1 = 1 olduğu için farkl osnuç elde edecektik. Kısacası OR mantıksal işlemi XOR gibi çift yönlü değildir. Benzer şekilde AND operatörünün de çift yönlü olmadığı gösterilebilir. 1 AND 0 = 0 iken, 0 AND 0 = 0 sonucu elde edilir.

XOR'un çift yönlü olma özelliği bize şu avantajı sağlar: Diske yazacağımız veriyi iki parçaya bölersek \(_striping_\) ve her iki parçayı XOR'layıp çıktısını \(_parity_\) ayrı bir diske yazarsak, disklerden herhangi birisi zarar gördüğünde diğer bitler yardımıyla kayıp veriyi yeniden oluşturabiliriz.

Örneğin diskimize 11001010 bit'lerinden oluşan 1 Byte'lık bir veriyi yazmak istersek, RAID 4 veriyi aşağıdaki şekilde parçalayacaktır.

![](/guvenilirlik/disk/raid_images/raid4-small.png)

Burada görüldüğü üzere 1 Byte'lık veri iki parçaya bölünmüştür. Buradaki her bir nibble[^1] diğer yarısıyla XOR'lanarak elde edilen _parity bit_'ler üçüncü disk olan Disk C'ye yazdırılmaktadır. Bu yüzden şekilde parity bit'ler yeşil kutucuk ile gösterilmiştir. Aslında işletim sistemi diske yazmak için 0110 bit'lerini asla göndermemiştir, bunu RAID kartı hesaplar ve parity diskine yazar.

Böyle bir senaryoda, veriyi okumak istediğimizde, ilk nibble'ı Disk A'dan, ikinci nibble'ı da Disk B'den okuyacağımız için 2 kat hızlanma sağlanmış olur. Aslında yazarken de tıpkı RAID 0'da olduğu gibi bu disklere 2 kat hızlı yazılmaktadır ancak hem XOR parity'sinin hesaplanması işlemi, hem de bunun sonucunun Disk C'ye yazılması işlemi beklendiği için, bu örnekteki 1 Byte'lık verinin yazılma hızında takıldığımız nokta, Disk C'nin yazma hızı olur. Bu yüzden RAID 4 sistemlerde yazma hızı açısından bir kazanım sağlanılmaz.

Bu örnekteki RAID dizimizde bir felaket senaryosu düşünelim. Disklerden herhangi birisi bozulsa bile, XOR'un çift yönlü işlem özelliği sayesinde kalan iki disk ile kayıp diski yeniden oluşturabiliriz. Örneğin Disk B'nin bozulması durumunda, Disk A XOR Disk C işlemi, yani 1100 XOR 0110 = 1010 olacağından Disk B oluşturulabilir. Gördüğünüz gibi sistem 1 diskin bozulmasına müsaade etmektedir. Burada yapılan işlemin sırası önemli değildir. Yani A XOR C yerine C XOR A yapabilirdik, yine aynı sonucu elde ederdik. Bu da XOR işleminin _değişme özelliği \(commutative property\)_ sayesindedir.

XOR'un bir diğer özelliği ise _birleşme özelliği \(associative property\)_ olarak geçer. Birleşme özelliği kısaca şu anlama gelmektedir. A XOR \( B XOR C\) = \(A XOR B\) XOR C. Bu özellik sayesinde RAID dizimizde üzerinde işlem yapacağımız girdi sayısını 2'den fazla yapabilir demektir. Eğer yukarıdaki örneğimize dördüncü bir disk ekleseydik, 3 disk'e veriyi striping ile parçalayarak yazabilir, üç diski XOR'layarak sonucu parity disk'e yazabilirdik. Üstelik XOR'un değişme özelliği sayesinde işlemlerin sırası bile önemli olmamaktadır.

Bunun için aşağıdaki daha kapsamlı örneğe bakalım.

![](/guvenilirlik/disk/raid_images/raid4-single-small.png)

Burada ilk örnekteki gibi 2 diske veriyi yazıp üçüncüyü parity amacıyla kullanmak yerine, 3 diske veriyi yazıp dördüncüyü parity amacıyla kullanıyoruz. Örneğin daha anlaşılabilir olabilmesi için daha önceki örneklerimizden daha büyü veri yazılıyor burada. 12 bit'lik veri segmentlerinin nasıl parçalandığı renkleriyle görülüyor. Disk A, B ve C veri yazmak için kullanılıyor, Disk D ise parity'nin saklanması için. Burada görebileceğimiz gibi Disk D üzerindeki bir parity'nin hesaplanması için A XOR B XOR C işlemi yapılması gerekiyor. Benzer şekilde, eğer herhangi bir disk bozulursa, kalan disklerdeki veri XOR'lanarak veri kurtarma işlemi yapılabiliyor. Artık veri okuma işini 3 diskten yapabildiğimiz için, bu senaryoda 3 kat okuma hızlanması sağlanıyor. Her ne kadar yazma işleminde orijinal veriyi 3'e böldüğümüz için 3 kat hızlandığımız algısı oluşsa da, bütün yazma işlemleri parity bitin hesaplanması ve Disk D'ye yazılmasını beklemek zorunda olduğundan, yazma hızımız _en fazla_ Disk D'nin yazma hızı kadar olabilir.

## RAID 5

## STRIPE SIZE

[^1]: Bilgisayar bilimlerinde 4bit'ten, yani yarım Byte'tan oluşan birime bir _nibble_ denilir.

