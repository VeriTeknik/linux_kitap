# RAID

Günümüz bilişim altyapıları, özellikle verinin en düşük seviyede dahi güvenilir bir biçimde yazılabilir ve okunabilir olmasına ihtiyaç duymaktadır. Herhangi bir sunucu üzerinde çalışılırken sabit disklerden birinin bozulması hem sistemin çalışmasını durdurabilir, hem de verilerde kurtarılamaz kayıplara sebep olabilir. Bu tip durumların önüne geçme için birden fazla sabit diskin bir arada kullanılarak daha güvenilir diziler oluşturması işlemine RAID \(Redundant Array of Independent Disks\) denilir.

RAID yapıları sayesinde sunucular üzerindeki birden fazla disk, işletim sistemine sanki tek bir diskmiş gibi gösterilebilir, böylece hem güvenilirlik hem de çeşitli açılardan performans kazanımları sağlanılabilir. Bir sunucu üzerinde RAID yapısı kurabilmek için RAID kontrol kartına ihtiyaç duyulur. Çoğu sunucuda bu kart dahil olacağı gibi, bazı sistemlere sonradan takılabilir. Ayrıca işletim sistemleri, RAID kontrol kartı bulunmayan sistemler için yazılımsal RAID desteği de sağlamaktadır. Ancak yazılımsal RAID'in bazı matematiksel işlemleri daha yavaş yapacağı, ayrıca disk bozulmaları durumunda sistemi kapatmadan tamir etme işleminin yapılamayacağını göz önünde bulundurmakta fayda var.

Birden fazla RAID çeşidi bulunmaktadır ve hepsinin kendince avantajları ve dezavantajları bulunur. Altyapınızı oluştururken hangi RAID tipini seçeceğinize karar vermek için altında yatan teknolojiyi anlamakta fayda var. Bu amaçla en sık kullanılan RAID yapılarını inceleyeceğiz.

## RAID 1

Oldukça sık kullanılan bu RAID yapısı, iki diske ihtiyaç duyar. Bir diske yazılan verinin _aynısı_ diğer diske de yazılır. Böylece disklerden birisi arızalandığında, verinin aynısı diğer diskte de olduğu için kayıp yaşanmaz. Bu işleme _mirroring_ denilir.

