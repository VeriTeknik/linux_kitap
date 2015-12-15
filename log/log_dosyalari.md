# Log Dosyaları

Log dosyaları LINUX ve UNIX sistemlerin en önemli ve ayrılmaz parçalarından biridir. Diğer işletim sistemlerinde şahit olduğumuz "hatır-şinas" loglama bu sistemlerde "ciddi" loglama olarak karşımıza çıkıyor. Genelde /var/log dizini altında yer alan log dosyalarının her birinin bir ya da birden çok servisi logladığını görebilirsiniz. 

Diğer işletim sistemlerinin aksine, LINUX tek satırlık log girdileri tutar, okumayı kolaylaştıran bu sistem sayesinde loglar içerisinde kaybolmazsınız.

Web Hosting yapan bir Sistem Uzmanının sürekli takip etmesi gereken log dosyaları şunlardır:

| LOG | Yolu | Amacı |
| -- | -- | -- |
| Messages | /var/log/messages | Genel amaçlı log dosyasıdır, bir çok sistem servisi bu dosyaya loglarını yazar, sistem açılış mesajları, disk arızaları da bu dosyaya yazılır |
| dmesg | /var/log/dmesg | kernel tüm mesajlarını bu dosya içerisine atar, mrneğin PCI portlarıi diskler ve diğer aygıtlar hakkında bu dosyadan bilgi alabilirsiniz. |


Bu dosyaların yanı sıra, kernel'in döngülü hafızasında bulunan mesajları da dmesg komutu ile görüntüleyebilirsiniz.Aynı şekilde, hangi kullanıcının en son ne zaman giriş yaptığını görüntülemek için 'lastlog' komutunu kullanabilirsiniz.