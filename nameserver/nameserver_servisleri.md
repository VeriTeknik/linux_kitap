# Alanadı Servisleri

Alanadı servisleri otoritiv \(yetkili\) ve caching \(önbellek\) olarak ikiye ayrılmaktadır, istemcilerde DNS sekmesine yazılan alan adlarına karşılık IP adreslerini alan caching türündeki alanadı sunucusudur, yani yetkili DNS sunucuları ile görüşen sunuculardır. Yetkili DNS sunucuları ise sadece hizmet ettikleri alan adlarının kayıtlarını dışarıya vermekle yükümlüdür. DNS sunucularının birden fazla olması temel prensiptir ve bu sunucuların prensipte farklı veri merkezlerinde hatta farklı coğrafyalarda olması tercih edilir. Ancak imkanlarınız dahilinde aynı sunucu üzerine hem birincil \(master\) hem de ikincil \(slave\) sunucuyu kurabilirsiniz. DNS sunucuları doğru ayarlandığında birbirlerini güncelleyebilir, bu işlemin düzgün çalışması için her bir alan adı kaydında "seri numarası" kullanılır, master sunucuda yapacağınız bir değişiklik sonrası seri numarası üzerinde yapacağınız artırım işlemi diğer bütün slave sunucuların "notify" edilmesini sağlar. Seri numaralar aşağıdaki standarda göre verilir

```
YILAYGUNSAYI
```

Örneğin 2018 yılının Mart ayının 24.de yapıacak ikinci değişiklik şu şekilde belirtilmelidir:

```
2018032402
```

Bu yapılan işlem hem Bind DNS hem de Power DNS için geçerlidir. DNS Sunucusundaki her bir DNS kaydı SOA \(State of Authority\) kaydı içermelidir, bu kayıttan sonra alan adı sunucu bilgileri \(NS\) ve diğer kayıt tipleri girilir. Aşağıdaki tabloda en sık kullanılan kayıt tipleri belirtilmiştir:

| Kayıt Tipi | Örnek Değer | İfade | Kullanım Amacı |
| :--- | :--- | :--- | :--- |
| NS | rackdc.com. | ns1.rackdc.com. | Alan adı sunucusunun ismi |
| A | rackdc.com. | 192.168.0.2 | isime karşılık gelen IP adresi |
| AAAA | rackdc.com. | 2a00:7300:100::2 | isime karşılık gelen IPv6 adresi |
| CNAME | www | rackdc.com. | Takma isim |
| MX | rackdc.com. | 5 mail.rackdc.com | Posta Sunucuları |
| TXT | rackdc.com. | "düz metin ifade" | Açıklamalar ve kurallar |

Sırasıyla,

NS: rackdc.com için yetkili alan adı sunucusunun ns1.rackdc.com olduğunu ifade eder  
A/AAAA: rackdc.com sorgulandığında karşılık gelen IP adresi gönderilir  
CNAME: Bir alan adına takma ad olarak kullanılır, bu yapı DNS sunucusunda iki defa sorgu atılmasına neden olduğu için pratikte kullanılmaz,  
MX: alan adına bağlı olan E-Posta sunucusunun bilgisini içerir, bu kısıma IP adresi yerine alan adı yazılmalıdır  
TXT: Genelde anti-spam sistemleri tarafından belirlenmiş olan ifadeler ya da alan adı sahipliğini doğrulayan metinler içerebilir.

Not: alan adlarının sonunda "." işareti varsa ifade named tarafından tamamlanmaz, nokta koymadığınız durumlarda named bu alanı tamamlayacaktır, örneğin www yazdığınızda named bunu www.rackdc.com olarak algılayacaktır.

### Bind Alan Adı Sunucusu Kurulumu

Bind DNS sunucusu kullandığınız GNU/Linux sürümünün standart reposunda gelmektedir.Yüklemek için "named" paketini tercih ettiğiniz paket yöneticisi ile indirip kurabilirsiniz.Kurulum tamamlandıktan sonra başlatma betikleri ve ayar dosyaları sisteminize yüklenecektir, named standart yüklemede ayar dosyasını /etc dizinine koyar, /etc/named.conf dosyasında genel ayarlamalarınızı yapabilirsiniz, oluşturacağınız alan adlarının bilgileri de /var/named dizininde saklanır. Loglar ile ilgili özel bir düzenleme yapmazsanız, hata loglarını messages içerisinden inceleyebilirsiniz.

#### Yetkili Alan Adı Sunucusu Kurulumu

Yetkili alan adı sunucusu kurulumu named.conf dosyasının düzenlenmesi ile oluşturulur. named.conf içerisindeki ayarlara kısaca göz atalım:

```
acl "genelsorgu" {
        localhost;
    94.103.32.0/20;
        185.35.20.0/22;
    185.96.170.0/24;
        192.168.15.0/24;
    2a00:7300::1/32;
};
```

