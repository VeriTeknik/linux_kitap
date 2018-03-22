## Standart Hata ve File Descriptor

Şimdiye kadar standart girdi ve çıktı yönlendirmelerini, UNIX pipeline dahilinde programların birbirleriyle iletişim sağlayabilmesi için nasıl kullandığımızı gördük.

Aslında UNIX üzerinde programların üç temel veri akış biçimi vardır. Standart girdi, standart çıktı ve standart hata. İngilizceleri _Standard Input_, _Standard Output_ ve _Standard Error_ olarak geçer. Bu yüzden bazen kısaltmalarını _stdin_, _stdout_, _stderr_ olarak görebilirsiniz.

Standart girdi ve standart çıktının varoluş amaçlarını biraz önceki örnelerde irdeledik. Standart hata ise, program çalıştırıldığında, programın hatalarını yönlendireceği noktayı belirtir. Normal şartlar altında bir program sonuçlarını da \(standart çıktı\) hatalarını da \(standart hata\) ekranımıza yönlendirir. Böylece program hata verdiyse, çıktıları arasında görürüz. Ancak bu her zaman istediğimiz bir şey olmayabilir. Özellikle çok fazla çıktı veren programlarda gerçekleşen hataların ayıklanması zorlaşabilir. Ayrıca, programların standart çıktılarını başka program veya dosyalara yönlendirdiğimizde, hataların da burada yer almasını istemeyebiliriz. Bu tip durumların önüne geçebilmek için, standart çıktıdan bağımsız olan bir çıktı biçimi olarak standart hata tanımlanmıştır. Bu sayede örneğin bir programın standart çıktısı pipe ile bir başka programa girdi olarak sunulurken, programın çalışması sırasında oluşacak hatalar kullanıcının terminal ekranında görülebilir.

Standart hata kavramı, UNIX'in 6. versiyonunun ardından Dennis Ritchie tarafından geliştirilmiştir. Bu yöntem ile birbirine standart girdi-çıktı yönlendiren programların standart hatalarının terminale yazdırılması durumunda, hangi hata mesajının hangi programdan geldiğinin bilinmemesi problemiyle de karşılaşılmıştır. Douglas McIlroy, bu problemin farkında olduklarını ama asla tamamen çözülmediğini, _UNIX Programmer's Manual_'ın 3. versiyonunda belirtir.

> All programs placed diagnostics on the standard output. This had always caused trouble when the output was redirected into a file, but became intolerable when the output was sent to an unsuspecting process. Nevertheless, unwilling to violate the simplicity of the standard-input-standard-output model, people tolerated this state of affairs through v6. Shortly thereafter Dennis Ritchie cut the Gordian knot by introducing the standard error file. That was not quite enough. With pipelines diagnostics could come from any of several programs running simultaneously. Diagnostics needed to identify themselves. Thus began a never quite finished pacification campaign: a few recalcitrant diagnostics still remain anonymous or appear on the standard output.

Standart hata'nın kullanımı için önce, _file descriptor_ konsepti hakkında kısaca fikir sahibi olmamızda fayda var.

## File Descriptor

UNIX üzerinde aslında her şey bir dosyadır. Klavyeniz, seri port cihazınız, ethernet cihazınız, yazıcınız, metin dosyanız, ekranınız... Tamamını UNIX dosya sistemi birer dosya olarak tanımlar. Programlarınız bu dosyalara erişmek istediklerinde, işletim sistemi tarafından erişim izniniz olup olmadığı kontrol edilir, ardından sistem programa "negatif olmayan bir tam sayı" olarak dosyayı sunar. Kısacası program, aslında dosyanın ismiyle, yoluyla neredeyse hiç ilgilenmez, programın etkileşim kurduğu "şey" bir tam sayıdır, bu tam sayı da işletim sistemi tarafından dosya yoluna tercüme edilir. Bu tam sayılar, program ile işletim sistemi arasındaki dosya tanımını yaptığı için, _file descriptor_ ismini alır. Erişilmiş bir dosya hakkındaki bütün bilgi işletim sisteminin sorumluluğundadır, yazılımlar bu dosyaları sadece birer file descriptor olarak tanırlar. Standart girdi, standart çıktı ve standart hata, ayrı file descriptor'lar ile temsil edilir.

Daha önce incelediğimiz `stdio.h` için bu gösterimler stdin, stdout, stderr iken, bunlara karşılık gelen tam sayılar da genellikle bash gibi kabuklarda kullanılır.

| İsim | Sayısal Değer | &lt;stdio.h&gt; Kullanımı |
| :--- | :--- | :--- |
| Standart Girdi | 0 | stdin |
| Standart Çıktı | 1 | stdout |
| Standart Hata | 2 | stderr |

Daha önce gördüğümüz **&lt; ** ve **&gt;** gösterimleri de aslında bunu doğrular niteliktedir. Programlar girdinin nereden geldiğini ve çıktının nereye gideceğini bilmezler, onlar için sadece 0, 1 ve 2 \(veya stdin, stdout ve stderr\) vardır. Kabuk aracılığıyla **&lt; ** ve **&gt;** gösterimlerini kullanarak aslında işletim sistemi hangi programın nereden veri alıp nereye ileteceğini kontrol eder.

