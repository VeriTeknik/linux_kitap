# Güvenlik

Bilişim sektöründe güvenlik en önemli konulardan birisi olmakla birlikte pazar payı da oldukça büyüktür. Ağa bağlı olan tüm cihazların güvenliğini etkliyen faktörleri şu şekilde açıklayabiliriz:

* Teknolojiye bağlı değişimler, 
* Kullanıcı hataları,
* Eksik sistem yapılandırması

Teknolojiye bağlı değişimler, zaman içerisinde uygulama ya da yazılımlarda çıkabilecek açıklar nedeniyle oluşmaktadır, örneğin son zamanlarda Intel yonga setlerinde çıkan açık nedeniyle neredeyse tüm işletim sistemleri saldırılara açık hale gelmiştir. Bunun yanı sıra SSL gibi güvenli veri iletişimi kurulmasını sağlayan protokoller de birer birer kullanımdan kalkmaktadır, siz her ne kadar iyi bir şekilde de güvenlik ayarlarını yapsanız da zaman zaman bu gibi nedenlerden dolayı zaman zaman sistemlerinizde beklenmedik güvenlik açıkları oluşabilmektedir.

### Öneriler

FTP servisini sürekli kapalı tutun, bunun için bir cron oluşturun, açmış olsanız bile kendiliğinden kapanmasını sağlayabilirsiniz.

```bash
11 0 * * * /etc/init.d/vsftpd stop
```

[http://www.veriportal.com/linux-guvenlik](http://www.veriportal.com/linux-guvenlik)

