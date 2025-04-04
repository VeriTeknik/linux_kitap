# wget\*

Bu kitapçıkta daha önce de karşılaştığımız komutlardan **wget**, internetten dosya ve web sayfalarını indirmek için kullanılan güçlü ve esnek bir komut satırı aracıdır. İsmi **Web GET** sözcüklerinin kısaltmasıdır. Özellikle toplu indirmeler, yarım kalan indirmeleri devam ettirme ve web sitelerini yansıtma (mirroring) gibi işlemler için kullanışlıdır.

**`wget` vs `curl`:**
`wget` ile benzer işlevleri gören bir diğer popüler araç `curl`'dür. İkisi de URL'lerden veri aktarımı yapar ancak farklı kullanım amaçları ve felsefeleri vardır:
*   **`wget`:** Genellikle dosyaları indirmek ve web sitelerini özyinelemeli (recursive) olarak indirmek için tasarlanmıştır. Varsayılan olarak dosyaları diske kaydeder.
*   **`curl`:** Daha çok tek bir URL ile etkileşim kurmak, veriyi standart çıktıya (stdout) yazdırmak (bu da pipe ile başka komutlara bağlanmasını kolaylaştırır) ve çok çeşitli protokolleri (HTTP, HTTPS, FTP, SCP, LDAP, SMTP vb.) desteklemek üzerine odaklanmıştır. API testleri ve betiklerde sıkça kullanılır.

Her iki aracın da kendine özgü güçlü yanları vardır ve genellikle sistem yöneticileri her ikisini de kullanır. Bu bölümde `wget`'e odaklanacağız.

İnternetten herhangi bir dosyayı indirmek için dosya adresini wget'e parametre olarak vermek yeterlidir.

```bash
wget http://www.plugged.in/downloads/plugged.sh
```

Yukarıdaki komutla bulunduğumuz dizine `plugged.sh` dosyası inecektir.

Bazen linkler istediğimiz isim yapısında olmayabilir. İndirilen dosyayı farklı isimle kaydetmek için aşağıdaki yöntem izlenebilir.

```bash
wget http://www.plugged.in/downloads/plugged.sh -O install.sh
```

Artık dosya `plugged.sh` ismiyle değil, `install.sh` ismiyle kaydedilecektir.

## Yarım Kalan İndirmeler

Normalde `wget` programı, indirilen dosya hali hazırda dizinde mevcutsa, yeni indirilen dosyayı sonuna **.1 .2** gibi rakamlar koyarak yazar. Örneğin yukarıdaki örnekte **plugged.sh** dosyası zaten dizinde mevcutsa, yeniden indirdiğimizde `wget` dosyayı **plugged.sh.1** olarak kaydeder.

Oysa indirme sırasında dosya yarım kalmışsa, kaldığı yerden devam ettirmek için **-c** parametresini vermemiz yeterlidir.

```bash
wget -c http://www.plugged.in/downloads/plugged.sh
```

## Hız Limitleme

İndirme hızını limitlemek için `--limit-rate` parametresi kullanılır.

```bash
wget --limit-rate=400k http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
```

## Deneme Sayısı

Bazı durumlarda indirme işlemini kaç kez deneyeceğinizi belirtmek isteyebilirsiniz.

```bash
wget --tries=10 http://plugged.in/downloads/plugged.sh
```

## İndirme İşlemini Arka Planda Gerçekleştirme

Büyük dosyaları indirme işlemini arka planda yürütebilirsiniz. Böyle bir durumda `wget` size programın PID numarasını verecek, ve logları yazdığı dosyayı bildirecekir. Bu sayede isterseniz indirme işlemini log dosyasından takip edebilir, dilediğiniz zaman PID numarasını kullanarak programı durdurabilirsiniz.

```bash
# wget -b http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
Continuing in background, pid 25965.
Output will be written to ‘wget-log’.
```

## Liste Kullanımı

İndirilecek dosyaların bir listesini bir program ile oluşturabilirsiniz, veya yükleme yazılımınızda kullanmak üzere hazırlayabilirsiniz. Bu listedeki linkleri sırayla indirmesi için **wget** programına parametre olarak verebilirsiniz.

```bash
eaydin@dixon ~/calisma/wget $ cat indir.txt
```

```
http://plugged.in/downloads/plugged.sh
http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
eaydin@dixon ~/calisma/wget $ wget -i indir.txt 
--2015-11-30 00:42:03--  http://plugged.in/downloads/plugged.sh
Resolving plugged.in (plugged.in)... 94.103.33.130, 2a00:7300:1::fff5
Connecting to plugged.in (plugged.in)|94.103.33.130|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8990 (8,8K) [application/x-sh]
Saving to: ‘plugged.sh’

100%[===============================================================================================>] 8.990       --.-K/s   in 0,02s   

2015-11-30 00:42:05 (422 KB/s) - ‘plugged.sh’ saved [8990/8990]

--2015-11-30 00:42:05--  http://mirror.rackdc.com/CentOS/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso
Resolving mirror.rackdc.com (mirror.rackdc.com)... 94.103.32.100, 2a00:7300:1::100
Connecting to mirror.rackdc.com (mirror.rackdc.com)|94.103.32.100|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 666894336 (636M) [application/x-iso9660-image]
Saving to: ‘CentOS-7-x86_64-Minimal-1503-01.iso’

 0% [                                                                                                ] 531.035      296KB/s
```

## İndirme Kontrolü