Yukarıdaki tablodan görüleceği gibi, aslında standart çıktının file descriptor'ının sayısal karşılığı 1'dir. Yani, aşağıdaki iki örnek, aynı anlama gelmektedir. İlk başta, daha önce uyguladığımız örneklerde olduğu gibi, file descriptor kullanmadan oluşan sonuca bakalım.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler > cikti1
eaydin@eaydin-vt ~/devel/lower $ cat cikti1 
AbCdE
```

Aşağıda ise, file descriptor ile aynı sonucun elde edildiğini görebiliyoruz.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler 1> cikti2
eaydin@eaydin-vt ~/devel/lower $ cat cikti2
AbCdE
```

Yönlendirmelerde standart çıktı çok sık kullanıldığı için, `1>` kullanımı olmadan da standart çıktı yönlendirmesi gerçekleştirilir.

Şimdi benzer işlemi standart hata yönlendirmesinde kullanalım.

cat programı, kendisine parametre olarak kaç dosya verilirse, tamamını peş peşe eklemekle görevlidir, ismi de zaten buradan gelir, _con**cat**enate_ sözcüğünün kısaltılmışıdır. Aşağıdaki kullanım açıklayıcı olacaktır.

```
eaydin@eaydin-vt ~/devel/lower $ echo Bu bir cümle > cumleler
eaydin@eaydin-vt ~/devel/lower $ cat cumleler 
Bu bir cümle
eaydin@eaydin-vt ~/devel/lower $ cat karakterler cumleler 
AbCdE
Bu bir cümle
```

Eğer parametre olarak verdiğimiz dosyalardan birisi yoksa \(veya okunamıyorsa\), hata verir.

```
eaydin@eaydin-vt ~/devel/lower $ cat paragraf
cat: paragraf: No such file or directory
```

Aşağıda, iki dosyayı birleştirmesini istiyoruz.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler paragraf > deneme
cat: paragraf: No such file or directory
eaydin@eaydin-vt ~/devel/lower $ cat deneme
AbCdE
```

Ancak gördüğünüz gibi, dosyalardan birisi \(`paragraf`\) olmadığı için program hata verdi. Yine de `deneme` dosyası oluşturuldu ve içinde sadece `karakterler` dosyasının içeriği yer alıyor. Eğer çok fazla dosyayı birleştiriyor olsaydık, veya bu işlemi bir script'in içerisinde kullanıyor olsaydık, veya pipe ile birçok işlemi birleştiriyor olsaydık, bu işlemin hatalarını terminal ekranına yazdırmak yerine bir dosyaya yönlendirmesini tercih edebilirdik. Bunun için file descriptor kullanımı gerekir ve yukarıdaki tablodan anlaşılacağı gibi `2>` notasyonuyla bu işlem gerçekleştirilir.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler paragraf > deneme 2> hatalar
eaydin@eaydin-vt ~/devel/lower $ cat deneme 
AbCdE
eaydin@eaydin-vt ~/devel/lower $ cat hatalar
cat: paragraf: No such file or directory
```

Burada önce standart çıktıyı `deneme` dosyasına yönlendirdiğimizi, standart hatayı ise `hatalar` dosyasına yönlendirdiğimizi görebilirsiniz. Yaptığımız işlemin sırasının bir önemi yok. Yani önce standart hatayı yönlendirip, sonra standart çıktıyı yönlendirebilirdik. Genellikle bu yapılmaz \(1 ve 2 sırasını içgüdüsel olarak koruruz\) ancak yapılmasında bir mahsur bulunmaz.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler paragraf 2> hatalar > deneme
eaydin@eaydin-vt ~/devel/lower $ cat hatalar
cat: paragraf: No such file or directory
eaydin@eaydin-vt ~/devel/lower $ cat deneme
AbCdE
```

Bütün bu işlemlerde, standart çıktı yönlendirmesi için **1&gt;** notasyonunu da kullanabilirdik. Yine, alışkanlık gereği pek kullanılmaz sadece. Yani aşağıdaki işlem ile yukarıdaki aynı işe yarayacaktır.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler paragraf 2> hatalar 1> deneme
```

### Open File Descriptor Limiti

File descriptor'ların UNIX sistemimiz üzerinde aslında çekirdek \(kernel\) tarafından idare edildiğini öğrendik. Yani bir program standart çıktısının nereye yazıldığını kendisi bilmiyor, ancak işletim sistemi her programın standart çıktısının \(ve standart girdisinin ve standart hatasının\) nereye işaret ettiğini, dolayısıyla her programın file descriptor'larının nereye karşılık geldiğini biliyor. Bu durum UNIX çekirdeğinin pek çok program için pek çok dosya işaretini _aklında tutmasına_ sebep olur. Buradaki "aklında tutması" aslında sistemin bunu _RAM'de tutması_ anlamına gelir. Bu yüzden sistemde file descriptor'ların bir limiti bulunur. Buna "o anda sistemin aklında tuttuğu file descriptor'lar" anlamına gelen **open file descriptor limit** denilir.

Daha önce `komut1 | komut2 | komut3` şeklinde bir pipeline oluştururken aslında _-neredeyse sonsuza kadar-_ bu diziyi uzatabileceğinizi söylemiştik. Buradaki "neredeyse" kısmı da bu limitten kaynaklanır. Aslında sisteminizin open file descriptor limiti kadar uzun bir dizi oluşturabilirsiniz, çünkü buradaki her program için farklı file descriptor'lar tanımlanmaktadır. İşletim sistemi bunların ne kadarını aynı anda aklında tutabilirse, o kadar komutu pipeline içerisinde kullanabilirsiniz demektir. Yine de bu limit, sunucular üzerinde pratik kullanımlarda fark etmeyeceğiniz kadar yüksektir. Genellikle gömülü sistemlerde \(kaynakların ekonomik sebeplerle düşük tutulduğu sistemlerde\) problem teşkil etmeye başlar.

