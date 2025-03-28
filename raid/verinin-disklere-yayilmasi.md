# Verinin Disklere Yayılması: Striping, Stripe Width ve Stripe Size

RAID 0, RAID 5, RAID 6, RAID 10 gibi birçok RAID seviyesi, performansı artırmak için **şeritleme (striping)** tekniğini kullanır. Striping, veriyi daha küçük bloklara (şeritlere) bölerek bu blokları RAID dizisindeki farklı disklere eş zamanlı olarak yazma işlemidir. Bu, tek bir diskin okuma/yazma hız limitini aşmayı sağlar.

Bu işlemle ilgili iki önemli kavram vardır:

1.  **Stripe Width (Şerit Genişliği):** Tek bir şeritleme operasyonuna katılan **disk sayısıdır**. Örneğin, 4 diskli bir RAID 0 veya RAID 5 dizisinde stripe width genellikle 4'tür. RAID 10 (1+0) gibi iç içe geçmiş seviyelerde, striping'in uygulandığı ayna (mirror) gruplarının sayısıdır (örn. 4 diskli RAID 10'da 2 mirror grubu varsa stripe width 2'dir). Stripe width arttıkça, teorik paralel okuma/yazma kapasitesi de artar.
2.  **Stripe Size (Şerit Boyutu / Chunk Size):** Tek bir diske tek bir şeritleme operasyonunda yazılan veri bloğunun **boyutudur**. Bu değer genellikle RAID kontrolcüsü veya yazılımsal RAID (`mdadm`) oluşturulurken ayarlanabilir (örn. 4KB, 16KB, 64KB, 128KB, 256KB...). Bazen _chunk size_, _block size_ veya _stripe length_ olarak da adlandırılır. **Stripe size**, stripe width ile karıştırılmamalıdır. Tam bir şerit (full stripe), `stripe width * stripe size` kadar veri içerir.

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

*(Burada, farklı stripe size'ların (örn. 4KB vs 64KB) 4 diskli bir RAID 0 dizisinde farklı boyutlardaki dosyaları (kırmızı, pembe, mavi, yeşil) nasıl dağıttığını gösteren bir diagram hayal edilebilir. Küçük stripe size (4KB) ile pembe dosyanın (44KB) 11 parçaya bölünüp 4 diske yayıldığı, büyük stripe size (64KB) ile ise pembe dosyanın tek bir diske sığdığı görülebilir. Daha büyük olan mavi ve yeşil dosyalar her iki durumda da birden fazla diske yayılır.)*

Bu dağılımın performansa etkisi şöyledir:
*   **Küçük Stripe Size:**
    *   **Avantaj:** Büyük dosyalar veya birden fazla küçük dosyaya eş zamanlı erişim durumunda, veri daha fazla diske yayıldığı için **paralel okuma/yazma artar**, toplam transfer hızı yükselir.
    *   **Dezavantaj:** Tek bir dosyaya erişmek için daha fazla diskin kafa hareket ettirmesi (seek) gerekebilir, bu da **erişim süresini (latency) artırabilir**. Özellikle tek, küçük bir dosyaya erişirken verimsiz olabilir.
*   **Büyük Stripe Size:**
    *   **Avantaj:** Küçük dosyalar genellikle tek bir diske sığar, bu da o dosyaya erişirken **kafa hareketini azaltır ve erişim süresini kısaltır**.
    *   **Dezavantaj:** Büyük dosyalar daha az diske yayılabilir veya tek bir dosyaya erişim sırasında daha az paralellik sağlanır, bu da **maksimum transfer hızını düşürebilir**.

**Doğru Stripe Size Nasıl Seçilir?**

Optimum stripe size, sistemin iş yüküne (workload) bağlıdır:
*   **Büyük Dosyalar ve Sıralı Erişim (örn. Video Streaming, Yedekleme):** Genellikle daha **büyük** stripe size (örn. 128KB, 256KB veya daha fazla) tercih edilir. Bu, tek bir dosyanın daha az parçaya bölünmesini sağlar ve sıralı okuma/yazma performansını artırabilir.
*   **Küçük Dosyalar ve Rastgele Erişim (örn. Veritabanları, Sanallaştırma, Çok Kullanıcılı Sistemler):** Genellikle daha **küçük** stripe size (örn. 16KB, 32KB, 64KB) tercih edilir. Bu, rastgele I/O işlemlerinin daha fazla diske dağıtılmasını sağlayarak genel performansı ve yanıt süresini iyileştirebilir.

Genel bir başlangıç noktası olarak **64KB** veya **128KB** stripe size sıkça kullanılır, ancak en iyi değeri bulmak için spesifik iş yükü altında testler yapmak gerekebilir. RAID kontrolcüsünün veya yazılımının varsayılan değeri genellikle makul bir başlangıçtır.

**Not:** ZFS gibi modern dosya sistemleri, genellikle sabit bir stripe size yerine dinamik veya değişken blok boyutları kullanarak bu ayarlamaları otomatik olarak optimize etmeye çalışır.

Bir diğer nokta, dosyaların büyüklüğünden ziyade erişim biçimidir. Rastgele okuma işlemleri ile sıralı okuma işlemleri hem disk üzerinde ciddi farklılıklar yaratır, hem de diske verilerin yazılma biçimlerini buna göre belirlemek faydalı olur. Bütün diskler sıralı okuma işlemini rastgele okuma işleminden daha hızlı yaparlar, ancak örneğin SSD'lerin rastgele okuma ile sıralı okuma işlemleri arasındaki hız farkı çok daha azdır. Bu durumda rastgele okuma işlemini çok yapacak birisi (veritabanı kullananlar, oyun oynayanlar vs.) manyetik diskler yerine SSD kullanmayı tercih edebilir. Öte yandan sıralı okuma işlemi örneğin bazı sıkıştırılmış dosyaların okunmasında kullanılır, veya güvenlik kameralarının verilerinin depolanması gibi konularda kullanılır. Hemen hemen hiçbir durumda bir video stream edilirken rastgele parçalarını yükleme ihtiyacı duymayız, videoyu baştan sona doğru _lineer_ olarak yüklemeyi tercih ederiz. Böyle bir durumda SSD yerine manyetik disk kullanımı tercih edilebilir olur (fiyat/performans açısından).

Video ve veritabanı erişimi örneklerindeki rastgele erişim ve sıralı erişim problemleri RAID Stripe Size ile de ilişkilidir. Yukarıdaki örnekte eğer pembe dosya sıralı okunması istenilen bir dosyaysa, tek diskte yer alması _dosyanın nerede bulunduğunu_ hızla bulmamızı sağlayacağı için erişim süresini çok kısaltır, zaten sıralı okuma yapılacağı için de disklerden paralel olarak verinin alınması bir anlam sağlamaz dolayısıyla büyük stripe size ile saklanması akıllıca olacaktır. Ancak eğer bu bir video dosyası değil de bir veritabanı dosyasının parçasıysa, birden fazla işlem bu dosyanın farklı noktalarına erişmek isteyecektir dolayısıyla bu kadar büyük bir dosyanın farklı disklere yayılması tercih edilebilir. Öte yandan bunun için bir genel geçer kural olduğunu söylemek yanlış olacaktır. Eğer sıralı okuma ihtiyacınız yoksa ve büyük dosyalar barındıracaksanız (örneğin büyük backup dosyaları) dosyaların transfer hızı, konumlandırma hızından önemli olacağı için küçük stripe size kullanarak çok diske yayılmalarını sağlamak verimli olabilir.
