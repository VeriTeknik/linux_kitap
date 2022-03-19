# Verinin Disklere Yayılması

RAID yapılarını incelerken verilerin parçalanması durumunu (_striping_) gördük. Özellikle RAID 0, RAID 5, RAID 10 gibi sıkça kullanılan yapılarda veri parçalanarak disklere yayılmaktadır. Bu durum iki terimin doğmasına neden olur.

Verileri kaç parçaya böldüğümüz, kısacası stripe'ların toplam genişliği _stripe width_ olarak adlandırılır. Eğer elimizde 2 diskten oluşan bir RAID 0 dizisi varsa, stripe width'imiz 2'dir. Genellikle stripe width tanımlanırken parity bit'lerin yazıldığı diskler de sayılır. Bu yüzden örneğin 7 disk'ten oluşan bir RAID 4 dizisinin stripe width'i 7'dir, 6 değildir. Stripe width arttıkça okuma (ve bazen yazma) hızımızın artacağı aşikar.

Stripe size ise başka bir kavramı ifade eder ve sistemin verimliliği açısından doğru seçilmesi önemlidir. Hatırlarsanız RAID 0 bölümündeki örneğimizde 1 Byte'lık verimizi iki tane nibble'a parçalamıştık, böylece her bir diske 4 bit'lik veri yazmıştık. Bu örnekte 4 bit, her bir diske yazılan atomik veri boyutu olduğundan, stripe size'ımız 4 bit olacaktır. Kısacası stripe size, yazma işlemi sırasında RAID kartının veriyi disklere bölerken kullanacağı en küçük parçacık birimine karşılık gelir. Bu yüzden ismi stripe _size_'dır, her bir parçacığın _boyutu_. Bazen stripe size yerine _stripe length, chunk size, block size_ gibi terimler kullanılabilir, tamamı aynı anlamı ifade etmektedir.

Her ne kadar örneklerimizde stripe size'ı 4 bit olarak kullanmış olsak da, pratikte RAID kartları bu kadar küçük değerler kullanmazlar. Değerler genellikle 2kB mertebesinden başlar ve MB mertebesine kadar gider (2kB, 4kB, 8kB, 16kB şeklinde ikilik olarak artar). Stripe size'ın ne kadar büyük veya küçük seçileceği tamamen disklere yazılacak verilerin büyüklüğü, okuma/yazma sıklığı, bir veriye ulaşıldığında komşu verilerin ne kadar sık kullanılacağı, sıralı okuma yapılıp yapılmayacağı gibi bir çok parametreye bağlı olduğundan, RAID kartlarında bu seçenek ayarlanabilir olarak bırakılır. Bu yüzden stripe size, verinin _konumlandırılması_ ve _aktarılması_ işlemlerinin hızını etkiler.

Diskinizden bir veriyi okumak istediğinizde, diskin manyetik kafasının öncelikle ilgili verinin bulunduğu fiziksel bölgeye gitmesi gerekir. Bu zaman alan bir süreçtir ve _verinin konumlandırılması_'ndan kastımız budur. Bir sözlükten bir kelimeyi bulma işlemine benzetilebilir. Önce sözlüğün içinde ilgili harfe, sonra da ilgili kelimeye ulaşmak gerekir.

Disk üzerinde veriyi bulduktan sonra, veriyi okumaya başlarız. _Verinin aktarılması_'ndan kastımız da budur. Artık veri konumlandırılmıştır, şimdi verinin işletim sistemine transfer edilmesi gerekmektedir. Sözlük analojimizde bu, ilgili kelimeyi bulduktan sonra artık okumaya başlamaya karşılık gelir.

