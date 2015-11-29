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

