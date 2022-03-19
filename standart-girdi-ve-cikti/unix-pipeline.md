# UNIX Pipeline

## UNIX Pipeline

Douglas McIlroy, işletim sistemindeki programların (veya parçacıkların) birer standart girdisinin ve standart çıktısının olması gerektiği fikrini aslında yukarıdaki örneklerde bahsettiğimiz gibi sadece dosya yönlendirme amacıyla geliştirmemiştir. McIlroy'un amacı, aslında programların birbirleriyle, arada geçici bir dosya veya kullanıcı müdahalesine gerek olmaksızın veri akışının sağlanmasıdır. Bunu yaparken de, bir programın çıktısının diğer programın girdisi olarak yönlendirilmesini kurgulamıştır. McIlroy bu metodolojiyi ilk defa 1973'te kurgulamıştır ve Ken Thompson,`pipe()`sistem çağrısını UNIX versiyon 3 üzerinde uyarlamıştır. Bunu yaparken de yönlendirme işareti olarak`|`kullanılmıştır. McIlroy, bu karakterin kullanımını Thompson'ın geliştirdiğini belirtir. Bu yatak çubuk, aslında bir _boru (pipe)_ olarak düşünülünce, tıpkı bir su tesisatındaki boru hattı gibi, bir noktadan giren bilginin, diğer noktadan çıkmasını sağladığı için _pipeline_ terminolojisini doğurmuştur.

Örneğin`less`programı, aslında kendisine sağlanan bilginin terminal ekranına sığacak şekilde parçalanmasını sağlar. Tıpkı`cat`programında olduğu gibi, parametrik kullanımla dosya adı belirtilebilir, veya standart girdi olarak veri sağlanabilir.

Eğer çok fazla dosya içeren bir dizin içerisinde`ls -l`komutunu çalıştırırsak, standart çıktı içerisinde kaybolabiliriz. Bunun için`ls -l`komutunun çıktısını,`less`programına girdi olarak sunabiliriz. Bunun için yapmamız gereken tek şey, aralarında _pipe_ işaretini kullanmak olacaktır.

```
root@ubuntu:/mnt/backups/orthogonal/# ls -l | less
total 7019320
-rwxr-xr-x 1 root root     15099 May 23  2016 E28b30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root  77772699 May 23  2016 E28b30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28b40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root  13464608 May 23  2016 E28b40_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c10_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 260127268 May 23  2016 E28c10_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c20_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 261387832 May 23  2016 E28c20_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 262376992 May 23  2016 E28c30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28c40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 250611074 May 23  2016 E28c40_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d10_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root  12259662 May 23  2016 E28d10_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d20_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 130844393 May 23  2016 E28d20_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 244966770 May 23  2016 E28d30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E28d40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 208044160 May 23  2016 E28d40_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E29a30_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 216910411 May 23  2016 E29a30_0HGK030HF2015COG.ecw
-rwxr-xr-x 1 root root     15099 May 23  2016 E29a40_0HGK030HF2015COG.aux
-rwxr-xr-x 1 root root 176545547 May 23  2016 E29a40_0HGK030HF2015COG.ecw
--More--
```

Bu çıktının en altında görülen`--More--`kısmı aslında,`less`programının getirdiği bir sonuç. Aynı şekilde`less`çalıştırıldıktan sonra, standart girdiden (klavyeden) herhangi bir _input_ beklemektedir. Kısacası`less`programı devreye girdikten sonra eğer bir tuşa basarsak,`ls -l`çıktısının geri kalanının ekranımıza sığdığı kadarını karşımızda görebiliriz.

Aslında buradaki notasyon şu şekilde işler.`komut1 | komut2 | komut3`şeklinde _-neredeyse- sonsuza kadar_ komutları birbirine bağlayabilirsiniz ("Neredeyse sonsuza kadar" olmasının sebebini **File Descriptor** bölümünde göreceğiz). Buradaki komutlar birbirinden farklı olabileceği gibi, birbirleriyle aynı da olabilir. Bu komutların birbirleriyle _konuşabilmeleri_ için bilmeleri gereken bir şey de yoktur. McIlroy'un 2. maddede bahsettiği, farklı programların birbirleriyle konuşmasını sağlama düşüncesi de burada ortaya çıkar.

