# Temel SQL Sorgularına Giriş

Burada kullanabilmemiz için sahte veri ürettim. Bu verileri internet üzerindeki herhangi bir siteden üretebilirsiniz. Bunları veritabanınıza almak için farklı yollar var.

Örneğin;

`mysql -u root -p`

Giriş yaptıktan sonra,

```
CREATE DATABASE dummy;
USE dummy;
```

Ardından da

```
SOURCE /veritabanının_yolu/dosya.sql;
```

Şeklinde "\*.sql" tablosunu içeri alabiliyoruz. Veritabanın yolunu yazmak için absolute path kullanmanıza gerek yok. Fakat bulunduğunuz dizinden farklı bir üst dizindeyse, absolute path yazmak daha kolay olabilir.

Bunun haricinde;

Varolan bir veritabanına veriyi almak için de şöyle bir yol izleyebilirsiniz;

`mysql -u kullanıcıadı -p veritabanı < dosya.sql`

Bazı \*.sql dosyalarında bu satırı veritabanı belirtememize gerek olmayabilir fakat biz şimdilik bunu gözardı ediyoruz.



