# Uzak Sunucuya Bağlanma

Komut satırından ssh ile bir sunucuya bağlanmak oldukça kolaydır.

```bash
eaydin@dixon ~ $ ssh 94.103.47.66
eaydin@94.103.47.66's password:
```

Yukarıdaki gibi bağlandığımızda sunucu şifre sormaktadır. Bir kullanıcı adı belirtmediğimiz için, mevcut kullanıcıyı parametre olarak göndermiştir. Oysa ki sunucuya **root** kullanıcısı ile bağlanmak istiyorduk.

```bash
eaydin@dixon ~ $ ssh root@94.103.47.66
root@94.103.47.66's password:
```

Bu noktada root şifresini girip sisteme bağlanabiliriz.

Bazı durumlarda ssh sunucusunun portu farklı olabilir. Bu, yaygın güvenlik önlemlerinden birisidir. Örneğin yukarıdaki sunucumuzun portu standart SSH portu olan 22 yerine 2291 olsaydı, aşağıdaki gibi bağlanabilirdik.

```bash
ssh root@94.103.47.66 -p 2291
```

**İpucu:** Sık bağlandığınız sunucular için kullanıcı adı, port, özel anahtar yolu gibi seçenekleri her seferinde yazmak yerine, kullanıcının kendi ev dizinindeki `~/.ssh/config` dosyasında tanımlayabilirsiniz. Bu dosya, bağlantı kısayolları ve özel ayarlar tanımlamak için çok kullanışlıdır.

Örnek `~/.ssh/config` dosyası:
```
Host testserver
    HostName 94.103.47.66
    User root
    Port 2291
    IdentityFile ~/.ssh/id_testserver_ed25519

Host anotherserver
    HostName domain.adı.com
    User myuser
    Port 22
```
Bu yapılandırma ile `ssh testserver` komutu otomatik olarak `ssh root@94.103.47.66 -p 2291 -i ~/.ssh/id_testserver_ed25519` komutuna genişleyecektir.

## Kimlik Doğrulama Yöntemini Seçme

Sunucuya şifreyle bağlanmak yerine, özel anahtarımızla da bağlanabiliriz. İlerleyen bölümlerde bunu nasıl yapacağımızı göreceğiz. Ama şimdilik anahtar yüklü bir sisteme bağlanırken kimlik doğrulama yöntemini nasıl seçeceğimizi görebiliriz.

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