Sisteminiz üzerinde bu limitler farklı biçimlerde temsil edilir. İşletim sisteminizin tamamının open file descriptor limitini öğrenmek için `/proc/sys/fs/file-max` dosyasının içeriğine bakmanız yeterli olacaktır. Örneğin:

```
[root@emre ~]# cat /proc/sys/fs/file-max
386774
```

Burada incelediğimiz sistemin _aynı anda_ 386774 tane file descriptor'ın açık olmasını desteklediğini görüyoruz. Yani UNIX pipeline'ında peş peşe çalıştırdığımız komutlar maksimum bu sayıya ulaşacak kadar file descriptor oluşturabilirler, ve tabii bu sırada çalışan programları da \(network servislerinin sağlanması, init programı, varsa çalışan veritabanları, başka yazılımlar vb.\) göz önünde bulundurmak gerekir. Hiç pipeline oluşturmasak bile, aslında çalışan programların da toplam bu kadar file descriptor oluşturabileceği anlamına gelir.

Ancak bu durum, tek bir programın \(veya kullanıcının\) 386774 limitinin çok çok büyük bir kısmını, örneğin 386000 tanesini işgal etmesine sebep olabilir. Bu tip durumların önüne geçmek için modern işletim sistemlerinde program başına open file descriptor limiti bulunmaktadır. Bunu öğrenmek için aşağıdaki komutu çalıştırabilirsiniz:

```
[root@emre ~]# ulimit -n
1024
```

Buradan görüleceği üzere, aslında bir program çalıştırıldığında, kendisine işletim sisteminin çekirdeği tarafından 1024 tane file descriptor oluşturma hakkı tanınır.

_"Ama önceki bölümlerde programların stdin, stdout ve stderr şeklinde 3 tane file descriptor'ı olduğunu söylemiştik? Tek program neden 1024 tane file descriptor'a ihtiyaç duysun ki?"_

Doğru, ve bu sorunun cevabını ilerleyen bölümlerde, file descriptor'ların doğasını daha derinlemesine irdelediğimizde alacağız. Şimdilik bu soruyu bir kenara bırakalım.

Sistemimizdeki open file descriptor limiti, program başına olsun veya olmasın, işletim sistemimizin limitlerine ve RAM'ine bağlı olduğu için, hali hazırda çekirdeğin aklında tuttuğu file descriptor sayısını görmek isteyebiliriz. Bunun için aşağıdaki komutu çalıştırabiliriz:

```
[root@emre ~]# cat /proc/sys/fs/file-nr
736    0    386774
```

Burada yine biraz önceki sayı olan 386774'ü, yani üst limiti görüyoruz. İlk baştaki 736 ise aslında sistemin şu anda aklında tuttuğu file descriptor sayısıdır. Dolayısıyla bu sistem üzerinde `386774-736=386038` tane daha file desciptor açabiliriz, ancak bunları programlara \(process'lere\) yaymak gerekecektir. Ortadaki 0 sayısı ise, sistem tarafından rezerve edilmiş \(_allocated_\) ancak kullanılmayan file descriptor'ların sayısını gösteriyor. Yani bu örnekte sistem "rezerve ettiği" bütün file descriptorları \(736 tane\) kullanmış.

Bu limitlerin nasıl düzenleneceğini, program başına neden limitler olduğunu biraz daha ilerleyen bölümlerde irdeleyeceğiz. Şimdi programların üç temel file descriptor'ına geri dönelim.

### Standart Hatanın Standart Çıktıya Yönlendirilmesi

