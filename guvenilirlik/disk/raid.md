# RAID

Günümüz bilişim altyapıları, özellikle verinin en düşük seviyede dahi güvenilir bir biçimde yazılabilir ve okunabilir olmasına ihtiyaç duymaktadır. Herhangi bir sunucu üzerinde çalışılırken sabit disklerden birinin bozulması hem sistemin çalışmasını durdurabilir, hem de verilerde kurtarılamaz kayıplara sebep olabilir. Bu tip durumların önüne geçme için birden fazla sabit diskin bir arada kullanılarak daha güvenilir diziler oluşturması işlemine RAID \(Redundant Array of Independent Disks\) denilir.

RAID yapıları sayesinde sunucular üzerindeki birden fazla disk, işletim sistemine sanki tek bir diskmiş gibi gösterilebilir, böylece hem güvenilirlik hem de çeşitli açılardan performans kazanımları sağlanılabilir. Bir sunucu üzerinde RAID yapısı kurabilmek için RAID kontrol kartına ihtiyaç duyulur. Çoğu sunucuda bu kart dahil olacağı gibi, bazı sistemlere sonradan takılabilir. Ayrıca işletim sistemleri, RAID kontrol kartı bulunmayan sistemler için yazılımsal RAID desteği de sağlamaktadır. Ancak yazılımsal RAID'in bazı matematiksel işlemleri daha yavaş yapacağı, ayrıca disk bozulmaları durumunda sistemi kapatmadan tamir etme işleminin yapılamayacağını göz önünde bulundurmakta fayda var.

RAID ile ilgili unutulmaması gereken noktalardan birisi, bir _yedekleme mekanizması_ olmadığı. RAID yapılan işlemlerin güvenilir ve/veya verimli bir biçimde yatayda yayılmasını sağlamaktadır. Ancak bu yayılma _hatalar_ ve _istenmeyen_ durumları da kapsamaktadır. Bir dosyayı sildiğinizde RAID dizinindeki bütün disklerden ilgili elemanları silinmiş olur ve bunu geri döndüremezsiniz. Bu yüzden sisteminizin RAID ile çalışacağını kurguladığınız zaman, yedekleme ihtiyacınızın ortadan kalktığını düşünmeyin.

Birden fazla RAID çeşidi bulunmaktadır ve hepsinin kendince avantajları ve dezavantajları bulunur. Altyapınızı oluştururken hangi RAID tipini seçeceğinize karar vermek için altında yatan teknolojiyi anlamakta fayda var. Bu amaçla en sık kullanılan RAID yapılarını inceleyeceğiz.

## RAID 1

Oldukça sık kullanılan bu RAID yapısı, iki diske ihtiyaç duyar. Bir diske yazılan verinin _aynısı_ diğer diske de yazılır. Böylece disklerden birisi arızalandığında, verinin aynısı diğer diskte de olduğu için kayıp yaşanmaz. Bu işleme _mirroring_ denilir. Bu işlemde yazma açısında bir performans kazancı sağlanılmaz, teorik üst limit tek diskin yazma hızına eşittir. Öte yandan okuma açısında hızlanma sağlanır. RAID kartları bir blok veriyi okuyacağı zaman verinin yarısını bir diskten, diğer yarısını diğer diskten okuyacağı için teorik olarak 2 kat okuma hızlanması sağlanır.

Ayrıca _harcanan_ disklerin yarısının toplam kullanılabilir kapasite olacağını göz önünde bulundurmak gerekir.

RAID 1 yapılarında 2'den fazla disk kullanılabilir \(RAID kartı desteklediği sürece\) ancak kullanılacak disk sayısı çift olmak zorundadır. Genellikle 2'den fazla disk kullanımında RAID 5, RAID 10 gibi yapılar tercih edilir.

Bütün RAID yapılarında olduğu gibi, RAID 1'de de kullanılacak iki diskin aynı boyutta olması \(hatta aynı marka/model olması\) tercih edilir. Eğer farklı boyutta diskler kullanılırsa, küçük boyutu olan diskin boyutu kadar alan kullanılabilir olur.

## RAID 0

Tıpkı RAID 1 gibi iki diske ihtiyaç duyar, ancak bu sefer mirroring uygulanmaz. Yazılacak bloğun yarısı ilk diske, diğer yarısı da ikinci diske yazılır. Bu işleme _data striping_ denilir. Böylece hem okuma, hem de yazma işlemlerinde yaklaşık 2 kat hızlanma sağlanılır. Ayrıca _harcanan_ disklerin tamamının kapasitesi kullanılmış olur. Ancak disklerden birisinin bozulması durumunda veri kurtarılamaz olur. Bu yüzden verinin güvenilir olarak saklanması gerektiği durumlarda değil, hızlı işlem yapılması istenilen durumlarda kullanılır. Örneğin işletim sisteminizin tmp dizinini RAID 0'lık bir diziden oluşturursanız, bu dizine dosya yazarken ciddi hız kazancı elde edersiniz.

## RAID 10

RAID 1 ve RAID 0'ın birleştirilmiş halidir. En az 4 disk gerektirir. Disklerden kendi içlerinde ikili gruplar halinde RAID 1 dizileri oluştururlar. Ortaya çıkan iki disk de RAID 0 ile birleştirilmiş olur. Böylece 4 diskin toplam kapasitesinin yarısı kadar kullanılabilir alan olur. Hem okuma hem de yazma işlemlerinde hızlanma sağladığı, üstelik güvenilirlik sağladığı için oldukça sık tercih edilen bir RAID yapısıdır. Aynı anda 2 diskin arıza vermesinde bile çalışabilir, ancak arıza veren disklerin farklı RAID 1 dizilerinde olması gerekir.

## RAID 4

Günümüzde neredeyse hiç kullanılmasa da, RAID 5'i anlayabilmek için RAID 4'ü öğrenmek gerekir. RAID 4, en az 3 disk gerektiren, 1 diskin bozulması durumunda çalışmayı sürdürebilen bir yapıya sahiptir. Okuma işlemlerinde yaklaşık iki kat hızlanma sağlar, yazma işlemlerindeyse hızlanma sağlamaz ve harcanan disklerin 2/3'ü kadar alan kullanılabilir olur.

RAID 4, tıpkı RAID 0'da gördüğümüz gibi _striping_ işlemi yapar, yani yazılacak veriyi parçalara bölerek farklı disklere yazar. Ancak bir diske daha, ilgili bitlerin _parity_'sini yazar. Burada _partiy_ ile kastedilen, bir mantıksal işlemin çıktısıdır.

Bitlerin yazılmaları işlemi sırasında, farklı disklere yazılan bitlerin XOR \(exclusive or\) operasyonuyla elde edilen çıktısını _parity disk_ olarak belirlenen diske yazar. Bu işlemi anlamak için AND, OR gibi mantıksal işlemlere hızlıca göz atıp, XOR'un nasıl bir farklılık sağladığını ve neden RAID 4 için XOR'un tercih edildiğine bakalım.





