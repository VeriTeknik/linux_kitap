# rpm Paketleri

Red Hat için geliştirilmiş paket yönetim sistemidir. Paketler -çoğunlukla olduğu gibi- doğrudan derlenmiş dosyalar olarak dağıtılabileceği gibi, kaynak kodların dağıtımını (srpm, spm) da sağlayabilir.

RPM (RPM Package Manager, eskiden Red Hat Package Manager), RHEL tabanlı dağıtımların (CentOS Stream, Rocky Linux, AlmaLinux, Fedora vb.) kullandığı temel paket yönetim sistemidir. Paketler genellikle `.rpm` uzantılı dosyalardır ve derlenmiş programları, kütüphaneleri, yapılandırma dosyalarını ve kurulum/kaldırma betiklerini içerir. Kaynak kod paketleri ise `.src.rpm` uzantılıdır.

**Önemli Not:** `rpm` komutu, paketleri doğrudan yönetmek için kullanılan düşük seviyeli bir araçtır. **Bağımlılıkları otomatik olarak çözmez.** Bu nedenle, paket kurma, kaldırma veya güncelleme işlemleri için genellikle `dnf` (veya eski sistemlerde `yum`) gibi daha üst seviye paket yöneticilerinin kullanılması **şiddetle tavsiye edilir**. `rpm` komutu daha çok paketleri sorgulamak, dosyaların hangi pakete ait olduğunu bulmak veya bağımlılıkları manuel olarak yönetmek gerektiğinde kullanılır.

RPM paketlerinin isimlendirmesinde genellikle aşağıdaki düzen izlenir.


```
<name>-<version>-<release>.<architecture>.rpm
<isim>-<versiyon>-<dağıtım>.<mimari>.rpm
```

Çoğunlukla yukarıdaki bölümlerden **isim** ve **mimari** kısmı bizi ilgilendirecektir. **isim** kısmı, tahmin edeceğiniz üzere yüklemek istediğimiz pakettir. **mimari** ise çalıştığımız sistemin işlemci mimarisidir. Örneğin x86, i386, arm gibi değerler alabilir. noarch yazması, paketin mimari bağımsız olması demektir. Örneğin düz metin dosyaları, Python/Perl scriptleri bu şekilde olabilir.

rpm dosyalarını genellikle dağıtımınızın yapılandırılmış depolarından (`dnf` aracılığıyla) veya güvenilir üçüncü parti kaynaklardan edinebilirsiniz. İnternetten rastgele indirilen RPM paketlerini kurmak güvenlik riski oluşturabilir.

## `rpm` Komutu ile Sorgulama ve Yönetim

Aşağıda `rpm` komutunun bazı yaygın kullanım örnekleri verilmiştir. Unutmayın, kurulum/kaldırma/güncelleme için genellikle `dnf` tercih edilmelidir.

**Paket Kurulumu (Bağımlılıkları Çözmez!):**

İndirilmiş bir RPM dosyasını kurmak için (genellikle `-ivh` kullanılır: install, verbose, hash marks):

```bash
rpm -ivh paket-dosyası.rpm
```
**Not:** Eğer bu paket başka paketlere bağımlıysa ve bu bağımlılıklar sistemde kurulu değilse, `rpm` komutu hata verecektir. `dnf install paket-dosyası.rpm` komutu ise bağımlılıkları depolardan otomatik olarak bulup kurmaya çalışır.

**Paket Sorgulama:**

Bir RPM dosyasının (kurulu olmayan) bağımlı olduğu paketleri listelemek için (`-p` ile dosya üzerinde işlem yapılır):

```bash
rpm -qpR paket-dosyası.rpm
```

Bir RPM dosyasının (kurulu olmayan) içerdiği genel bilgileri görmek için:
```bash
rpm -qip paket-dosyası.rpm
```

Bir RPM dosyasının (kurulu olmayan) içerdiği dosyaları listelemek için:
```bash
rpm -qlp paket-dosyası.rpm
```

**Kurulu Paketleri Sorgulama:**

Sistemde belirli bir paketin kurulu olup olmadığını kontrol etmek için:
```bash
rpm -q htop
```

Sistemde kurulu tüm paketleri listelemek için (`less` ile kullanmak faydalıdır):
```bash
rpm -qa | less
```

Kurulu bir paket hakkında detaylı bilgi almak için:
```bash
rpm -qi nmap
```

Kurulu bir paketin içerdiği dosyaları listelemek için:
```bash
rpm -ql nmap
```

Belirli bir dosyanın hangi kurulu pakete ait olduğunu bulmak için:
```bash
rpm -qf /etc/my.cnf
```

Kurulu bir paketin dosyalarının bütünlüğünü doğrulamak için (değiştirilmiş, eksik dosyaları vb. kontrol eder):
```bash
rpm -V nmap
```
(Eğer komut çıktı vermezse, paket dosyaları orijinal durumdadır.)

**Paket Kurulum/Güncelleme/Kaldırma (Düşük Seviye):**

**Uyarı:** Aşağıdaki komutlar bağımlılıkları otomatik yönetmez. Genellikle `dnf` kullanın.

Bağımlılıkları göz ardı ederek paket kurmak (Genellikle önerilmez!):

```bash
rpm -ivh --nodeps paket-dosyası.rpm
```

Bir paketi güncellemek veya kurulu değilse kurmak için (`-U` update/install):
```bash
rpm -Uvh paket-dosyası.rpm
```
(Sadece kuruluysa güncellemek için `-F` veya `--freshen` kullanılır.)

Bir paketi kaldırmak için (`-e` erase):
```bash
rpm -e paket-ismi
```
**Not:** Bu komut, kaldırılan pakete bağımlı olan diğer paketlerin çalışmasını bozabilir. `dnf remove paket-ismi` komutu bağımlılıkları daha güvenli yönetir.

Bağımlılıkları kontrol etmeden bir paketi kaldırmak için (Genellikle önerilmez!):
```bash
rpm -ev --nodeps paket-ismi
```

Özetle, `rpm` komutu paketler hakkında bilgi almak ve düşük seviyeli işlemler yapmak için kullanışlıdır, ancak günlük paket yönetimi (kurulum, kaldırma, güncelleme) için `dnf` komutunu kullanmak daha güvenli ve pratiktir.
