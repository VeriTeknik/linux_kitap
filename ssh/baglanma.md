# Uzak Sunucuya Bağlanma

Komut satırından ssh ile bir sunucuya bağlanmak oldukça kolaydır.

```bash
eaydin@dixon ~ $ ssh 94.103.47.66
eaydin@94.103.47.66's password: 
```

Gördüğünüz gibi yukarıdaki gibi bağlandığımızda sunucu şifre sormaktadır. Bir kullanıcı adı belirtmediğimiz için, mevcut kullanıcıyı parametre olarak göndermiştir. Oysa ki sunucuya **root** kullanıcısı ile bağlanmak istiyorduk.

```bash
eaydin@dixon ~ $ ssh root@94.103.47.66
root@94.103.47.66's password:
```

Bu noktada root şifresini girip sisteme bağlanabiliriz.

Bazı durumlarda ssh sunucusunun portu farklı olabilir. Bu yaygın güvelik önlemlerinden birisidir. Örneğin yukarıdaki sunucumuzun portu standart SSH portu olan 22 yerine 2291 olsaydı, aşağıdaki gibi bağlanabilirdik.

```bash
ssh root@94.103.47.66 -p 2291
```

## Kimlik Doğrulama Yöntemini Seçme

Sunucuya şifreyle bağlanmak yerien, özel anahtarımızla da bağlanabiliriz. İlerleyen bölümlerde bunu nasıl yapacağımızı göreceğiz. Ama şimdilik anahtar yüklü bir sisteme bağlanırken kimlik doğrulama yöntemini nasıl seçeceğimizi görebiliriz.

Aşağıdaki yöntem, kimlik doğrulamada anahtarı gözardı edip, şifre girmeyi zorunlu hale getirmektedir.

```bash
eaydin@dixon ~ $ ssh -o PreferredAuthentications=password \
-o PubkeyAuthentication=no root@94.103.47.66
root@94.103.47.66's password: 
```

Öte yandan, dökümanlarda yer almasa da, bir [stackexchange](http://stackexchange.com) kullanıcısının OpenSSH kodlarını incelerken dikkatini çeken "şifre sormayı zorlama" yöntemini sizinle paylaşmak istiyoruz:

```bash
eaydin@dixon ~ $ ssh root:@94.103.47.66
root:@94.103.47.66's password:
```
[*Kaynak*](http://unix.stackexchange.com/a/124582)


Aşağıdaki yöntemleyse, ayar dosyamızda belirilen ssh anahtarı dışında bir anahtarla bağlanabilmenin yolu görülmektedir.

```bash
eaydin@dixon ~ $ ssh -i /media/ssh_keys/eaydin_id_rsa root@94.103.47.66
```

## Strict Host Key Checking

Eğer bir sunucuya ilk kez bağlanıyorsanız, genellikle aşağıdaki gibi bir soruyla karşılaşırsınız.

```bash
eaydin@dixon ~ $ ssh root:@94.103.47.66
The authenticity of host '94.103.47.66 (94.103.47.66)' can't be established.
RSA key fingerprint is c2:54:d7:77:57:76:a1:78:f8:82:8b:48:de:89:71:c5.
Are you sure you want to continue connecting (yes/no)? 
```

Burada ssh, bağlanacağınız sunucunun RSA parmak izine bakıp gerçekten doğru sunucu olup olmadığını bildiğinizi sorar. Tabii ki bu rakamları ezberlemenizin imkanı yoktur dolayısıyla ilk bağlandığınızda genellikle buna **yes** demek normaldir.

Ancak daha sonra bu soruyu sormaz, çünkü artık RSA parmak izini kenara not etmiştir SSH. 

```bash
eaydin@dixon ~ $ ssh root:@94.103.47.66
The authenticity of host '94.103.47.66 (94.103.47.66)' can't be established.
RSA key fingerprint is c2:54:d7:77:57:76:a1:78:f8:82:8b:48:de:89:71:c5.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '94.103.47.66' (RSA) to the list of known hosts.
root:@94.103.47.66's password
```

İlerleyen zamanlarda eğer yine bu sunucuya bağlanacak olursanız ve sunucunun RSA parmak izi değişmiş olursa farklı bir uyarı verir.

```bash
eaydin@dixon ~ $ ssh root:@94.103.47.66
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
c2:54:d7:77:57:76:a1:78:f8:82:8b:48:de:89:71:c5.
Please contact your system administrator.
Add correct host key in /home/eaydin/.ssh/known_hosts to get rid of this message.
Offending RSA key in /home/eaydin/.ssh/known_hosts:1
  remove with: ssh-keygen -f "/home/eaydin/.ssh/known_hosts" -R 94.103.47.66
RSA host key for 94.103.47.66 has changed and you have requested strict checking.
Host key verification failed.
```

Bu noktada SSH kısaca "bağlanmaya çalıştığın sunucunun parmak izi daha önceden farklıydı. Bu şüpheli bir durum. Eğer yine de devam etmek istiyorsan benim kenara not ettiğim satırı sil lütfen" demektedir.

Hata mesajında parmak izini nereye not ettiği görülebilir: ```/home/eaydin/.ssh/known_hosts:1``` yani bu dosyanın ilk satırı.

Bu satırı silersek, tekrar bize **yes/no** sorusunu soracaktır. Öte yandan tek seferlik bu kontrolü engellemek için aşağıdaki gibi bir bağlantı yapabilirdik.

```bash
eaydin@dixon ~ $ ssh -o UserKnownHostsFile=/dev/null root:@94.103.47.66
The authenticity of host '94.103.47.66 (94.103.47.66)' can't be established.
RSA key fingerprint is c2:54:d7:77:57:76:a1:78:f8:82:8b:48:de:89:71:c5.
Are you sure you want to continue connecting (yes/no)? 
```

Aslında yaptığımız ```/home/eaydin/.ssh/known_hosts``` dosyasını yerine ```/dev/null``` kullanmak oldu. Bu dosya da boş olunca, yine RSA parmak izini tanıyıp tanımadığımızı sordu. Eğer bu soruyu da sormamasını isteseydik,

```bash
eaydin@dixon ~ $ ssh -o UserKnownHostsFile=/dev/null \
-o StrictHostKeyChecking=no \root:@94.103.47.66
Warning: Permanently added '94.103.47.66' (RSA) to the list of known hosts.
root:@94.103.47.66's password: 
```

Yukarıdaki mesajda her ne kadar "Permanently added" dese de, eklediği liste dosyası ```/dev/null``` olduğu için, bu bilgi hiçbir yere kaydedilmemiş oldu.

## İstemci Ayarları

Yukarıdaki örneklerde ```-o``` parametresiyle bağlantı sırasında bazı seçenekleri açıp kapattık. Aslında burada yaptığımız, ssh istemcinin tanımlanmış ayarlarında bazılarını kullanmayıp o an belirttiklerimizi zorlamaktı.

Sözkonusu istemci ayarları sistem üzerinde ```/etc/ssh/ssh_config``` yolunda yer alır.

Örneğin bu dosyada ```StrictHostKeyChecking no``` yaptığımız takdirde yukarıdaki **yes/no** sorusuyla karşılaşmayız. (Tavsiye edilmez!) Benzer şekilde kullanılacak özel anahtarların yolu, şifre kullanımına izin verilmesi, port belirtilmediği takdirde hangi portun kullanılacağı (öntanımlı değer 22) gibi bir çok seçenek ayarlanabilir.

ssh_config hakkında yardım dosyalarını okumak için

```bash
man 5 ssh_config
```