Her ne kadar standart hata yönlendirmesi, standart çıktı ile aynı noktaya yazılmasını istemediğimizden dolayı ortaya çıkmış olsa da, bazı durumlarda hata ve çıktıyı aynı yere yazmak isteyebiliriz. Bu gibi durumlar için, file descriptor kullanımında farklı bir gösterim kullanılır.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler paragraf > sonuc 2>&1
eaydin@eaydin-vt ~/devel/lower $ cat sonuc
AbCdE
cat: paragraf: No such file or directory
```

Yukarıdaki örnekte, yine `cat` programı ile `karakterler` ve `paragraf` dosyalarını okuyup birleştirmek istedik. Birleşmiş çıktıyı da `sonuc` isminde bir dosyaya yazmak istedik. Ancak `paragraf` diye bir dosyamız bulunmadığı için, programın hata vermesini bekleriz. Fakat programı çalıştırırken, standart hatayı da, standart çıktıya yazmasını söyledik. Standart çıktımız `sonuc` dosyası olduğu için, hatalarımızı ekranda görmek yerine, `sonuc` dosyasına görmeyi bekleriz. Gerçekten de `sonuc` dosyasının içeriğine baktığımızda hem `karakterler` dosyasının içeriğini, hem de `paragraf` dosyası bulunamadığı için `cat` programınım verdiği hatayı görüyoruz.

Burada standart hatayı, standart çıktıya yönlendirmek için kullanılan notasyon biraz farklı görünebilir: `2>&1`

Bu gösterimin ne anlama geldiği, aslında yine Brian Kernighan ve Dennis Ritchie'nin The C Programming Language kitabının 8. bölümünde yatıyor:

> A file descriptor is analogous to the file pointer used by the standard library \[...\]

Yani, bir _file descriptor_, aslında C programlama dilinde standart kütüphanenin kullandığı _file pointer_'lar ile aynı işi yapıyor. C'de pointer gösterimi **&** işaret ile yapılmakta. Bunu, daha önce 1 olarak tanımlanmış bir değerin adresi olarak düşünebilirsiniz. Yukarıdaki bash satırında da, `cat` programının standart çıktısı için sonuc tanımlandı. Dolayısıyla 1 \(stdout\) değeri için bir adres belirtmiş olduk. Daha sonra 2 \(stderr\) değeri için adres belirtirken de "daha önce 1 için tanımladığım **adresi** kullan" demiş oluyoruz. Burada **adres** sözcüğüne karşılık gelmesi için de, C'deki pointerlardaki gösterim gibi **&** işareti kullanılmaktadır.

Burada **&** işaretini, bir programı arka planda çalıştırmak için sonuna koyduğumuzdaki kullanımıyla karıştırmamakta fayda var. Eğer ardından 1 veya 2 geliyorsa, doğrudan file desriptor için pointer görevi görmektedir.

Yukarıdaki işlemi de yine tersi olarak yapabilirdik. Yani önce standart hata yönlendirmesi yapıp, daha sonra standart çıktının yönleneceği yerin, standart hatanın adresi olmasını da söyleyebilirdik.

```
eaydin@eaydin-vt ~/devel/lower $ cat karakterler paragraf 2> hatalisonuc 1>&2
eaydin@eaydin-vt ~/devel/lower $ cat hatalisonuc 
AbCdE
cat: paragraf: No such file or directory
```

### Çıktı Yönlendirme Örneği

Genellikle standart hatayı ve standart çıktıyı birlikte yönlendirme işini, kalabalık çıktı sunan bir programın arka planda çalışmasında kullanırız. Örneğin checkmate.py isimli geliştirdiğimiz bir Python programı, belirli dizinlerdeki dosyaları tarayıp içlerinde bozuk dosya olup olmadığını kontrol etmektedir. Bu programın çıktısı "dosyaları kontrol ettim, problem yok" veya "dosyaları kontrol ettim, problem var" şeklinde olabilir. Kontrol işlemini bitirince email ile uyarı göndermektedir. Bu programın oluşabilecek hatası ise, kontrol edeceği dizinlere erişememesi durumunda gerçekleşebilir. Öyleyse aşağıdaki gibi programı çalıştırırsak:

```
root@ubuntu:~# ./checkmate.py >> /var/log/checkmate.log 2>&1
```

Program çalışmaya başlayacak, ancak hem "kontrol ettim problem var/problem yok" gibi uyarılarını, hem de programın akışında yaşayacağı problemleri aynı dosyaya, `/var/log/checkmate.log` dosyasına yönlendirecektir. Ayrıca bu dosyada mevcut log satırlarını silmeyecektir, çünkü **&gt;&gt;** işaretiyle satırları sonuna eklemek istediğimizi belirttik.

Terminalimizde yeni bir işlem yapmak için, programın sonlanmasını beklememiz gerekir. Eğer bunu istemeseydik, sonuna bir **&** işareti daha eklememiz gerekecekti.

```
root@ubuntu:~# ./checkmate.py >> /var/log/checkmate.log 2>&1 &
```

Buradaki iki **&** işaretinin farklı görevler gördüğünün daha önce altını çizmiştik. `2>&1` gösterimindeki **&** işaret, standart hatanın, standart çıktının adresine yönlenmesini sağlamaktadır. Ardından gelen **&** işareti ise bütün işlemin arka planda yürütülmesini söyler.

Bazı durumlarda programlar arka planda çalışırken, standart hataları doğru yere yönlendirilmez. Aşağıdaki gibi bir kullanım buna karşılık gelir:

```
root@ubuntu:~# ./checkmate.py >> /var/log/checkmate.log &
```

Bu durumda, `checkmate.py` programı arka planda işini yapacak ve "kontrol ettim hata var/yok" bilgilerini yine doğru log dosyasına yazacaktır. Bu işlemler gerçekleştirilirken biz de aynı terminal ekranında standart işlerimize devam edebilir oluruz. Ancak eğer bu sırada `checkmate.py` programı bir hatayla karşılaşırsa, bunu terminal ekranına yazdıracaktır. Bazen terminal üzerinde çalışırken durduk yere bu tip mesajlarla karşılaşabilirsiniz. Bu durumlar çoğunlukla arka planda çalışan bir işlemin standart hatasının doğru yönlendirilmemesinde kaynaklanır.

Genellikle standart çıktıyı ve standart hatayı aynı yere yönlendirme işlemini `/dev/null` dosyası ile görebilirsiniz. İleride göreceğimiz özel dosyalardan birisi olan `/dev/null`, içine yazılan her şeyi silen özel bir dosyadır. Böylece, örneğin `checkmate.py` programımızın çalışmasını istiyorsak, ancak oluşturacağı çıktılarla hiç ilgilenmiyorsak, bütün çıktılarını `/dev/null` dosyasına yönlendirip, bu çıktıların sistemde tutulmamasını sağlayabiliriz.

```
root@ubuntu:~# ./checkmate.py > /dev/null/ 2>&1
```

Bu tip kullanıma, en çok \(yine ileride göreceğimiz\) zamanlanmış görevlerde rastlarız. `crontab` içine yazılan satırların çoğu, eğer loglanmasını istemediğimiz işlemler yapılıyorsa bu şekilde yazılır.

## Programların File Descriptor'ları

Aşağıdaki C kodunun derlendiği bir programı düşünelim.

```
#include <stdio.h>
#include <unistd.h>

