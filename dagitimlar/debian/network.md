# Network Ayarları

Debian sistemler üzerinde kalıcı network ayarları yapmak Red Hat sistemlerdekine benzer olsa da, farklı dosya yapıları barındırır. Red Hat'teki gibi her NIC kendi dosyasına sahip değildir, bütün NIC'lerin ortak bir dosyası vardır: ```/etc/network/interfaces```

Red Hat'tekinden farklı olarak, bu dosyayı yöneten servisin adı **network** değil, **networking**dir.

```bash
service networking start
```
