# MongoDB

MongoDB, açık kaynaklı, döküman odaklı (document-oriented) bir NoSQL veritabanıdır. Verileri JSON benzeri BSON (Binary JSON) formatında dökümanlar olarak saklar. Esnek şema yapısı, yüksek performans, ölçeklenebilirlik (özellikle yatay ölçeklenebilirlik - sharding) ve geliştirici dostu olmasıyla popülerdir.

## Yaygın Kullanım Alanları

MongoDB'nin esnek yapısı ve ölçeklenebilirliği, onu belirli türdeki uygulamalar için çok uygun kılar:

*   **İçerik Yönetim Sistemleri (CMS) ve Bloglar:** Esnek şema, farklı türdeki içeriklerin (makaleler, kullanıcı profilleri, yorumlar vb.) kolayca yönetilmesini sağlar.
*   **E-ticaret Uygulamaları:** Ürün katalogları, kullanıcı sepetleri, siparişler gibi değişken ve ilişkisel olmayan verilerin yönetimi için uygundur.
*   **Mobil Uygulamalar:** Hızlı geliştirme döngüleri ve değişen veri yapılarına kolay adaptasyon sağlar.
*   **Nesnelerin İnterneti (IoT):** Cihazlardan gelen büyük miktarda yapısal olmayan veya yarı yapısal verinin depolanması ve işlenmesi için ölçeklenebilir bir çözümdür.
*   **Gerçek Zamanlı Analitik:** Büyük veri setleri üzerinde hızlı sorgulama ve analiz yapma yeteneği sunar. Aggregation Pipeline gibi araçlarla karmaşık veri işleme işlemleri yapılabilir.
*   **Kişiselleştirme ve Kullanıcı Profilleri:** Kullanıcı davranışlarını ve tercihlerini depolamak ve bunlara dayalı kişiselleştirilmiş deneyimler sunmak için esnek bir yapı sunar.
*   **Kataloglama ve Envanter Yönetimi:** Değişken özelliklere sahip ürün veya varlıkların yönetimi için idealdir.

**Ne Zaman İlişkisel Veritabanı Tercih Edilebilir?**
MongoDB esnek olsa da, veriler arasında karmaşık ilişkilerin (çoktan çoğa ilişkiler gibi) yoğun olduğu, sıkı veri bütünlüğü ve ACID garantisinin kritik olduğu (örneğin, karmaşık finansal işlemler) durumlarda PostgreSQL gibi ilişkisel veritabanları daha uygun olabilir.

Bu bölümde NoSQL veritabanı olan MongoDB'nin kurulumu, temel yapılandırması ve kullanımı ele alınacaktır.