Bazı durumlarda indirme işleminden önce indirmenin mümkün olup olmadığını kontrol edebilirsiniz. Bunun için `--spider` seçeneği kullanılabilir. Bu seçeneğin en yaygın kullanımı, sisteminizin internet erişiminin olup olmadığı, internet erişimi varsa da DNS çözümlemelerini doğru yapıp yapmadığınız konusundadır. Aşağıdaki örnekleri inceleyelim.

```bash
eaydin@dixon ~/calisma/wget $ wget --spider google.com
```

```
Spider mode enabled. Check if remote file exists.
--2015-11-30 00:26:28--  http://google.com/
Resolving google.com (google.com)... 216.58.209.14, 2a00:1450:4017:803::200e
Connecting to google.com (google.com)|216.58.209.14|:80... connected.
HTTP request sent, awaiting response... 302 Found
Location: http://www.google.com.tr/?gfe_rd=cr&ei=lHtbVtjnGcuz8wfWmI6IAw [following]
Spider mode enabled. Check if remote file exists.
--2015-11-30 00:26:28--  http://www.google.com.tr/?gfe_rd=cr&ei=lHtbVtjnGcuz8wfWmI6IAw
Resolving www.google.com.tr (www.google.com.tr)... 216.58.208.99, 2a00:1450:4017:803::2003
Connecting to www.google.com.tr (www.google.com.tr)|216.58.208.99|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Remote file exists and could contain further links,
but recursion is disabled -- not retrieving.
```

```bash
eaydin@dixon ~/calisma/wget $ echo $?
0
```

```bash
eaydin@dixon ~/calisma/wget $ wget --spider google.c
```

```
Spider mode enabled. Check if remote file exists.
--2015-11-30 00:26:52--  http://google.c/
Resolving google.c (google.c)... failed: Name or service not known.
wget: unable to resolve host address ‘google.c’
```

```bash
eaydin@dixon ~/calisma/wget $ echo $?
4
```

Gördüğünüz gibi **google.com** için **exit status 0** aldık, oysa **google.c** için **exit status 4** döndü. Bu sayede scriptlerimize bir internet kontrol mekanizması koyabiliriz.

En yaygın kullanım, bu seçeneği `-q` \(quiet\) seçeneği ile kullanmaktır.

```bash
eaydin@dixon ~/calisma/wget $ wget -q --spider google.com
eaydin@dixon ~/calisma/wget $ echo $?
0
```

**NOT:** Yukarıdaki ifadelerde **$?** gösteriminin ne anlama geldiğini bilmiyorsanız, kitabın Bash Programlama bölümüne bakabilirsiniz.

## Tarayıcı Gibi Davranmak

İndirmek istediğiniz bazı linkler kaynağın bir tarayıcı gibi davranmasını isterler.  
Aşağıda bu tip senaryolarda kullanabileceğiniz iki yol gösterici örnek görebilrsiniz.

```bash
# wget --user-agent="Mozilla/5.0 (X11; U; Linux i686; \ 
en-US; rv:1.9.0.3) Gecko/2008092416 \
Firefox/3.0.3" http://indirilecek-link
```

Veya cookie kullanılarak login yapılabilir.

```bash
# wget --save-cookies cookies.txt \
--post-data 'user=kullanici&password=sifre' \
http://sayfa.com/login.php

# wget --load-cookies cookies.txt http://sayfa.com/indir.tar.gz
```

## Sayfayı Tamamen İndirmek

```bash
wget --mirror -p --convert-links -P indirme_dizini http://plugged.in
```

Yukarıdaki komut [plugged.in](http://plugged.in) sayfasını `indirme_dizini` dizinine indirecektir. `--mirror` seçeneği, bütün dizinleri indirmesi gerektiğini belirtir. `-p` ile HTML'in doğru görüntülenmesi için gerekli dosyalar \(örn. CSS dosyaları\) beraberinde gelir. `--convert-links` HTML dosyaları içerisindeki linkleri yerel linkler ile değiştirir. Bu işlem sadece indirme tamamlanınca gerçekleşir, yani indirme işlemini yarıda keserseniz linkleri düzenlemez.

Yukarıdaki komutun açık hali şöyledir

```bash
wget --mirror --page-requisites --convert-links \
--directory-prefix=indirme_dizini http://plugged.in
```

Okuduğunuz kitabın HTML halini indirmek isterseniz aşağıdaki komutu kullanabilirsiniz.

```bash
wget --mirror --page-requisites --convert-links \
--directory-prefix=kitap \
https://veriteknik.gitbooks.io/linux-yonetimi/content/
```

## Belirli Dosya Tiplerini ve Dizin Oluşumunu Engellemek

Aşağıdaki örnek, rackdc CentOS mirror'undan iso dosyaları hariç \(`--reject`\) tüm dosyaları indirecek, ancak "Parent Directory"e gidip tarama işlemini genişletmeyecek \(`-np`\) ve tamamını tek dizine indirip alt dizinleri oluşturmayacaktır.

```bash
wget --reject=iso -P CentOS --mirror -np \ 
--no-directories http://mirror.rackdc.com/CentOS/7/isos/x86_64/
```

## Sadece Belirli Dosyaları İndirmek

Benzer şekilde sadece belirli tipteki dosyaları indirebilirsiniz.

```bash
wget -A.torrent -P torrent-files --mirror \
-np --no-directories \
http://mirror.rackdc.com/CentOS/7/isos/x86_64/
```

## FTP Kullanımı

Son olarak wget ile anonim ve şifreli FTP kullanımı göreceğiz.

```bash
# wget ftp-sitesi.com
# wget --ftp-user=KULLANICI --ftp-password=SIFRE ftp-sitesi.com
```

\*FreeBSD Sisteminde wget yerine fetch kullanılmaktadır.