Yapılan işleme göre disk üzerinde bazen rastgele bölgeler okumamız gerekebilir. Bazen bir dosyayı bütün olarak okumamız gerekebilir ancak okuma sırası önemli değildir, bazense dosyayı sırayla (baştan sona) okumamız gerekebilir. Sözlük analojimizde karşılıklarını düşünecek olursak, sırasıyla _bahar_, _zeytin_ ve _bahçe_ sözcüğünün anlamlarına bakmak istediğimizde, sözlüğün başı ve sonu arasında adresleme işlemleri yapmamız gerekir. Bu yüzden 3 kere adresleme işlemi yaparız. Eğer bu sözcüklerin anlamlarını öğrenme sıramız önemli değilse, o zaman üç farklı sözlük kullanabiliriz ve üç farklı kişi sözlüklere bakabilir. Böylece her birini konumlandırırken de, anlamlarını okurken de hızlanma sağlarız. Eğer elimizde 3 okuyucu yoksa, hiç değilse _bahar_ ve _bahçe_ sözcüklerini birbirlerine yakın oldukları için ilk başta okuyabilir, arından _zeytin_ sözcüğüne geçebiliriz. Ancak bütün bunlar tamamen verilerin nasıl okunması istendiğine bağlıdır.

Bu örneklerde verinin büyüklüğü de önemlidir. Veriyi bir kez konumlandırdıktan sonra, veriyi okumak da zaman alacaktır ve veriyi konumlandırma süresi ile okuma süresi arasındaki dengeyi de gözetmek gerekir. Eğer sözlüğümüzdeki kelimelerin açıklaması çok uzunsa, örneğin 20 sayfasa, _bahar-zeytin-bahçe_ sıralamasından _bahar-bahçe-zeytin_ sıralamasına geçmek neredeyse hiç avantaj sağlamayabilir bile, çünkü bir kelimeyi okumak, kelimeyi sözlükte bulmaktan hatırı sayılır miktarda uzun olacaktır.

Stripe size küçüldükte, yazılan dosyalar daha küçük parçalara bölündüğü için ortalama bir dosyanın çok diske yazılması olasılığı artar. Dosyalar çok fazla diske yazılmış olacağı için diskler üzerinde _konumlandırılması_ zorlaşır. Ancak bir kez konumlandırıldı mı, birden fazla disk tarafından okunacağı için okuma işlemi hızlanır.

Stripe size'ın büyütülmesi ise tam tersi etkiyi yaratır. Yazılacak dosya boyutlarına göre bir dosyanın az sayıda diske yayılması (hatta tek diske yayılması) olasılığı artar. Bu durumda ilgili dosyaya erişmek daha kolay olacaktır çünkü tek diskteki tek sektöre ulaşmak çok daha hızlı olur, ancak bu dosyayı paralel olarak okuyamayız, bu durumda okuma/yazma işlemlerimiz tek diskin performansıyla sınırlandırılmış olur.

Aşağıdaki örnek, aynı disklerle oluşturulmuş iki tane RAID 0 dizisini göstermektedir. Her dizinin 4 elemanı&#x20;

(

diski

)

&#x20;vardır. İkisine de aynı dosyalar yazılır. Dosyalar farklı renklerle gösterilmiştir. Yani kırmızı dosya, pembe, dosya, mavi dosya ve yeşil dosya olmak üzere 4 tane dosya var. Soldaki konfigürasyonda stripe size 4kB seçilmiştir, sağdaki konfigürasyonda ise aynı dosyalar aynı disklere 64kB stripe size ile yazılmıştır.

![](<../.gitbook/assets/stripe size.png>)

Şekildeki bloklar stripe size'ı işaret etmektedir. Bu örnekte kırmızı dosya 4kB boyuta, pembe dosya 44 kB, mavi dosya 120 kB ve yeşil dosya 212 kB boyuta sahiptir. Örnekten görülebileceği gibi her iki konfigürasyonda da kırmızı dosya tek diske yazılır, çünkü stripe size 4kB'dan küçük olmadığı sürece, 4kB'lık dosyayı parçalayamayız. İlginç olan, pembe dosyanın durumudur. 4kB stripe size kullanıldığında pembe dosya 4 diske yayılırken, 64kB stripe size kullanıldığında bütün dosya tek diske yazılmıştır. Pembe dosyayı soldaki RAID dizisinden okumak istersek, 4 kat hızlı okuyacağız demektir, ancak dosyanın konumunun hesaplanması için 4 farklı diskte adresleme işlemi yapılması gerekmektedir. Kısacası dosyaya&#x20;

