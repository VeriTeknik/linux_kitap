# nslookup

"Name Server Lookup" anlamına gelen nslookup, BIND paketleri içerisinde en eskilerindendir. Bir dönem Internet Systems Consortium (ISC) nslookup'ın kullanımını kaldırmayı planlasa da (deprecate) 2004'te bu kararlarından vazgeçmiştir ve geliştirmeye devam etmiştir.

Bugün nslookup ile yapabileceğiniz her şeyi ve daha fazlasını dig ile gerçekleştirebilirsiniz, bu yüzden dig kullanımını tavsiye ederiz. ISC'nin nslookup desteğini kaldırıp dig kullanımına teşvik etme kararı, dig'in isim çözümlemede işletim sistemi kaynaklarına göre davranmasına, nslookup'ın ise bu işi kendi içinde halletmesine bağlanıyordu. İlerleyen versiyonlarda nslookup'ın bu bağımlılığını kaldırdılar.

Her ne kadar dig kullanımını tavsiye etsek de, bağlandığınız bazı sistemlerde dig bulunmayabilir, nslookup kullanmanız gerekebilir. Bu sebeple burada temel kullanımını göreceğiz.

