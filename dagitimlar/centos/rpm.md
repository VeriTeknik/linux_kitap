# rpm Paketleri

Red Hat için geliştirilmiş paket yönetim sistemidir. Paketler -çoğunlukla olduğu gibi- doğrudan derlenmiş dosyalar olarak dağıtılabileceği gibi, kaynak kodların dağıtımını (srpm, spm) da sağlayabilir.

RPM paketlerinin isimlendirmesinde genellikle aşağıdaki düzen izlenir.


```
<name>-<version>-<release>.<architecture>.rpm
<isim>-<versiyon>-<dağıtım>.<mimari>.rpm
```

Çoğunlukla yukarıdaki bölümlerden **isim** ve **mimari** kısmı bizi ilgilendirecektir. **isim** kısmı, tahmin edeceğiniz üzere yüklemek istediğimiz pakettir. **mimari** ise çalıştığımız sistemin işlemci mimarisidir. Örneğin x86, i386, arm gibi değerler alabilir. noarch yazması, paketin mimari bağımsız olması demektir. Örneğin düz metin dosyaları, Python/Perl scriptleri bu şekilde olabilir.

rpm dosyalarını pek çok yerden edinebilirsiniz. [rpmfind.net](http://rpmfind.net) bunlardan birisidir.

## Yükle

Bir rpm paketini yüklemek için:

```
rpm -ivh paket-dosyası.rpm
```

## Bağımlı Olduğu Paketleri Göster

Bir rpm paketini yüklemeden, bu paketin bağımlı olduğu diğer paketleri öğrenmek için:

```
rpm -qpR paket-dosyası.rpm
```

## Bağımlı Olduğu Paketler Hariç Yükle

Bir rpm paketini, bağımlı olduğu diğer paketleri gözardı ederek yükler. Çok sık gerekecek bir durum değildir, kimi zaman sisteminizdeki belirli kütüphanelerin değişmesini istemezseniz yapmanız gerekebilir.

```
rpm -ivh --nodeps paket-dosyası.rpm
```

## Yüklü Paketler Arasında Birini Sorgula

Yüklü paketleriniz içinde örneğin **htop** olup olmadığını merak ediyorsanız,

```
rpm -q htop
```

## Yüklü Paketlerin Tamamını Göster

```
rpm -qa
```

## Yüklü Pakedin Tüm Dosyalarını Görüntüle

Örneğin sisteminizdeki **nmap** paketinin hangi dosyaları yüklediğini merak ediyorsanız.

```
rpm -ql nmap
```

## Bir Pakedi Güncelle

```
rpm -Uvh paket-dosyası.rpm
```

## Bir Pakedi Sil

Bu seçenek ile paket kaldırılır. Eğer bu pakedin bağımlı olduğu paketler, başka bir paket tarafından kullanılıyorsa onlar da kaldırılır.

```
rpm -ev paket-ismi
```

## Bir Pakedi Bağımlı Olduğu Diğer Paketlere Dokunmadan Kaldır

Bu seçenek ile, paket kaldırılır ancak kendisinin bağımlığı olduğu paketlere dokunulmaz.

```
rpm -ev --nodeps paket-ismi
```

## Bir Dosyanın Hangi Pakete Ait Olduğu

Sisteminizde gördüğünüz bir dosyanın, hangi rpm pakediyle geldiğini öğrenmek istiyorsanız aşağıdaki gibi bir sorgulama yapabilirsiniz. Örneğin **/etc/my.cnf** dosyasının nereden geldiğini merak ediyorsak:


```
[root@emre /]# rpm -qf /etc/my.cnf
mysql55w-libs-5.5.43-1.w6.x86_64
```