acl, access-list İngilizce teriminin kısaltmasıdır, bu acl ile DNS sunucusunda "recursion" yani dış sorgu yapabilecek IP bloklarının bilgisini yazıyoruz, yetkili DNS sunucusu kendi bünyesinde hizmet verdiği alan adlarının bilgileri dışında harici alan adlarını da sorgulayabilir durumdadır, yukarıda belirtilmiş olan IP blokları bu hizmetten faydalanabilecektir, bu IP grupları dışında harici alan adlarını sorgulamaya kalkan IP adreslerine cevap dönmeyecektir. Bu yapılandırmayı test etmek için belirtilen IP bloklarından birinden dig komutu ile sorgulama yapabilirsiniz

```
dig google.com @DNS_SUNUCU_IP_ADRESI
```

Bu sorguyu yaptığınızda harici alan adının IP adresini görebilmeniz gerekir. acl kısmından sonra options ise şu ifadeleri içerir:

```
options {
    version "Not disclosed";
    listen-on port 53 { 127.0.0.1;IP_ADRESI_1;IP_ADRESI_2;};
    listen-on-v6 port 53 { any; };
    directory     "/var/named";
    allow-transfer { none; };
    allow-recursion { genelsorgu; };
    allow-query-cache { genelsorgu; };
    recursion yes;

    /* Path to ISC DLV key */
    bindkeys-file "/etc/named.iscdlv.key";
};
```

options kısmı Bind DNS sunucusunun nasıl çalışacağını belirten ayarları içermektedir, sırasıyla inceleyelim:

version "Not Disclosed": Bind sunucusuna sorgu yapıldığında sunucu versiyonu da iletilir, güvenlik açısından versiyon bilgisi paylaşmak istemezseniz bu satırı eklemeniz gerekmektedir.

listen-on port 53 { 127.0.0.1;IP\_ADRESI\_1;IP\_ADRESI\_2;}; Bind sunucusunun dinleyeceği portu ve IP adreslerini içerir, her bir IP adresini ";" ile ayırmayı unutmayınız, sunucuyu hem master hem de slave olarak kullanacaksanız buraya en az iki IP adresi girmelisiniz.

listen-on-v6 port 53 { any; }; Aynı şekilde eğer kullanıyorsanız IPv6 adresleri için de bu ayarlamayı yapmanız gerekmektedir.

directory "/var/named"; Yetkili alan adlarının ayar dosyalarının bulunduğu dizin

allow-transfer { none; }; Bu kısıma slave sunucunun IP adresi yazılabilir, bu kısmı ayar dosyasına koymazsanız bu DNS sunucusunun içerdiği tüm verileri ALL sorgusu ile alabilirler, bu durumda XFER ataklarına açık halde olur, bu nedenle tavsiye etmiyoruz.

allow-recursion { genelsorgu }; bu kısıma recursion yani harici DNS verisi sorgulamasına izin verdiğiniz IP ya da subnetleri yazabilirsiniz. allow-query-cache ise ön bellekteki kayıtlara ulaşabilmek için yazılmalıdır.

recursion yes; recursion yapılmasını sağlar, eğer recursion yapılmasını istemiyorsanız bunu kapatınız, bu ayar kapalı olduğunda allow-recursion kısmı da etkili olmayacaktır.

```
controls {
    inet 127.0.0.1 allow { localhost; } keys { rndckey; };
};

logging {
    category lame-servers {null;};
    channel default_debug {
    file "data/named.run";
    severity dynamic;
//  severity debug;
    };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/rndc.key";
include "/etc/named.rfc1912.zones";
```

Yukarıdaki ayarlar ise standart named.conf'ta bulunması gereken ayarlardır, controls kısmında sistemin rndc ile yönetilebileceği ve bunun sadece localhost'tan olabileceği belirtilmektedir.

logging kısmı, named'in ne kadar detaylı log üreteceğini belirtmektedir, detaylı loglama istedeğiniz zaman severity dynamic kısmını kapatıp severity debug kısmını açabilirsiniz, "//" ile başlayan satırları named yorum olarak algılayacaktır.

Aşağıda açıklayacağımız kısım ise, DNS sunucusunun yetkili olduğu alan adlarının bilgisini içermektedir, aşağıda gösterildiği şekilde ekleyeceğiniz her domain bind tarafından servis edilecektir.

```
zone "rackdc.com" { type master; file "/var/named/rackdc.com.db"; };
```

Burada eklediğimiz domain için bu DNS sunucusu "master" konumundadır, slave sunucuda ise yapılması gereken şudur:

```
zone "rackdc.com" { type slave; file "/var/named/rackdc.com"; masters { BIRINCI_SUNUCU_IP; }; notify no; };
```

Şimdi de /var/named/rackdc.com.db dosyasına bakalım:

