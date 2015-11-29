# Ortak Network Dosyaları

Her ne kadar dağıtımların network ayarlarını yaparken farklılıklar olduğunu gördüysek de, bazı dosyalar tüm sistemlerde aynı işlevi görmektedir. Bu bölümde bu dosyaları inceleyeceğiz.

## hostname

Sistemin kendi ismini bilmesi için düzenlenmesi gereken iki dosyadan biridir. ```/etc/hostname``` yolundadır. Bu dosyanın içinde tek satır bulunur.

```bash
eaydin@dixon ~ $ cat /etc/hostname
dixon
```

## hosts

Sistemin adreslere karşılık isim tuttuğu dosyadır.

```bash
eaydin@dixon ~ $ cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	dixon

94.103.47.66 test-centos1 
94.103.47.78 test-debian1

94.103.33.130 plugged.in
```

Yukarıdan görüleceği gibi, sistemin localhost'u tanıması için 127.0.0.1'e işaret etmesi gerekir. Benzer şekilde hostname'de tanımladığımız değer de burada belirtilmelidir.

Alttaki satırlar ise kullanıcının kendince yarattığı kısa yollardır. Burada gördüğünüz plugged.in değeri sitenin gerçek adresidir. ```/etc/hosts``` dosyasında yer alması, herhangi bir şekilde DNS'lerden alınan bilginin önüne geçilmesi anlamına gelir. Kısacası artık sistem üzerinde her program plugged.in adresine gitmek için 94.103.33.130 IP'sini kullanacaktır. Eğer buraya başka bir değer, örneğin 94.103.32.32 yazsaydık, sistem üzerinde her program plugged.in'e gidecekken DNS'lere bakmadan 94.103.32.32'ye yönlenecekti, ve veritech.net sayfası cevap verecekti.

host dosyası manual dosyasına erişmek için

```bash
man 5 hosts
```

## resolv.conf

Sistem üzerinde nameserver'ların (DNS) tutulduğu dosyadır. Aşağıdaki yapıyı izler.

```
domain dixon
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Yukarıdaki haliyle sistem nameserver olarak Google'ın 8.8.8.8 ve 8.8.4.4 adreslerini kullanmaktadır.

domain ifadesi sistemin kendi ismine (localhost) işaret eder. Buraya domain yazılmasaydı, sistem hostname'i kendisi okuyacaktı.

resolv.conf dosyası manual dosyasına erişmek için

```bash
man 5 resolv.conf
```