# Log Analiz Yöntemleri

Log dosyalarının UNIX sistemler üzerinde en verimli şekilde kullanmanın yolu, log dosyası oluşurken anlık olarak takip etmektir. Bu işlemi yaparken "tail" komutu kullanılur. tail argüman verilmeden çalıştırıldığında dosyanın en son satırlarını gösterir, ancak "-f" argümanı ile \(çoğu işletim sisteminde **tailf** şeklinde alias olarak bulunmaktadır\) dosya oluşurken takip etmek mümkündür. Tipik kullanımı şu şekildedir:

```bash
tailf /var/log/messages
```

Yukarıdaki komutu girdikten sonra, en son nerede kaldığınızı hatırlamak için bir kaç kez enter tuşuna basıp satırlar arasında ayırt edici bir boşluk bırakabilirsiniz. Siz enter'a bassanız dahi yeni log oluştuğunda boşluklardan sonra log akmaya devam edecektir.

Başka güzel bir metod ise, daha hızlı akan loglarda sadece aranan kelimenin bulunduğu satırın gözükmesini sağlamaktır, UNIX log standartlarında, her hatanın bir satır halinde verilmesi olasıdır. Diğer işletim sistemlerinde ya da Java'daki gibi bir hatayı ifade etmek için birden çok satır kullanılmaz.

Örnek vermek gerekirse:

```bash
mail# tailf maillog | grep IMAP
May 19 00:20:34 mail imapd: LOGIN, user=user@domain.com.tr, ip=[10.10.128.25], port=[58833], protocol=IMAP
```

Yukarıda yoğun bir mail sunucusunda sadece "IMAP" eventleri filtrelenmiştir, bu şekilde bir tarafta log ekreanı açıkken diğer tarafta denemelerinizi yapabilir, sonuçlarını canlı izleyebilirsiniz.

### Journalctl

Yeni nesil işletim sistemlerinde "journalctl" komutuyla aynı işi daha kolay yapabilirsiniz. Günlük servisi \(systemd-journald.service\) sisteminizde çalışıyorsa, tutulan günlüğün en son halini şu şekilde öğrenebilirsiniz:

en baştan günlüğe bakmak isterseniz:

```bash
journalctl -xm
>Apr 20 02:13:02 localhost systemd-journal[112]: Runtime journal is using 8.0M ...
>Apr 20 02:13:02 localhost systemd-journal[112]: Runtime journal is ...
```

en son günlük bilgilerini almak için ise journalctl -xn kullanılabilir, bu komut hakkında daha detaylı bilgi man arşivlerinde bulunmaktadır.Journal dosyasını sürekli akar şekilde takip etmek isterseniz şu komutu kullanabilirsiniz:

```
[root@ckaraca ~]# journalctl -xf
-- Logs begin at Mon 2018-03-05 18:25:01 +03. --
Apr 06 23:25:02 manage postfix/cleanup[1095]: 165932084F53: message-id=<20180406202502.165932084F53@manage>
Apr 06 23:25:02 manage postfix/local[1103]: 14B002084F55: to=<manage>, relay=local, delay=0.01...
```



