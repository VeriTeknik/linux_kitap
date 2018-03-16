# Güvenlik ve Optimizasyon

Bilişim sektöründe güvenlik en önemli konulardan birisi olmakla birlikte pazar payı da oldukça büyüktür. Ağa bağlı olan tüm cihazların güvenliğini etkliyen faktörleri şu şekilde açıklayabiliriz:

* Teknolojiye bağlı değişimler, 
* Kullanıcı hataları,
* Eksik sistem yapılandırması

Teknolojiye bağlı değişimler, zaman içerisinde uygulama ya da yazılımlarda çıkabilecek açıklar nedeniyle oluşmaktadır, örneğin son zamanlarda Intel yonga setlerinde çıkan açık nedeniyle neredeyse tüm işletim sistemleri saldırılara açık hale gelmiştir. Bunun yanı sıra SSL gibi güvenli veri iletişimi kurulmasını sağlayan protokoller de birer birer kullanımdan kalkmaktadır, siz her ne kadar iyi bir şekilde de güvenlik ayarlarını yapsanız da zaman zaman bu gibi nedenlerden dolayı zaman zaman sistemlerinizde beklenmedik güvenlik açıkları oluşabilmektedir.

Kullanıcı hataları, günümüzde yazılım hizmetlerinin bir "fast-food" mahiyetinde hızlı üretilip, hızlı tüketilmesi sonucunda malesef hem güvenlik hem de optimizasyon çoğu zaman göz ardı edilmekte, yazılımlar içerisinde de yeteri kadar loglama yapılmamaktadır.

Eksik sistem yapılandırması, çoğu sistem yöneticisi kurulum ve kullanım kolaylığı açısında Linux yüklemelerinden sonra selinux ve firewall'u kapatmaktadır, ne var ki yeniden kullanabileceğiniz küçük betikler ile bu sistem yapılandırmalarını kolayca yapmanız mümkündür. VeriTeknik GitHub reposunda bu betiklerin örneklerinden bulabilirsiniz.

Aşağıda anlatılan güvenlik önerileri size %100 güvenlik sağlayamayabilir ancak ne var ki sistemlerinize de lise düzeyinde yapılacak bir çok saldırıdan da korumuş olacaktır.

Genel Öneriler:

**Firewall kullanımı:** Mümkünse mutlaka bir donanımsal firewall kullanmanızı tavsiye ederiz, firewall'da yeni nesil teknolojileri kullanmıyor dahi olsanız, farklı servisler için kullandığınız sunucuların bölgeleri arasında ayrım yapmanız dahi önemli bir güvenlik sağlayacaktır, firewall arkasında NAT kullanmanız bir çok dış saldırıdan sizi koruyacaktır, aynı zamanda içeriden dışarıya sadece tanımlanmış hedeflere ve portlara izin vermeniz durumunda, sunucuya bir şekilde sızmış olan truva atları dahi dışarı çıkacak yol bulamaz. Genelde güncellemelerin kolay yapılabilmesi için içeriden dışarıya tam yetki verildiğini gözlemlemekteyiz, yum gibi bir çok güncelleme aracı en yakın yansı üzerinden güncelleme yapmaya çalışmaktadır, bizim tavsiyemiz size yakın olduğunu bildiğiniz bir yansı seçip, fastest mirror türündeki eklentileri kapatıp tek bir nokta üzerinden güncelleme yapmanızdır, aynı şekilde Windows sunucularda da merkezi bir WSUS sistemi kurabilirsiniz.

**Sunucunuza iç ve dış sızma testleri uygulayın:** Yaygın olarak kullanılan ücretsiz sızma testi uygulamaları İnternet'te yaygın şekilde kullanılmaktadır, bunlardan en çok kullanılanı Kali Linux distrosudur, sanal olarak ta kurabileceğiniz bu sistem üzerinde popüler sızma testi araçları ile birlikte gelmektedir. Çoğu bir kaç tıklama ile çalıştırılabilen bu uygulamalar sistem güvenliğinizin büyük bir ölçüde iyileşmesine yardımcı olacaktır.

**Gereksiz tüm servislerin silinmesi:** Temel dağıtımların bir çoğu ihtiyacınız olmayacak uygulamalar ve servislerle birlikte paketlenmektedir. İlk kurulum yaptığınızda özellikle dış ağlara açık uygulamaları kapatmanızda fayda vardır, bu uygulamaları kolaylıkla "netstat" komutu ile görebilirsiniz.

**Host uygulamalarının gereksiz komutlarının kapatılması:** Özellikle PHP ve Apache Web Sunucusunda bir çok fonksiyon ve servis açık durumda gelmektedir. Apache üzerinde http-info modülünü açarak kullanılmayan modulleri bulabilirsiniz, ayar dosyasında Extended Status'ün açık olmasına dikkat ediniz, işlemi tamamladıktan sonra bu modülü tekrar kapatabilirsiniz. PHP'de ise özellikle sistem kaynaklarına ve komutlarına erişim sağlayan fonksiyonları kapalı tutulmasında fayda vardır, kullanmış olduğunuz php.ini dosyasını bulun:

```
php -i | grep php.ini
```

bu dosya içerisinde disable\_functions içeriğini aşağıdaki gibi değiştirin:

```
disable_functions = "apache_get_modules,apache_get_version,apache_getenv,apache_note, apache_setenv,disk_free_space,
diskfreespace,dl, highlight_file,ini_alter,ini_restore,openlog,passthru,phpinfo, proc_nice,shell_exec,show_source,
symlink,system, exec, proc_close,proc_open,popen,escapeshellarg,escapeshellcmd,myshellexec,c99_buff_prepare,
c99_sess_ put,fpassthru"
```

* Otomatik güncellemeleri açın,
* tmp partisyonlarından çalıştırma iznini kaldırın
* Kod özgünlüğü
* Her sunucunun bir işlevi olması,
* Her servis için ayrı container, docker ya da sanal sunucu kurulması,
* NTP Senkronizasyonu
* Cloudlinux gibi, güvenlik açıklarına daha hızlı yama yapabilen sürümlerin kullanılması,
* Yedek dizinlerinin okuma erişimlerinin kaldırılması
* sadece yerel erişim gereken servislerin loopback arayüzüne çekilmesi
* özel anahtarların HSM içerisinde tutulması

### Öneriler

FTP servisini sürekli kapalı tutun, bunun için bir cron oluşturun, açmış olsanız bile kendiliğinden kapanmasını sağlayabilirsiniz.

```bash
11 0 * * * /etc/init.d/vsftpd stop
```

[http://www.veriportal.com/linux-guvenlik](http://www.veriportal.com/linux-guvenlik)

