# Redis

Redis (Remote Dictionary Server), açık kaynaklı, bellek-içi (in-memory) bir veri yapısı deposudur. Genellikle anahtar-değer (key-value) deposu olarak kullanılır, ancak string'lerin yanı sıra listeler, hash'ler, set'ler ve sorted set'ler gibi çeşitli veri yapılarını da destekler. Yüksek performansı (düşük gecikme süresi) ve atomik operasyonları ile bilinir.

## Yaygın Kullanım Alanları

Redis'in hızı ve basitliği, onu özellikle performansın kritik olduğu durumlar için ideal kılar:

*   **Önbellekleme (Caching):** En yaygın kullanım alanıdır. Veritabanı sorgu sonuçlarını, API yanıtlarını veya sık erişilen diğer verileri Redis'te önbelleğe alarak uygulama performansını önemli ölçüde artırır.
*   **Oturum Yönetimi (Session Management):** Web uygulamalarında kullanıcı oturum bilgilerini hızlı bir şekilde saklamak ve erişmek için kullanılır.
*   **Gerçek Zamanlı Liderlik Tabloları (Leaderboards):** Sorted Set veri yapısı, skorları sıralı bir şekilde tutmak ve hızlıca güncellemek/sorgulamak için çok uygundur.
*   **Hız Sınırlama (Rate Limiting):** Belirli bir süre içinde bir kullanıcının veya IP adresinin yapabileceği istek sayısını sınırlamak için sayaç olarak kullanılabilir.
*   **Mesaj Kuyrukları (Message Queues):** Basit mesajlaşma ve görev kuyrukları oluşturmak için listeler (LPUSH/BRPOP) kullanılabilir (ancak daha karmaşık senaryolar için RabbitMQ veya Kafka gibi özel mesaj kuyruğu sistemleri daha uygun olabilir).
*   **Pub/Sub (Publish/Subscribe):** Gerçek zamanlı mesajlaşma ve olay bildirim sistemleri oluşturmak için dahili Pub/Sub mekanizması sunar.
*   **Gerçek Zamanlı Sayaçlar:** Web sitesi ziyaretçi sayısı, beğeni sayısı gibi sık güncellenen sayaçları atomik olarak artırmak için kullanılabilir (`INCR` komutu).

**Kalıcılık (Persistence):** Redis bellek-içi çalışsa da, verilerin diske kaydedilmesi için RDB (snapshotting) ve AOF (Append-Only File) olmak üzere iki farklı kalıcılık mekanizması sunar. Bu sayede sunucu yeniden başlasa bile veriler kaybolmaz.

Bu bölümde anahtar-değer (key-value) deposu olan Redis'in kurulumu, temel yapılandırması ve kullanımı ele alınacaktır.
