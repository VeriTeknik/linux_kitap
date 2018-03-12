# CentOS'a MySQL Kurulumu

CentOS repolarında MySQL bulunmuyor. Bu yüzden RPM paketini MySQL'den indirmemiz gerek. 

`https://dev.mysql.com/downloads/repo/yum`

Bu adrese giriş yaparak, en güncel versiyonun ne olduğunu kontrol edelim. 

CentOS 7'ye kurulum yapacağımızdan RedHat 7 için olanı kullanabiliriz. Kurulum yapmamız gereken paketin altında son paketin adı açık olarak gözüküyor, `mysql-57-community-release-el7-11.noarch.rpm` bunu kopyalayarak `https://dev.mysql.com/get/`  bu adresin sonuna ekleyebilir veya download kısmına tıklayarak, açılan sayfada "No thanks, just start my download." linkine farenin sağ tuşuyla tıklayıp "Copy Link Location"\(Bağlantı adresini kopyala\)'a tıklarsanız güncel RPM paketinin adresini kopyalamış olursunuz.

```
yum install wget
wget https://dev.mysql.com/get/mysql-57-community-release-el7-11.noarch.rpm
```

#### 



