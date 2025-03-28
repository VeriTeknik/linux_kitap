# Başlangıç: Init (PID 1)

Linux çekirdeği (kernel) başlatma işlemini tamamladıktan sonra, kontrolü kullanıcı alanındaki (user space) ilk sürece devreder. Bu ilk süreç her zaman **PID 1** (Process ID 1) olarak çalışır ve genellikle `/sbin/init` yoluyla başlatılır.

## PID 1'in Rolü

`init` süreci, sistemin temelini oluşturur ve kritik görevleri yerine getirir:

1.  **Tüm Süreçlerin Atası:** Sistemdeki diğer tüm kullanıcı alanı süreçleri (servisler, kullanıcı oturumları, uygulamalar vb.) doğrudan veya dolaylı olarak `init` süreci tarafından başlatılır. Bu nedenle, `init` tüm süreçlerin en üst düzeydeki atasıdır. `pstree` komutu ile bu hiyerarşi görülebilir.
2.  **Yetim Süreçleri Evlat Edinme (Orphan Process Adoption):** Normalde bir süreç (child process) sonlandığında, durumu üst süreci (parent process) tarafından okunur (reaping). Eğer bir üst süreç, alt süreci sonlanmadan önce kendisi sonlanırsa, alt süreç "yetim" (orphan) kalır. Bu durumda, yetim kalan süreç otomatik olarak `init` (PID 1) tarafından evlat edinilir. `init` süreci, evlat edindiği süreçler sonlandığında onların durumunu okuyarak sistemde "zombi" süreçlerin birikmesini engeller. (Zombi süreçler, sonlanmış ancak durumu üst süreci tarafından okunmamış süreçlerdir ve işlem tablosunda gereksiz yer kaplarlar).
3.  **Sistemi Başlatma ve Durdurma:** `init` süreci, yapılandırmasına göre sistem servislerini belirli bir sıra ve bağımlılık ilişkisi içinde başlatır. Aynı şekilde, sistem kapatılırken veya yeniden başlatılırken servislerin düzgün bir şekilde durdurulmasından ve dosya sistemlerinin güvenli bir şekilde ayrılmasından (unmount) sorumludur.
4.  **Sistem Durumunu Yönetme:** `init` sistemi, sistemin farklı çalışma durumlarını (örneğin, tek kullanıcı modu, çok kullanıcılı metin modu, grafiksel mod) yönetir ve bu durumlar arasında geçişi sağlar. Modern systemd'de bu "target" birimleri ile, eski SysVinit'te ise "runlevel"lar ile yapılır.

## `/sbin/init` ve Gerçek Init Sistemi

`/sbin/init` dosyası genellikle gerçek init sisteminin çalıştırılabilir dosyasına bir sembolik linktir. Modern sistemlerde bu link genellikle `/lib/systemd/systemd`'ye işaret eder. Eski sistemlerde ise doğrudan SysVinit'in `/sbin/init` dosyası olabilir. Çekirdek, varsayılan olarak `/sbin/init`'i çalıştırmaya çalışır, ancak bu davranış `init=` çekirdek parametresi ile değiştirilebilir (örneğin kurtarma modunda `init=/bin/bash` kullanmak gibi).

Hangi init sisteminin çalıştığını anlamak için [Init Versiyonunu Belirlemek](init-versiyonunu-belirlemek.md) bölümüne bakabilirsiniz. `init` sürecinin kendisi sonlandırılırsa (ki bu genellikle engellenmiştir), sistem paniğe (kernel panic) girer ve durur, çünkü tüm süreçlerin temel dayanağı ortadan kalkmış olur.