int main() {
    while (1) {
        printf("Test\n");
        sleep(1);
    }
    return 0;
}
```

Eğer bu programı `deneme.c` olarak kaydedip aşağıdaki şekilde derler ve çalıştırırsak, her saniye ekrana `Test` yazmasını bekleriz.

```
eaydin@eaydin-vt ~/devel/sleep-test $ gcc deneme.c -o deneme
eaydin@eaydin-vt ~/devel/sleep-test $ ./deneme
Test
Test
Test
Test
```

Yani aslında program standart çıktıya Test yazıyor. Şimdi bu programın Linux üzerindeki process ID'sini \(PID\) öğrenelim. \(Bunu program çalışırken yapıyoruz\)

```
eaydin@eaydin-vt ~ $ ps ax | grep deneme
18622 pts/1    S+     0:00 ./deneme
18650 pts/2    S+     0:00 grep --color=auto deneme
```

Programın sistem üzerindeki PID'si 18622'ymiş. İşletim sisteminin çekirdeği tarafından bu işlemciye ayrılan file descriptorları, /proc dizini altında görebiliriz \(Yine, program hala çalışıyorken yapıyoruz bu işlemleri\).

```
eaydin@eaydin-vt ~ $ ls /proc/18622/fd/
0  1  2
```

Buradaki notasyona ve sonuçlarına dikkat edecek olursak, `/proc` isminde özel bir dizine baktık. Bu dizin Linux çekirdeğiyle ilgili işlemleri tutuyor. Bunun altında hangi PID'li işleme bakacaksak, onun için açılan dizine girdik. Onun içinde de File Descriptor'ın kısaltmasını temsil eden `fd` dizinine baktık. Burada 3 tane file descriptor ile karşılaştık. Her program çalıştırıldığında işletim sistemi çekirdeğinin öntanımlı olarak atadığı standart file descriptorlar. Aslında burada gördüğümüz üç dosya, birer sembolik link, daha detaylı bakacak olursak:

```
eaydin@eaydin-vt ~ $ ls -l /proc/18622/fd/
total 0
lrwx------ 1 eaydin eaydin 64 Mar 22 14:36 0 -> /dev/pts/1
lrwx------ 1 eaydin eaydin 64 Mar 22 14:36 1 -> /dev/pts/1
lrwx------ 1 eaydin eaydin 64 Mar 22 14:34 2 -> /dev/pts/1
```

Buradan görüleceği üzere, aslında programın standart girdisi, standart çıktısı, standart hatası aynı noktaya işaret ediyor, terminal ekranımıza.

Şimdi programımızı durdurup, tekrar çalıştırırken standart çıktısını bir dosyaya yönlendirelim.

```
eaydin@eaydin-vt ~/devel/sleep-test $ ./deneme > cikti
```

Yeni PID'yi öğrenip, file descriptorlarına baktığımızda durum aşağıdaki gibi oluyor:

```
eaydin@eaydin-vt ~ $ ps ax | grep deneme
18895 pts/1    S+     0:00 ./deneme
18899 pts/2    S+     0:00 grep --color=auto deneme
eaydin@eaydin-vt ~ $ ls -l /proc/18895/fd
total 0
lrwx------ 1 eaydin eaydin 64 Mar 22 14:41 0 -> /dev/pts/1
l-wx------ 1 eaydin eaydin 64 Mar 22 14:41 1 -> /home/eaydin/devel/sleep-test/cikti
lrwx------ 1 eaydin eaydin 64 Mar 22 14:40 2 -> /dev/pts/1
```

Standart girdi hala terminal ekranı, standart hata da öyle, ancak standart çıktıyı yönlendirdiğimiz dosyayı burada görebiliyoruz. Öyleyse sistem üzerinde çalışan programların hangi dosyalara eriştiğini, dolayısıyla açık file descriptor'larını bu şekilde öğrenebiliyoruz.

## Standart Olmayan File Descriptor'lar

Bu bölüm boyunca, programların üç tane file descriptor'ından bahsettik. Standart girdi, standart çıktı, standart hata. Aslında Dennis Ritchie standart hata'yı UNIX'in 6. versiyonu üzerinde tanımladığında tam olarak şunu yaptı: Bir program çalışmaya başladığında, programın _ön tanımlı_ 3 tane file descriptor'ı olsun. Bu yüzden bunlara _standart_ diyoruz aslında. Çünkü programlar -neredeyse- her zaman bu üç tip file descriptor'a ihtiyaç duyuyorlar. Ancak bir programın 3'ten fazla file descriptor'ı olabilir.

Daha önce herhangi bir C programının `getchar` veya `putchar` gibi fonksiyonlar ile standart girdi ve standart çıktıyı kontrol ettiğini gördük. Ancak C programı bu işleri yaparken, bir yandan bir dosyayı açıp üzerinde işlem yapmasını sağlayabiliriz. Bu dosya söz konusu standart çıktı olmak zorunda değil. Örneğin biraz önce yazdığımız deneme programı ekrana \(standart çıktıya\) Test yazdırırken, bir yandan yaz.txt isimli bir dosya açıp içinde işlemler yapabilir. Aşağıdaki kodu deneme2.c olarak yazıp derlersek bu durumu irdeleyebiliriz.

```
#include <stdio.h>
#include <unistd.h>