_ilk erişim_

&#x20;daha yavaş olacaktır, ancak bir kez eriştik mi hızla okuyacağız demektir. Öte yandan sağdaki RAID dizisinde pembe dosyayı hemen buluruz, ancak okuma hızımız tek diskin okuma hızından öteye gidemez.

Farkındaysanız yeşil dosya her iki senaryoda da 4 diske yayılmış durumda. Bunun birkaç sebebi olabilir, birincisi seçilen stripe size yeterince büyük değildir, ikincisi ise dosyanın beraber barındırıldığı diğer dosyalardan farklı bir yapıya sahip olması olabilir. Örneğin bir sürü küçük dosyamız varsa ancak bir tane devasa dosyamız varsa bile, RAID dizisi üzerindeki konumlaması diğerlerinden farklı olacaktır. Bu bile performansı etkileyen etmenleden birisidir. Bu yüzden genellikle benzer yapıdaki dosyaların benzer disk yapılarına yazılması tercih edilebilir, ancak problemi her zaman bu kadar sınırlandırarak uygulamak pratikte mümkün olmaz.

Bir diğer nokta, dosyaların büyüklüğünden ziyade erişim biçimidir. Rastgele okuma işlemleri ile sıralı okuma işlemleri hem disk üzerinde ciddi farklılıklar yaratır, hem de diske verilerin yazılma biçimlerini buna göre belirlemek faydalı olur. Bütün diskler sıralı okuma işlemini rastgele okuma işleminden daha hızlı yaparlar, ancak örneğin SSD'lerin rastgele okuma ile sıralı okuma işlemleri arasındaki hız farkı çok daha azdır. Bu durumda rastgele okuma işlemini çok yapacak birisi (veritabanı kullananlar, oyun oynayanlar vs.) manyetik diskler yerine SSD kullanmayı tercih edebilir. Öte yandan sıralı okuma işlemi örneğin bazı sıkıştırılmış dosyaların okunmasında kullanılır, veya güvenlik kameralarının verilerinin depolanması gibi konularda kullanılır. Hemen hemen hiçbir durumda bir video stream edilirken rastgele parçalarını yükleme ihtiyacı duymayız, videoyu baştan sona doğru _lineer_ olarak yüklemeyi tercih ederiz. Böyle bir durumda SSD yerine manyetik disk kullanımı tercih edilebilir olur (fiyat/performans açısından).

Video ve veritabanı erişimi örneklerindeki rastgele erişim ve sıralı erişim problemleri RAID Stripe Size ile de ilişkilidir. Yukarıdaki örnekte eğer pembe dosya sıralı okunması istenilen bir dosyaysa, tek diskte yer alması _dosyanın nerede bulunduğunu_ hızla bulmamızı sağlayacağı için erişim süresini çok kısaltır, zaten sıralı okuma yapılacağı için de disklerden paralel olarak verinin alınması bir anlam sağlamaz dolayısıyla büyük stripe size ile saklanması akıllıca olacaktır. Ancak eğer bu bir video dosyası değil de bir veritabanı dosyasının parçasıysa, birden fazla işlem bu dosyanın farklı noktalarına erişmek isteyecektir dolayısıyla bu kadar büyük bir dosyanın farklı disklere yayılması tercih edilebilir. Öte yandan bunun için bir genel geçer kural olduğunu söylemek yanlış olacaktır. Eğer sıralı okuma ihtiyacınız yoksa ve büyük dosyalar barındıracaksanız (örneğin büyük backup dosyaları) dosyaların transfer hızı, konumlandırma hızından önemli olacağı için küçük stripe size kullanarak çok diske yayılmalarını sağlamak verimli olabilir.