Örneğin, sistemimizdeki yüklü Python modüllerinin bulunduğu`/usr/local/lib/python3.5/dist-packages`dizini içerisinde sonu`.py`ile bitmeyen dosyaların sayısını öğrenmek istersek, aşağıdaki komut dizisini kullanabiliriz.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | wc -l
129
```

Buradaki ilk komut,`ls /usr/local/lib/python3.5/dist-packages`çok tanıdık bir iş yapıyor: dizinin içeriğini listeliyor. Ancak içeriğini standart çıktıda göstermek yerine,`grep`programına iletiyor.`grep`ise standart girdiden okuduğu veriler üzerinde`-v .py$`parametrelerini çalıştırıyor. Burada yaptığı iş, "sonu`.py`ile biten dosyaları göstermemek". Detaylarına grep ve Regular Expressions bölümünde değineceğiz. Elimizde artık sonu`.py`ile bitmeyen dosyaların bir listesi var, ancak bunu görüntelemek istemiyoruz, çünkü bunların sayısını öğrenmek istiyoruz. Öyleyse standart çıktısını`wc`(_word count_) programına iletiyoruz.`wc -l`ile standart girdiden gelen verinin kaç satır olduğunu sayıyoruz, ve bu sonucu artık standart çıktıya yazıyoruz. Bu yüzden standart çıktı olarak **129** değerini görüyoruz.

Tahmin edeceğiniz gibi, burada elde ettiğimiz sayıyı standart çıktı yerine aşağıdaki gibi bir dosyaya da yönlendirebilirdik.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | wc -l > paket_sayisi
```

## T-Pipe

Yaptığımız son örnekte, dosyaların sayısını`paket_sayisi`isminde bir dosyaya yazdırdık. Ancak eğer bu dosya sayısını ekranda görmek istiyor, ancak dosyaların bir listesini bir dosyaya yazdırmak istiyor olsaydık?

Örneğin, eğer paketlerin listesini bir dosyaya yazdırmak isteseydik, sondaki word count komutunu kullanmayıp, grep'in standart çıktısını bir dosyaya yönlendirmemiz gerekecekti.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ > paket_listesi
```

Öte yandan, paketlerin sayısını ekranda görmek için, daha önce kullandığımız komutu kullanmamız gerekirdi.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | wc -l
129
```

Bir akış içerisinde standart çıktıyı yönlendirmek yerine, birkaç parçaya bölme işini yapmak için`tee`isimli program geliştirilmiştir. Örneğin`tee`kullanarak aşağıdaki komut dizisini yazarsak, yukarıdaki iki komutu ayrı ayrı çalıştırmamıza gerek kalmaz.

```
eaydin@eaydin-vt ~ $ ls /usr/local/lib/python3.5/dist-packages | grep -v .py$ | tee paket_listesi | wc -l
129
```

Gördüğünüz gibi, standart çıktıya yine word count programının sonucu olan **129** yazıldı. Ancak arada`tee`programı,`grep`'in çıktısını`paket_listesi`dosyasına yazdı. Dosyanın baş kısmına`head`programıyla bakıp içeriğine göz atabiliriz.

```
eaydin@eaydin-vt ~ $ head paket_listesi 
alabaster
alabaster-0.7.10.dist-info
asn1crypto
asn1crypto-0.24.0.dist-info
attr
attrs-17.3.0.dist-info
babel
Babel-2.5.1.dist-info
bcrypt
bcrypt-3.1.4.dist-info
```

`tee`programı, aslında UNIX pipeline'ında bir T-Pipe görevi gördüğü için bu ismi almıştır. UNIX pipeline'ı aslında bir boru tesisatı gibi düşünülebilir, ismi de buradan gelir zaten. Yazılımlar birbirlerine borularla bağlıdır, ve akış tek yönlüdür. Soldan sağa doğru akış gerçekleşir. Eğer bu akış içerisinde bir dallanmaya ihtiyaç duyarsak, boru tesisatlarında olduğu gibi T şeklinde bir boru kullanmamız gerekir. Böylece akışı iki veya daha fazla dala ayırabiliriz. Programa birden fazla parametre verip, çıktının birden fazla dosyaya da yazdırılmasını sağlayabiliriz.

Kısacası`tee`programı, standart girdiden gelen veriyi, hem kendisine parametre olarak verilen dosyalara yazar, hem de standart çıktıya yönlendirir. Böylece kendi standart çıktısı hangi programa standart girdi olarak sunulmuşsa, komutların akışı devam edebilir.

Normal şartlar altında **>** işaretinin yaptığı gibi, kendisine parametre olarak verilen dosyaları silip üzerine yazar. Ancak bu dosyaların mevcut bilgilerini koruyup sonuna veri eklemesini istersek (_append_ etmesini istersek)`-a`parametresiyle çalıştırmak gerekir. Bu, standart çıktı yönlendirmedeki **>>** işaretinin karşılığı gibi düşünülebilir.

