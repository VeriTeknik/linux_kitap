# rpm Paketleri

Red Hat için geliştirilmiş paket yönetim sistemidir. Paketler -çoğunlukla olduğu gibi- doğrudan derlenmiş dosyalar olarak dağıtılabileceği gibi, kaynak kodların dağıtımını (srpm, spm) da sağlayabilir.

RPM paketlerinin isimlendirmesinde genellikle aşağıdaki düzen izlenir.


```
<name>-<version>-<release>.<architecture>.rpm
<isim>-<versiyon>-<dağıtım>.<mimari>.rpm
```

Çoğunlukla yukarıdaki bölümlerden **isim** ve **mimari** kısmı bizi ilgilendirecektir. **isim** kısmı, tahmin edeceğiniz üzere yüklemek istediğimiz pakettir. **mimari** ise çalıştığımız sistemin işlemci mimarisidir. Örneğin x86, i386, arm gibi değerler alabilir. noarch yazması, paketin mimari bağımsız olması demektir. Örneğin düz metin dosyaları, Python/Perl scriptleri bu şekilde olabilir.



