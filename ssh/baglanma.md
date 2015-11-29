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

Aşağıdaki yöntemleyse, ayar dosyamızda belirilen ssh anahtarı dışında bir anahtarla bağlanabilmenin yolu görülmektedir.

```bash
eaydin@dixon ~ $ ssh -i /media/ssh_keys/eaydin_id_rsa root@94.103.47.66
```