[_Kaynak_](http://unix.stackexchange.com/a/124582)

Yukarıdaki yöntem, 2015 tarihinden itibaren çalışmamaya başlamış olsa da, bazı programlarda dokümante edilmemiş özelliklerin barınabileceğini göstermek için iyi bir örnek teşkil ediyor.

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

Burada SSH istemcisi, daha önce bağlanmadığınız bu sunucunun kimliğini (`94.103.47.66`) sunduğu genel anahtarın parmak izi (fingerprint) ile birlikte size gösterir. Bu mekanizmanın amacı, ortadaki adam (Man-in-the-Middle - MitM) saldırılarını önlemektir. İlk bağlantıda sunucunun parmak izini doğrulamanız (örneğin sunucu yöneticisinden teyit alarak) ve `yes` diyerek kabul etmeniz beklenir. Kabul ettiğinizde, sunucunun adresi ve genel anahtarı sizin `~/.ssh/known_hosts` dosyanıza kaydedilir.

**Not:** Örnekte RSA anahtarı gösterilmiştir. Modern SSH sunucuları genellikle daha güvenli ve performanslı olan Ed25519 veya ECDSA anahtarlarını tercih eder ve sunar. Parmak izi formatı da anahtar türüne göre değişebilir (örn. SHA256 tabanlı).

Ancak daha sonra bu soruyu sormaz, çünkü artık RSA parmak izini kenara not etmiştir SSH.

```bash
eaydin@dixon ~ $ ssh root:@94.103.47.66
The authenticity of host '94.103.47.66 (94.103.47.66)' can't be established.
RSA key fingerprint is c2:54:d7:77:57:76:a1:78:f8:82:8b:48:de:89:71:c5.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '94.103.47.66' (RSA) to the list of known hosts.
root:@94.103.47.66's password
```

İlerleyen zamanlarda aynı sunucuya tekrar bağlanmaya çalıştığınızda, SSH istemcisi sunucudan gelen genel anahtarı `known_hosts` dosyanızdaki kayıtlı anahtarla karşılaştırır. Eğer anahtarlar eşleşmezse (sunucu yeniden kurulmuş olabilir, anahtarı değişmiş olabilir veya gerçekten bir MitM saldırısı olabilir), SSH bağlantıyı durdurur ve aşağıdaki gibi bir uyarı verir:

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

Hata mesajında parmak izini nereye not ettiği görülebilir: `/home/eaydin/.ssh/known_hosts:1` yani bu dosyanın ilk satırı.

Bu satırı silersek, tekrar bize **yes/no** sorusunu soracaktır. Öte yandan tek seferlik bu kontrolü engellemek için aşağıdaki gibi bir bağlantı yapabilirdik.

```bash
eaydin@dixon ~ $ ssh -o UserKnownHostsFile=/dev/null root:@94.103.47.66
The authenticity of host '94.103.47.66 (94.103.47.66)' can't be established.
RSA key fingerprint is c2:54:d7:77:57:76:a1:78:f8:82:8b:48:de:89:71:c5.
Are you sure you want to continue connecting (yes/no)?
```

Aslında yaptığımız `/home/eaydin/.ssh/known_hosts` dosyasını yerine `/dev/null` kullanmak oldu. Bu dosya da boş olunca, yine RSA parmak izini tanıyıp tanımadığımızı sordu. Eğer bu soruyu da sormamasını isteseydik,

```bash
eaydin@dixon ~ $ ssh -o UserKnownHostsFile=/dev/null \
-o StrictHostKeyChecking=no root:@94.103.47.66
Warning: Permanently added '94.103.47.66' (RSA) to the list of known hosts.
root:@94.103.47.66's password:
```

Yukarıdaki mesajda her ne kadar "Permanently added" dese de, eklediği liste dosyası `/dev/null` olduğu için, bu bilgi hiçbir yere kaydedilmemiş oldu.

`known_hosts` dosyasına eklenen bir RSA parmak izini silmek için dosyayı düzenlemek yerine `ssh-keygen` komutunun bir parametresini kullanabilirdik. Bu bizi hashlenmiş dosyada ilgili satırı bulma derdinden kurtarmaktadır. Yukarıdaki örnek için yapacak olursak:

```bash
eaydin@dixon ~ $ ssh-keygen -R 94.103.47.66
# Host 94.103.47.66 found: line 2 type RSA
/home/eaydin/.ssh/known_hosts updated.
Original contents retained as /home/eaydin/.ssh/known_hosts.old
```

## İstemci Ayarları

Yukarıdaki örneklerde `-o` parametresiyle bağlantı sırasında bazı seçenekleri açıp kapattık. Aslında burada yaptığımız, ssh istemcinin tanımlanmış ayarlarında bazılarını kullanmayıp o an belirttiklerimizi zorlamaktı.

SSH istemcisinin davranışını etkileyen ayarlar iki ana dosyada bulunur:
1.  **`/etc/ssh/ssh_config`**: Sistem genelindeki varsayılan ayarlar.
2.  **`~/.ssh/config`**: Kullanıcıya özel ayarlar. Bu dosyadaki ayarlar, sistem genelindeki ayarları geçersiz kılar (override eder). Sık kullanılan bağlantılar için kısayollar ve özel seçenekler tanımlamak için idealdir.

Örneğin, `/etc/ssh/ssh_config` dosyasında veya `~/.ssh/config` dosyasında `StrictHostKeyChecking no` ayarı yapılırsa, SSH bilinmeyen veya değişmiş anahtarlar için soru sormaz (güvenlik açısından **tavsiye edilmez**). Benzer şekilde, varsayılan kullanıcı, port, kullanılacak kimlik dosyaları (`IdentityFile`), bağlantı zaman aşımı (`ConnectTimeout`) gibi birçok seçenek bu dosyalarda `Host` blokları altında veya genel olarak tanımlanabilir.

Detaylı bilgi için `ssh_config(5)` man sayfasına bakabilirsiniz:
```bash
man 5 ssh_config
```
