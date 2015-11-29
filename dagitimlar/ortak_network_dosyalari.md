# Ortak Network Dosyaları

Her ne kadar dağıtımların network ayarlarını yaparken farklılıklar olduğunu gördüysek de, bazı dosyalar tüm sistemlerde aynı işlevi görmektedir. Bu bölümde bu dosyaları inceleyeceğiz.

## hostname

Sistemin kendi ismini bilmesi için iki dosyayı düzenlemek gerekir. Birincisi ```/etc/hostname``` dosyasıdır. Bu dosyanın içinde tek satır bulunur.

## hosts

Sistemin hem kendi ism


## resolv.conf

Sistem üzerinde nameserver'ların tutulduğu dosyadır. Aşağıdaki yapıyı izler.

```
domain localdomain
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Yukarıdaki haliyle sistem nameserver olarak Google'ın 8.8.8.8 ve 8.8.4.4 adreslerini kullanmaktadır.

domain ifadesi sistemin 