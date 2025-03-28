# MongoDB Temel Kullanım

Bu bölümde MongoDB'ye nasıl bağlanılacağı ve temel veritabanı/koleksiyon işlemlerinin (veri ekleme, sorgulama, güncelleme, silme vb.) nasıl yapılacağı anlatılacaktır.

## Bağlantı Kurma (`mongosh`)

MongoDB ile etkileşim kurmak için modern komut satırı aracı `mongosh`'tur (eski araç `mongo` idi).

*   **Yerel Sunucuya Bağlanma:**
    Eğer MongoDB sunucusu yerelde çalışıyorsa ve kimlik doğrulama kapalıysa, doğrudan `mongosh` komutu ile bağlanabilirsiniz:
    ```bash
    mongosh
    ```
    Bu komut varsayılan olarak `mongodb://127.0.0.1:27017` adresine bağlanır ve `test` veritabanını kullanır.

*   **Belirli Bir Sunucuya ve Porta Bağlanma:**
    Farklı bir sunucuya veya porta bağlanmak için bağlantı dizesi (connection string) kullanılır:
    ```bash
    mongosh "mongodb://sunucu_adresi:port"
    ```

*   **Kimlik Doğrulama ile Bağlanma:**
    Eğer kimlik doğrulama (`security.authorization: enabled`) aktifse, kullanıcı adı, parola ve kimlik doğrulama veritabanını belirtmeniz gerekir:
    ```bash
    mongosh "mongodb://kullanici_adi:parola@sunucu_adresi:port/?authSource=admin"
    ```
    *Not: `authSource` genellikle kullanıcıların tanımlandığı veritabanıdır (çoğunlukla `admin`).*

## Temel `mongosh` Komutları

`mongosh` kabuğu içindeyken JavaScript benzeri bir sözdizimi ve özel komutlar kullanılır.

*   **Veritabanlarını Listeleme:**
    ```javascript
    show dbs
    ```

*   **Mevcut Veritabanını Gösterme:**
    ```javascript
    db
    ```

*   **Veritabanı Değiştirme/Oluşturma:**
    Olmayan bir veritabanı adı kullanıldığında, içine ilk veri eklendiği anda otomatik olarak oluşturulur.
    ```javascript
    use yeni_veritabani
    ```

*   **Koleksiyonları (Collections) Listeleme:**
    ```javascript
    show collections
    ```

*   **Yardım:**
    ```javascript
    help
    ```

*   **Çıkış:**
    ```javascript
    exit
    // veya
    quit()
    ```

## Temel Veri İşlemleri (CRUD)

MongoDB'de veriler BSON (Binary JSON) formatında dökümanlar olarak saklanır ve bu dökümanlar koleksiyonlar içinde gruplanır.

*   **Veri Ekleme (Create):**
    `insertOne()` (tek döküman) veya `insertMany()` (çoklu döküman) kullanılır.
    ```javascript
    // 'urunler' adında bir koleksiyona tek bir döküman ekleme
    db.urunler.insertOne({ adi: "Laptop", fiyat: 1500, kategori: "Elektronik" })

    // Birden fazla döküman ekleme
    db.urunler.insertMany([
      { adi: "Klavye", fiyat: 75, kategori: "Aksesuar" },
      { adi: "Monitor", fiyat: 300, kategori: "Elektronik" }
    ])
    ```
    Eğer `urunler` koleksiyonu yoksa, ilk ekleme işleminde otomatik olarak oluşturulur.

*   **Veri Okuma (Read):**
    `find()` (tüm eşleşenleri bulur) veya `findOne()` (ilk eşleşeni bulur) kullanılır. Filtreleme için sorgu dökümanları kullanılır.
    ```javascript
    // 'urunler' koleksiyonundaki tüm dökümanları bulma
    db.urunler.find()

    // Kategori'si "Elektronik" olan ürünleri bulma
    db.urunler.find({ kategori: "Elektronik" })

    // Fiyatı 100'den büyük olan ürünleri bulma ($gt: greater than)
    db.urunler.find({ fiyat: { $gt: 100 } })

    // Sadece ilk eşleşen elektronik ürünü bulma
    db.urunler.findOne({ kategori: "Elektronik" })
    ```
    `find()` sonucu bir imleç (cursor) döndürür. Tüm sonuçları görmek için genellikle `.pretty()` metodu eklenir: `db.urunler.find().pretty()`

*   **Veri Güncelleme (Update):**
    `updateOne()` (ilk eşleşeni günceller) veya `updateMany()` (tüm eşleşenleri günceller) kullanılır. İlk argüman filtre, ikinci argüman güncelleme operatörlerini içeren dökümandır (`$set`, `$inc` vb.).
    ```javascript
    // 'Laptop' ürününün fiyatını güncelleme
    db.urunler.updateOne(
      { adi: "Laptop" },
      { $set: { fiyat: 1450, stok: 10 } } // fiyat'ı değiştir, stok alanını ekle
    )

    // Tüm Aksesuar kategorisindeki ürünlerin fiyatını %10 artırma ($mul: multiply)
    db.urunler.updateMany(
      { kategori: "Aksesuar" },
      { $mul: { fiyat: 1.10 } }
    )
    ```

*   **Veri Silme (Delete):**
    `deleteOne()` (ilk eşleşeni siler) veya `deleteMany()` (tüm eşleşenleri siler) kullanılır. Argüman olarak filtre dökümanı alır.
    ```javascript
    // 'Klavye' ürününü silme
    db.urunler.deleteOne({ adi: "Klavye" })

    // Fiyatı 300'den düşük olan tüm ürünleri silme ($lt: less than)
    db.urunler.deleteMany({ fiyat: { $lt: 300 } })
    ```
