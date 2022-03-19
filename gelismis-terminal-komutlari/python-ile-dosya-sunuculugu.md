# Python ile Dosya Sunuculuğu

Eğer dosya transferi yapmak istediğiniz tarafın teknik araçlara erişimi yetersizse, birden fazla kişiye dosya sunmak isterseniz, dosyayı alacak kişilerin sadece internet tarayıcısı kullanarak sunucunuz üzerindeki herhangi bir klasöre erişmelerini sağlayabilirsiniz.

Bunun için Python'dan faydalanacak olmamıza rağmen, Python bilgisine ihtiyaç gerekmemektedir.

Örneğin `/root/resimler` dizinine (ve altındaki bütün dizinlere) HTTP ile erişilebilmesi için, ilgili dizine gidip aşağıdaki komutu çalıştırmak yeterli olacaktır.

```
[root@emre ~]# cd /root/resimler
[root@emre resimler]# python -m SimpleHTTPServer
Serving HTTP on 0.0.0.0 port 8000 ...
```

Artık ilgili sunucunun IP'sine 8000 portunu kullanarak bağlanırsak, dosyalara erişebiliriz.

![](../.gitbook/assets/simple-http-server.png)

Eğer 8000 dışında bir port kullanmak isterseniz, parametre olarak belirtmeniz yeterli. Örneği 9001 portu için:

```
[root@emre resimler]# python -m SimpleHTTPServer 9001
Serving HTTP on 0.0.0.0 port 9001 ...
```

Kopyalama işlemleri tamamlandıktan sonra, güvenlik gereği Python programını öldürmeniz gerekecektir. Bunu ister **kill** ile, ister **CTRL+C** ile _interrupt_ göndererek yapabilirsiniz.
