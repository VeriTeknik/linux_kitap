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
eaydin@dixon ~ $ ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no root@94.103.47.66
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