### tee Örneği

Kullanımı hakkında, GNU Core Utils'de paylaşılan birkaç örnek fikir verebilir.

Örneğin herhangi bir dosyanın internetten indirilmesi ile dosyanın MD5 Checksum hesaplanmasının sağlanmasını bir arada yapmak için aşağıdaki komut incelenebilir.

```
wget -O - http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso | tee centos7.iso | md5sum > centos7.md5
```

Yukarıdaki komut VeriTeknik mirrorlarından CentOS 7 Minimal güncel versiyonunu indirir,`tee`ile dosya`centos7.iso`olarak kaydedilir ancak standart çıktıya yazılan veri`tee`ile`md5sum`programına aktarılır ve MD5 Checksum hesaplandıktan sonra`centos7.md5`dosyasına kaydedilir. Burada önemli olan noktalardan birisi, MD5 hesaplanması için verinin tamamının indirilmesinin beklenmemesidir. Veri indikçe `md5sum` programına veri akacak ve hesaplama başlayacaktır. Dolayısıyla verinin önce diske yazılması beklenmemektedir. Verinin önce diske yazılmasını, sonra`md5sum`programının diskten tekrar okumasını sağlamak için şöyle yapabilirdik:

```
wget http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso centos7.iso && md5sum centos7.iso
```

Ancak burada işlemler sırayla yapılacağı için, UNIX Pipeline'ın avantajlarından hiçbir şekilde faydalanmamış olurduk.

### Process Substitution

Eğer yukarıda indirdiğimiz dosyanın, indirilirken, aynı anda hem MD5 Checksum'ının, hem de SHA1 Checksum'ının hesaplanmasını isteseydik,`tee`ile standart çıktıyı iki farklı programa yönlendirmemiz gerekecekti. Bu işleme _process substitution_ denilir. Aşağıdaki örnek ile görülebilir.

```
eaydin@eaydin-vt ~/Downloads $ wget -O - http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso | tee 
>(sha1sum > centos7.sha1) 
>(md5sum > centos7.md5) > centos7.iso
--2018-03-14 14:19:36--
http://mirror.veriteknik.net.tr/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso
Resolving mirror.veriteknik.net.tr (mirror.veriteknik.net.tr)... 94.103.33.100, 2a00:7300:1::101
Connecting to mirror.veriteknik.net.tr (mirror.veriteknik.net.tr)|94.103.33.100|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 830472192 (792M) [application/octet-stream]
Saving to: ‘STDOUT’


- 100%[=============================================================>] 792,00M  11,1MB/s    in 73s     


2018-03-14 14:20:49 (10,9 MB/s) - written to stdout [830472192/830472192]


eaydin@eaydin-vt ~/Downloads $ cat centos7.md5
5848f2fd31c7acf3811ad88eaca6f4aa  -
eaydin@eaydin-vt ~/Downloads $ cat centos7.sha1 
aae20c8052a55cf179af88d9dd35f1a889cd5773  -
```

Yukarıdaki komut dizisinin kritik noktası,`tee >(sha1sum > centos7.sha1) >(md5sum > centos7.md5)`bölümüdür. Burada `tee`, standart çıktısını iki farklı işleme daha yönlendirir. Bu işlemler parantez içinde belirtilir, ve daha önce gördüğümüz **>** işareti ile bu işlemlere yönlendirme yapılır. Burada dikkat edilmesi gereken nokta, **>** işareti ile **(** arasında boşluk bulunmaması gerektiğidir. Öte yandan`sha1sum`ve`md5sum`sonrasında gelen **>** işaretleri doğrudan bu programların standart çıktılarını yönlendirme amacıyla yazıldığından, boşluk konması problem yaratmaz, zaten parantez içinde kullanılma sebepleri de budur.

Sanki`tee`programı iki farklı dosyaya yazmak yerine, iki farklı işleme standart çıktıyı yönlendirmektedir. Burada dosya (_file_) yerine işlem (_process_) koyduğumuz için, bir değişiklik (_substitution_) işlemi yapmış olduk. Bu yüzden bu yönteme _process substitution_ denilir. Bu örnekte de, indirme işleminin, SHA1 hesaplamasının ve MD5 hesaplamasının birbirlerini beklemediğini, işlemin paralel gerçekleştirildiğini hatırlatmakta fayda var.
