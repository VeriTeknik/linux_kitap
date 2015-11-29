# Ortak Network Dosyaları

Her ne kadar dağıtımların network ayarlarını yaparken farklılıklar olduğunu gördüysek de, bazı dosyalar tüm sistemlerde aynı işlevi görmektedir. Bu bölümde bu dosyaları inceleyeceğiz.

## hostname

Sistemin kendi ismini bilmesi için düzenlenmesi gereken iki dosyadan biridir. ```/etc/hostname``` yolundadır. Bu dosyanın içinde tek satır bulunur.

```bash
eaydin@dixon ~ $ cat /etc/hostname
dixon
```

## hosts

Sistemin adreslere karşılık isim tuttuğu dosyadır. hostname içine yazılan değer 


## resolv.conf

Sistem üzerinde nameserver'ların tutulduğu dosyadır. Aşağıdaki yapıyı izler.

```
domain localdomain
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Yukarıdaki haliyle sistem nameserver olarak Google'ın 8.8.8.8 ve 8.8.4.4 adreslerini kullanmaktadır.

domain ifadesi sistemin 