int main() {

    FILE *fp;
    fp = fopen("yaz.txt", "w");

    while (1) {
        printf("Test\n");
        sleep(1);
    }
    return 0;
}
```

Programı derleyip yine PID'sini öğrenip file descriptor'larına bakalım:

```
eaydin@eaydin-vt ~/devel/sleep-test $ ls -l /proc/22447/fd
total 0
lrwx------ 1 eaydin eaydin 64 Mar 22 15:45 0 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 15:45 1 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 15:45 2 -> /dev/pts/2
l-wx------ 1 eaydin eaydin 64 Mar 22 15:45 3 -> /home/eaydin/devel/sleep-test/yaz.txt
```

Burada daha önce görmediğimiz, yeni bir file descriptor açığa çıktı. Standart 0, 1 ve 2 dışında bir de 3 numaralı file descriptor. Kod içerisinde `yaz.txt` dosyasını açmasını söyledik, işletim sistemi de programın çalıştığı dizin altında `yaz.txt` diye bir dosya oluşturup bunu programın 3 numaralı file descriptor'ı ile eşleştirdi.

Eğer programımızı yaz.txt içerisine de satırlar yazıp kaydedecek şekilde düzenlersek, burada da bir takım sonuçlar görmeyi bekleriz. Örneğin kodumuz aşağıdaki gibi olsaydı:

```
#include <stdio.h>
#include <unistd.h>

int main() {

    FILE *fp;
    fp = fopen("yaz.txt", "w");

    int i;

    for (i=0;i<10;i++) {
        printf("Test\n");
        fprintf(fp, "Dosyaya yazdırma\n");
        sleep(1);
    }
    fclose(fp);
    return 0;
}
```

Bu durumda ekrana \(standart çıktıya\) Test yazarken, yaz.txt içerisine Dosyaya yazdırma yazmasını beklerdik. Bunu 10 kere yapıp \(her birini bir saniye arayla\) sonra da program sonlanırdı. Bu durumda da yine yaz.txt'nin file descriptor'lar arasında 3 numaraya sahip olacağını düşünmek normal. Peki programı çalıştırırken, 3 numaralı file descriptor'ı yeni bir dosyaya yönlendirirsek? yaz.txt içerisine yazılacak verinin başka yere yazılmasını mı bekleriz?

```
eaydin@eaydin-vt ~/devel/sleep-test $ ./deneme3 3>yeni.txt
Test
Test
Test
```

Bu durumda kodumuzun 3. çıktısını yeni.txt'ye yönlendiriyoruz. Hemen sistemde açık file descriptor'larına bakalım.

```
eaydin@eaydin-vt ~/devel/sleep-test $ ls -l /proc/23705/fd
total 0
lrwx------ 1 eaydin eaydin 64 Mar 22 15:59 0 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 15:59 1 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 15:59 2 -> /dev/pts/2
l-wx------ 1 eaydin eaydin 64 Mar 22 15:59 3 -> /home/eaydin/devel/sleep-test/yeni.txt
l-wx------ 1 eaydin eaydin 64 Mar 22 15:59 4 -> /home/eaydin/devel/sleep-test/yaz.txt
```

Beklediğimiz sonucu vermedi. Sistem 3. file descriptor'ı terminalde belirttiğimiz gibi `yeni.txt` dosyasına yönlendirdi, program içerisinde açtığımız `yaz.txt` ise kendine yeni bir numara edinerek 4. file descriptor oldu.

İşletim sisteminin çekirdeği şu şekilde davranıyor: "Bu program ile ilgili bir dosya işlemi talep edildiğinde, eğer bana file descriptor adresi \(numarası\) verilmemişse, ben sıradan uygun ilk rakamı tahsis edeyim."

Öyleyse kodumuz içerisinde özellikle `yaz.txt` yolunu belirtmek yerine, file descriptora yazmasını belirtebilirdik.

```
#include <stdio.h>
#include <unistd.h>