```
$TTL 14400
@       IN      SOA     ns1.rackdc.com.      hostmaster.rackdc.com. (
        2018270301    ; serial
        14400        ; Yenileme
        3600        ; Tekrar deneme
        1209600        ; Zaman aşımı
        86400 )        ; TTL
; Ad Sunucuları
rackdc.com.    NS    ns1.rackdc.com.
rackdc.com.    NS    ns2.rackdc.com.

; Standart Bölüm
localhost       A   127.0.0.1
localhost       AAAA    ::1

ns1        A        NS_IP_1
ns1        AAAA    NS_IPv6_1

ns2        A        NS_IP_2
ns2        AAAA    NS_IPv6_2

;MX Ayarı
@        MX        5 mail

; A Kayıtları
@        AAAA    SUNUCU_IPv6
@        A        SUNUCU_IP
www        A        SUNUCU_IP
mail    A        MAIL_SUNUCU_IP
```

SOA kısmının her alan adı kaydında bulunması gerekir, İngilizce State of Authority kelimelerinin baş harfleridir. Alan adı hakkında seri numarası, Yenileme süresi, tekrar deneme ve zaman aşımı gibi domain bazlı değişkenleri içerir. Global Server Load Balancing kullanılan DNS sunucularında Zaman aşımı düşük bir miktar, örneğin 10 saniyeye alınır, bu şekilde istemcinin sürekli IP adresini sorgulaması istenir.

Bu kayıt dosyasında alan adına karşılık gelen @ işareti de kullanılabilir, eğer ifadelerin sonunda "." işareti yoksa sistem ifadeyi subdomain olarak algılayacaktır.

SOA kısmı ilk yetkili DNS sunucusunun ismini \(ns1.rackdc.com\) ve yetkili email adresini içermektedir, bu kısımda yazılan email adresi @ yerine nokta ile yazılır. Buraya hostmaster yerine istediğiniz bir adresi de yazabilirsiniz. SOA kısmından sonra yetkili DNS sunucularının bilgilerini vermeniz gerekmektedir, sunucunun kendisi DNS sunucusu olsa bile alan adı farklı DNS hizmetlerini kullanıyor olabilir, bu nedenle alan adının yetkili sunucuları sırasıyla yazılır. Bu alana yazdığınız NS sunucusu sayısı kadar sunucu bu alan adı için yetkili olacaktır. Unutmayınız aynı bilgileri alan adı aldığınız kayıtçının kontrol panelinden de girmelisiniz.

```
rackdc.com. NS ns1.rackdc.com.
rackdc.com. NS ns2.rackdc.com.
```

Yukarıdaki ifade rackdc.com adresi için ns1 ve ns2.rackdc.com yetkilidir demektir. Bu ifade tam anlamıyla doğru olmasına rağmen sorgulama yapan istemciye nameserverların IP adresini vermeyecektir, bunun için istemci tekrardan sorgulama yapar ve sunucuların A kayıtlarını ister:

```
ns1 A NS_IP_1
ns2 A NS_IP_2
```

Bu kayıtlar istendikten sonra istemcinin sorgulama algoritmasına göre DNS sunucularından her hangi birinden asıl istenen sorgu yapılır, bu sorgulama bir A kaydı için, TXT kaydı için ya da MX kaydı için olabilir, aynı şekilde IPv6 için ise AAAA ile ifade edilen sorgular geçerli olacaktır.

Alan adına hizmet vermekte olan bir e-posta sunucusu varsa bu kayıt MX ifadesi ile yer almalıdır, eğer birden çok e-posta sunucusu alan adı için hizmet veriyorsa bu sunucular öncelik sırasına göre ifade edilir, en öncelikli sunucu en küçük değeri almaktadır, bu ifadenin farklı şekillerdeki yazımları aşağıda belirtilmiştir:

```
@                MX    10 mail
rackdc.com.      MX    20 posta.rackdc.com.
```

MX için yazdığınız sunucu isimlerinin de ayrı bir ifadede A kayıtlarının girilmesi gerekecektir:

```
mail.rackdc.com.    A    MAIL_SUNUCU_IP_ADRESI
posta               A    MAIL_2_SUNUCU_IP_ADRESI
```

Bir alan adının bulundurması gereken zorunlu ifadelerin yanı sıra, hatırlamakta zorluk çektiğiniz IP adresleri için de ifadeler yazabilirsiniz, örneğin evinizde statik IP kullanıyorsanız onu da burada tanımlayıp RDP ya da SSH gibi uygulamalarda DNS adı ile erişim sağlayabilirsiniz, öyle ki iç ağınızdaki cihazlar için de tanımlama yapmanız mümkündür:

```
ev.rackdc.com.    A    EV_IP_ADRESI
ofis.rackdc.com.  A    OFIS_IP_ADRESI
printer           A    192.168.0.2
switch            A    192.168.0.3
```

Named servisinin kurulumu tamamlandıktan sonra sistem açılışında otomatik olarak çalışması için aşağıdaki komutu girmelisiniz:

```
systemctl enable named.service
```

Çalıştırmak için ise

```
systemctl start named
```

DNS kayıtlarında değişiklik yaptığınızda ise rndc reload komutu ile DNS sunucusunu yeniden başlatmadan güncelleme yapabilirsiniz, var olan bir alan adı üzerinde yapılan değişikliklerde seri numara mutlaka yükseltilmelidir.

