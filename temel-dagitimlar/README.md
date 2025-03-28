# Temel Dağıtımlar ve Kurulum

Sunucularda kullanılan temel dağıtımların (ve bazı masaüstü dağıtımlarının) paket yönetimi, ağ ayarları gibi temel yapılandırmaları bu bölümde anlatılmıştır. Kurulum süreçleri dağıtımdan dağıtıma değişmekle birlikte, kurulum sonrası temel yönetim adımları benzerlikler gösterir.

## Disk Bölümlendirme ve Biçimlendirme Üzerine Notlar

Linux kurulumu sırasında disk bölümlendirme (partitioning) ve dosya sistemi seçimi önemli adımlardır. Kesin kurallar olmamakla birlikte, yaygın yaklaşımlar ve dikkat edilmesi gerekenler şunlardır:

**Bölümlendirme Stratejileri:**

*   **Basit:** En azından bir kök (`/`) bölümü ve bir takas (swap) alanı gereklidir. `/boot` bölümü, özellikle UEFI sistemlerde veya LVM/şifreleme kullanıldığında ayrı bir bölüm olarak oluşturulur (genellikle 500MB - 1GB yeterlidir).
*   **Ayrı Bölümler:** Sunucunun kullanım amacına göre `/home` (kullanıcı verileri), `/var` (loglar, veritabanları, web içerikleri gibi değişken veriler), `/tmp` (geçici dosyalar) gibi dizinleri ayrı bölümlere ayırmak yönetimi kolaylaştırabilir ve bir bölümün dolmasının diğerlerini etkilemesini engelleyebilir.
*   **LVM (Logical Volume Management):** Fiziksel diskleri veya bölümleri bir havuzda toplayıp, bu havuzdan mantıksal birimler (LV) oluşturmayı sağlar. Bölümleri sonradan yeniden boyutlandırma, anlık görüntü (snapshot) alma gibi esneklikler sunar. Sunucu ortamlarında sıklıkla tercih edilir.

**Takas Alanı (Swap):**

*   RAM dolduğunda veya sistem askıya alma (hibernation) için kullanılır.
*   Geleneksel olarak ayrı bir swap bölümü oluşturulurdu.
*   Modern sistemlerde, özellikle LVM kullanılmıyorsa veya esneklik isteniyorsa, **swap dosyası** kullanmak daha yaygındır. Swap dosyasının boyutu sonradan daha kolay değiştirilebilir.
*   **Boyut:** Eskiden "RAM x 2" kuralı yaygındı ancak modern sistemlerde (özellikle bol RAM olanlarda) bu genellikle gereksizdir. Gerekli swap miktarı iş yüküne bağlıdır. Genel bir başlangıç noktası:
    *   Az RAM (< 4GB): RAM kadar veya biraz fazlası.
    *   Orta RAM (4GB - 16GB): RAM kadar.
    *   Çok RAM (> 16GB): RAM'in yarısı veya sabit bir miktar (örn. 4GB-8GB), eğer hibernation kullanılmayacaksa daha azı veya hiç olmayabilir.

**Dosya Sistemleri:**

*   **ext4:** Uzun yıllardır varsayılan olan, kararlı, güvenilir ve yaygın olarak kullanılan günlük (journaling) dosya sistemidir. Çoğu durum için iyi bir tercihtir.
*   **XFS:** Özellikle RHEL ve türevlerinde varsayılan olarak gelen, yüksek performanslı, büyük dosyalar ve dosya sistemleri için optimize edilmiş bir günlük dosya sistemidir. Paralel I/O işlemleri için iyidir.
*   **Btrfs:** Modern, kopya-üzerine-yazma (copy-on-write) özellikli bir dosya sistemidir. Anlık görüntü (snapshot), alt birimler (subvolumes), dahili RAID benzeri özellikler sunar, ancak ext4 veya XFS kadar olgunlaşmış kabul edilmeyebilir.
*   **ext2:** Günlük tutmayan (non-journaling) eski bir dosya sistemidir. Modern SSD'lerde journaling'in performans etkisi ve yıpranma endişeleri büyük ölçüde azaldığı için, **ext2 kullanımı genellikle önerilmez**. `ext4`'ün sunduğu veri bütünlüğü koruması (journaling sayesinde) genellikle daha önemlidir.

Seçim, dağıtımın varsayılanlarına, sunucunun iş yüküne ve sistem yöneticisinin tecrübesine bağlı olacaktır. Çoğu genel amaçlı sunucu için `ext4` veya `XFS` güvenli ve performanslı seçeneklerdir.
