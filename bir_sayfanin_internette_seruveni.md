# Bir sayfanın İnternetteki Serüveni

Vermiş olduğumuz eğitimlerde, çoğunlukla İnternet'in çalışma mantığının çok iyi anlatılmadığı ya da kavranmadığı konusunda deneyimlerimiz tecrübe ile sabittir.

Günümüzde kullanılan İnternet Alan Adı çözme sistemi belirli konsorsiyumların ve iktidar odaklarının denetiminde ve tekelindedir. Amerika merkezli kar amacı güdmeyen ICANN adlı firma tüm alan adı çözünürlüğünün dağıtımını sağlamaktadır. Örneğin ".com" uzantılı alan adlarını sadece VeriSign adlı Amerikan firması yapabilir ve ICANN her yıl ücretlerini %7 oranında arttırabilmesi için yetki vermiştir.

İşletim sistemleri ve onların bağlı bulunduğu DNS sunucuları, ICANN'ın belirlemiş olduğu kök DNS sunucularını kurulumları sırasında "varsayılan" kök DNS sunucuları olarak belirler. Linux sistemlerde şu komut ile kök sunucularının bilgisine ulaşabilirsiniz.

```bash
dig +nocomments
; <<>> DiG 9.8.2rc1-RedHat-9.8.2-0.37.rc1.el6_7.4 <<>> +nocomments
#;; global options: +cmd
#;.                              IN      NS
#.                       58399   IN      NS      h.root-servers.net.
#.                       58399   IN      NS      i.root-servers.net.
#.                       58399   IN      NS      e.root-servers.net.
#.                       58399   IN      NS      f.root-servers.net.
#.                       58399   IN      NS      c.root-servers.net.
#.                       58399   IN      NS      j.root-servers.net.
#.                       58399   IN      NS      m.root-servers.net.
#.                       58399   IN      NS      a.root-servers.net.
#.                       58399   IN      NS      l.root-servers.net.
#.                       58399   IN      NS      g.root-servers.net.
#.                       58399   IN      NS      b.root-servers.net.
#.                       58399   IN      NS      k.root-servers.net.
#.                       58399   IN      NS      d.root-servers.net.
#a.root-servers.net.     158901  IN      A       198.41.0.4
#a.root-servers.net.     158901  IN      AAAA    2001:503:ba3e::2:30
#b.root-servers.net.     158901  IN      A       192.228.79.201
#b.root-servers.net.     158901  IN      AAAA    2001:500:84::b
#c.root-servers.net.     158901  IN      A       192.33.4.12
#c.root-servers.net.     158901  IN      AAAA    2001:500:2::c
#d.root-servers.net.     158901  IN      A       199.7.91.13
#d.root-servers.net.     158901  IN      AAAA    2001:500:2d::d
#e.root-servers.net.     158901  IN      A       192.203.230.10
#f.root-servers.net.     158901  IN      A       192.5.5.241
#f.root-servers.net.     158901  IN      AAAA    2001:500:2f::f
#g.root-servers.net.     158901  IN      A       192.112.36.4
#h.root-servers.net.     158901  IN      A       198.97.190.53
#;; Query time: 0 msec
#;; SERVER: 127.0.0.1#53(127.0.0.1)
#;; WHEN: Mon Dec  7 23:31:25 2015
#;; MSG SIZE  rcvd: 496
```