int main() {

    int i;

    for (i=0;i<10;i++) {
        printf("Test\n");
        write(3, "Dosyaya yazdırma\n", 18);
        sleep(1);
    }
    return 0;
}
```

Programı derleyip çalıştırdığımızda, standart çıktıya sadece Test yazdığını görüyoruz.

```
eaydin@eaydin-vt ~/devel/sleep-test $ ./deneme4 3>yeni.txt
Test
Test
Test
Test
Test
Test
Test
Test
Test
Test
```

Öte yandan, 3. file descriptor'ını yeni.txt'ye yönlendirmiştik. Kodun içerisinde hiçbir yerde dosya ismi belirtmedik, ancak kodda 3. file descriptor'a bir yazma işlemi söz konusu. PID'den açık file descriptor'lara bakalım.

```
eaydin@eaydin-vt ~/devel/sleep-test $ ls -l /proc/24419/fd
total 0
lrwx------ 1 eaydin eaydin 64 Mar 22 16:18 0 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 16:18 1 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 16:18 2 -> /dev/pts/2
l-wx------ 1 eaydin eaydin 64 Mar 22 16:18 3 -> /home/eaydin/devel/sleep-test/yeni.txt
```

Öyleyse yeni.txt içerisinde beklediğimiz satılar olmalı.

```
eaydin@eaydin-vt ~/devel/sleep-test $ cat yeni.txt 
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
```

İşin ilginç tarafı, programımızı çalıştırırken 3. numaralı file descriptor'ı hiçbir yere yönlendirmeseydik, sistem üzerinde file descriptor oluşmayacaktı bile.

```
eaydin@eaydin-vt ~/devel/sleep-test $ ./deneme4
Test
Test
Test
Test
Test
Test
Test
Test
Test
Test
```

Bu durumda açık file descriptor'lara bakacak olursak:

```
eaydin@eaydin-vt ~/devel/sleep-test $ ls -l /proc/24890/fd
total 0
lrwx------ 1 eaydin eaydin 64 Mar 22 16:29 0 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 16:29 1 -> /dev/pts/2
lrwx------ 1 eaydin eaydin 64 Mar 22 16:29 2 -> /dev/pts/2
```

Çünkü ne programın içinde, ne de programı çalıştırırken 3. file descriptor'ın nereye işaret edeceğini söylemedik.

Her ne kadar ilk üç file descriptor standart olarak belirlenmiş olsa da, programın kodlarında ilgili descriptorları kapatıp yeniden bir dosya açtığımızda işletim sistemi çekirdeğinin bu descriptor'ları kullandığını gözlemek mümkün. Yani 1 numaralı file descriptor'ı C kodundan kapatırsak, sonra C kodu içerisinde yeni bir dosyaya erişim sağlayacak olursak, işletim sistemi ilk uygun boş sayı 1 olacağı için bu değeri kullanacaktır.

### Alternatif File Descriptor'ın Pipeline'da Kullanımı

Programları birbirlerine pipeline ile bağlarken, eğer bir programın alternatif file descriptorlarından birini kullanmak istiyorsak biraz dolambaçlı bir yol izlemek gerekecektir. Örneğin biraz önceki `deneme4` programımızın 3. file descriptor çıktısını `grep`'e göndermek istiyorsak, ve sadece 3. file descriptor'ı ile ilgileniyorsak, programın 1. descriptor'ı ile 3. descriptor'ını yer değiştirebiliriz. Böylece programın sanki standart çıktısından bu bilgiler çıkıyormuş gibi davranır, pipe ise bunu olduğu gibi diğer programa taşır.

```
eaydin@eaydin-vt ~/devel/sleep-test $ ./deneme4 3>&1 | grep Dos
Dosyaya yazdırma
Dosyaya yazdırma
Dosyaya yazdırma
^C
```

Ancak bu örnekte standart çıktıya yazılan `Test` satırlarını kaybettik. Eğer hem 3. hem de 1. descriptor'ın sonuçlarıyla ilgileniyorsak, process substitution tekniğini kullanabiliriz.

```
eaydin@eaydin-vt ~/devel/sleep-test $ ./deneme4 3> >(grep Dos)
Test
Dosyaya yazdırma
Test
Dosyaya yazdırma
Test
Dosyaya yazdırma
^C
```

Burada dikkat edilmesi gereken nokta, standart çıktının verilerini `grep` programına yollamadık, dolayısıyla ekranda gördüğümüz `Test` satırları `grep`'in işlemlerinden geçmedi, sadece `Dosyaya yazdırma` satırları buradan geçti.

Eğer birden fazla file descriptor'ın birden fazla programa çeşitli yollarda gönderilmesini istiyorsak, en pratik çözüm bir sonraki bölümde göreceğimiz **named pipe** kullanımı olacaktır.

## Open File Descriptor Limiti'ne Dönüş

Daha önce işletim sisteminin aklında tuttuğu file decriptor sayısına bir limit getirdiğini, üstelik program başına da bir limit getirdiğini görmüştük. Daha önce incelediğimiz örnekte bunun bir PID başına 1024 tane olduğuyla karşılaşmıştık. Bir programın neden 3'ten fazla file descriptor'a ihtiyaç duyabileceği, alternatif file descriptor'lar ile tanıştığımızda netleşmiştir herhalde.

Örneğin bir sunucu üzerinde çalışan MySQL Veritabanı servisinin açık file descriptor'larından bir kesit aşağıda görülebilir:

```
[root@emre ~]# ls -l /proc/6274/fd
total 0
lr-x------ 1 root root 64 Mar 20 18:26 0 -> /dev/null
l-wx------ 1 root root 64 Mar 20 18:26 1 -> /var/log/mysqld.log
lrwx------ 1 root root 64 Mar 20 18:26 10 -> socket:[187781]
lrwx------ 1 root root 64 Mar 20 18:26 11 -> /tmp/ibnc02LB (deleted)
lrwx------ 1 root root 64 Mar 20 18:26 12 -> socket:[187782]
lrwx------ 1 root root 64 Mar 20 18:26 13 -> /var/lib/mysql/mysql/host.MYI
lrwx------ 1 root root 64 Mar 20 18:26 14 -> /var/lib/mysql/mysql/host.MYD
lrwx------ 1 root root 64 Mar 20 18:26 15 -> /var/lib/mysql/mysql/user.MYI
lrwx------ 1 root root 64 Mar 20 18:26 16 -> /var/lib/mysql/mysql/user.MYD
lrwx------ 1 root root 64 Mar 20 18:26 17 -> /var/lib/mysql/mysql/db.MYI
lrwx------ 1 root root 64 Mar 20 18:26 18 -> /var/lib/mysql/mysql/db.MYD
lrwx------ 1 root root 64 Mar 20 18:26 19 -> /var/lib/mysql/mysql/tables_priv.MYI
l-wx------ 1 root root 64 Mar 20 18:26 2 -> /var/log/mysqld.log
lrwx------ 1 root root 64 Mar 20 18:26 20 -> /var/lib/mysql/mysql/tables_priv.MYD
lrwx------ 1 root root 64 Mar 20 18:26 21 -> /var/lib/mysql/mysql/columns_priv.MYI
lrwx------ 1 root root 64 Mar 20 18:26 22 -> /var/lib/mysql/mysql/columns_priv.MYD
...
lrwx------ 1 root root 64 Mar 20 18:26 25 -> /var/lib/mysql/mysql/servers.MYI
lrwx------ 1 root root 64 Mar 20 18:26 26 -> /var/lib/mysql/mysql/servers.MYD
lrwx------ 1 root root 64 Mar 20 18:26 27 -> /var/lib/mysql/mysql/event.MYI
lrwx------ 1 root root 64 Mar 20 18:26 28 -> /var/lib/mysql/mysql/event.MYD
lrwx------ 1 root root 64 Mar 20 18:26 3 -> /var/lib/mysql/ibdata1
lrwx------ 1 root root 64 Mar 20 18:26 30 -> /var/lib/mysql/mysql/func.MYI
lrwx------ 1 root root 64 Mar 20 18:26 31 -> /var/lib/mysql/mysql/func.MYD
...
lrwx------ 1 root root 64 Mar 20 18:26 37 -> /var/lib/mysql/mysql/help_keyword.MYD
lrwx------ 1 root root 64 Mar 20 18:26 38 -> /var/lib/mysql/mysql/help_relation.MYI
lrwx------ 1 root root 64 Mar 20 18:26 39 -> /var/lib/mysql/mysql/help_relation.MYD
lrwx------ 1 root root 64 Mar 20 18:26 4 -> /tmp/ibGquxvh (deleted)
lrwx------ 1 root root 64 Mar 20 18:26 40 -> /var/lib/mysql/mysql/help_topic.MYI
...
lrwx------ 1 root root 64 Mar 20 18:26 46 -> /var/lib/mysql/mysql/proc.MYI
lrwx------ 1 root root 64 Mar 20 18:26 47 -> /var/lib/mysql/mysql/proc.MYD
lrwx------ 1 root root 64 Mar 20 18:26 48 -> /var/lib/mysql/mysql/slow_log.CSM
lr-x------ 1 root root 64 Mar 20 18:26 49 -> /var/lib/mysql/mysql/slow_log.CSV
lrwx------ 1 root root 64 Mar 20 18:26 5 -> /tmp/ibGbuH88 (deleted)
lrwx------ 1 root root 64 Mar 20 18:26 50 -> /var/lib/mysql/mysql/time_zone.MYI
lrwx------ 1 root root 64 Mar 20 18:26 51 -> /var/lib/mysql/mysql/time_zone.MYD
lrwx------ 1 root root 64 Mar 20 18:26 52 -> /var/lib/mysql/mysql/time_zone_leap_second.MYI
lrwx------ 1 root root 64 Mar 20 18:26 53 -> /var/lib/mysql/mysql/time_zone_leap_second.MYD
...
```

Çok uzun olduğu için çıktıyı keserek paylaşmak durumunda kaldık. Sadece bu işlemin 86 tane açık file descriptor'ı mevcut ancak çok yoğun veritabanı kullanımlarında bu sayı çok daha yüksek \(binlerce\) olabiliyor. Yukarıdaki örnekten MySQL'in log dosyalarına, yardım dosyalarına ve time zone dosyalarına erişim sağlaması söz konusu. Hatta `/tmp` dizini altında kullandığı bazı geçici dosyalar silinmiş bile. Böyle bir durumda çok fazla file descriptor harcayabilir, tek başına işletim sisteminin diğer programlara file descriptor ayırabilmesi olanaksız hale gelebilir. Bu durumun önüne geçmek için kernel parametrelerinde çeşitli sınırlandırmalar sağlanır. Bu sınırlandırmalar sistem genelinde olabileceği gibi, kullanıcı bazlı da değişebilir.

Limitler iki çeşittir. Hardlimit ve Softlimit. Hardlimit sadece root tarafından düzenlenebilecek limitlerdir. Softlimit ise bir kullanıcının kendi düzenleyebileceği limittir.

Örneğin biraz önceki sunucuda mysql kullanıcısının limitlerini öğrenmek için, bu kullanıcı ile login olduktan sonra aşağıdaki komutları uygulayabiliriz.

```
[root@emre ~]# su - mysql
-bash-4.1$ ulimit -Hn
4096
-bash-4.1$ ulimit -Sn
1024
```

Demek ki bu sunucu üzerinde `mysql` kullanıcısının Hardlimit'i 4096, Softlimit'i 1024. Bu şu anlama gelir, bir program `mysql` kullanıcısı olarak çalıştırıldığında, aynı anda 1024'ten fazla file descriptor oluşturamaz. Eğer 1024'ten fazlasına ihtiyaç duyarsa, bu sayıyı kendisi artırabilir, ancak asla Hardlimit olan 4096 sayısını geçemez. Bu sayıyı geçmesi için root yetkisi olan yöneticisinden izin alması gerekir ve ancak root kullanıcısı Hardlimit değerini düzenleyebilir.

Oluşabilecek bir yanlış anlaşılmayı gidermek adına not edelim, bu limitler kullanıcı bazlı olsa bile, kullanıcıların bütün programları için toplanarak giden bir değer değildir. Kullanıcının her bir programı bu limitler dahilinde davranabilir. Yani Hardlimit'i 4096 olan bir kullanıcının iki farklı programı ayrı ayrı 4000'er dosya açabilir.



