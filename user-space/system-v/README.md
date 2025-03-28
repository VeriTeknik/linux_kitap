# System V init (SysVinit)

System V init (kısaca SysVinit), geleneksel Unix sistemlerinden miras kalan ve uzun yıllar boyunca birçok Linux dağıtımının varsayılan init sistemi olmuş bir başlangıç sistemidir. `/sbin/init` süreci (PID 1) olarak çalışır ve sistemi önceden tanımlanmış **çalışma seviyelerine (runlevels)** göre başlatmaktan ve yönetmekten sorumludur.

## Temel Özellikleri

*   **Runlevel'lar:** Sistem farklı çalışma modlarını (runlevel) tanımlar (genellikle 0-6 arası). Örneğin, runlevel 0 sistemi kapatır, 1 tek kullanıcı modudur, 3 çok kullanıcılı metin modudur, 5 grafiksel moddur, 6 sistemi yeniden başlatır. Aktif runlevel `/etc/inittab` dosyasında (veya önyükleyici parametrelerinde) belirlenir.
*   **Başlangıç Betikleri:** Her runlevel için çalıştırılacak veya durdurulacak servisler, genellikle `/etc/init.d/` (veya `/etc/rc.d/init.d/`) dizininde bulunan kabuk betikleri (shell scripts) ile yönetilir. Belirli bir runlevel'a girildiğinde, `/etc/rc<runlevel>.d/` (örn. `/etc/rc3.d/`) dizinindeki sembolik linkler aracılığıyla ilgili betikler çalıştırılır. `S` ile başlayan linkler servisleri başlatır (start), `K` ile başlayanlar durdurur (kill). Linklerdeki sayılar çalışma sırasını belirler.
*   **Sıralı Başlatma:** SysVinit genellikle servisleri betiklerdeki sıra numaralarına göre büyük ölçüde sıralı olarak başlatır. Bu, modern sistemlerdeki paralel başlatmaya göre daha yavaş olabilir.
*   **Servis Yönetimi:** Servisler genellikle `/etc/init.d/` altındaki betikler aracılığıyla (`/etc/init.d/<servis> start|stop|restart|status`) veya `service <servis> start|stop|...` komutuyla yönetilir. Servislerin başlangıçta otomatik çalışıp çalışmayacağı `chkconfig` (Red Hat tabanlı) veya `update-rc.d` (Debian tabanlı) gibi araçlarla yönetilir.

## Günümüzdeki Yeri

SysVinit, basitliği ve anlaşılırlığı nedeniyle hala bazı sistemlerde (özellikle eski veya gömülü sistemlerde) kullanılsa da, modern Linux dağıtımlarının büyük çoğunluğu (Debian, Ubuntu, Fedora, RHEL, CentOS Stream, Arch Linux vb.) varsayılan init sistemi olarak **systemd**'ye geçmiştir. systemd, paralel başlatma, daha iyi bağımlılık yönetimi, servis izleme ve daha gelişmiş özellikler sunar.

Bu bölümdeki diğer dosyalar, SysVinit'in temel kavramlarını (runlevel'lar, servis yönetimi) ve örnek bir servis betiğinin nasıl oluşturulacağını tarihsel bir bağlamda açıklamaktadır.
