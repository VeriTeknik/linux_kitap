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
| NS | sanallastirma.com. | ns1.sanallastirma.com. | Alan adı sunucusunun ismi |
| A | sanallastirma.com. | 192.168.0.2 | isime karşılık gelen IP adresi |
| AAAA | sanallastirma.com. | 2a00:7300:100::2 | isime karşılık gelen IPv6 adresi |
| CNAME | www | sanallastirma.com. | Takma isim |
| MX | sanallastirma.com. | 5 mail.sanallastirma.com | Posta Sunucuları |
| TXT | sanallastirma.com. | "düz metin ifade" | Açıklamalar ve kurallar |

Sırasıyla, 

NS: sanallastirma.com için yetkili alan adı sunucusunun ns1.sanallastirma.com olduğunu ifade eder  
A/AAAA: sanallastirma.com sorgulandığında karşılık gelen IP adresi gönderilir  
CNAME: Bir alan adına takma ad olarak kullanılır, bu yapı DNS sunucusunda iki defa sorgu atılmasına neden olduğu için pratikte kullanılmaz,  
MX: alan adına bağlı olan E-Posta sunucusunun bilgisini içerir, bu kısıma IP adresi yerine alan adı yazılmalıdır  
TXT: Genelde anti-spam sistemleri tarafından belirlenmiş olan ifadeler ya da alan adı sahipliğini doğrulayan metinler içerebilir.

Not: alan adlarının sonunda "." işareti varsa ifade named tarafından tamamlanmaz, nokta koymadığınız durumlarda named bu alanı tamamlayacaktır, örneğin www yazdığınızda named bunu www.sanallastirma.com olarak algılayacaktır.

### Bind Alan Adı Sunucusu Kurulumu 



