# RAID

Günümüz bilişim altyapıları, özellikle verinin en düşük seviyede dahi güvenilir bir biçimde yazılabilir ve okunabilir olmasına ihtiyaç duymaktadır. Herhangi bir sunucu üzerinde çalışılırken sabit disklerden birinin bozulması hem sistemin çalışmasını durdurabilir, hem de verilerde kurtarılamaz kayıplara sebep olabilir. Bu tip durumların önüne geçmek için birden fazla sabit diskin bir arada kullanılarak daha güvenilir diziler oluşturması işlemine RAID (_Redundant Array of Independent Disks_) denilir. İlk defa University of California araştırmacıları tarafından 1988'de yayımlanan "A Case for Redundant Arrays of Inexpensive Disks (RAID)" makalesinde anlatılmıştır. Bu makalede ucuz disklerin paralel kullanımları yardımıyla güvenilir ve verimli disk okuma/yazma işlemlerinin sağlanabileceği anlatılmaktadır. Makale isminden anlaşılacağı gibi ucuz diskler (_inexpensive disks_) hedef alınarak başlanılsa da, günümüzde disklerin ucuz olmasından daha ziyade sistemin güvenilir ve verimli olması hedeflendiğinden, _inexpensive_ sözcüğü yerine _independent_ (bağımsız) getirilerek RAID kısaltmasına sadık kalınmıştır.

RAID yapıları sayesinde sunucular üzerindeki birden fazla disk, işletim sistemine sanki tek bir diskmiş gibi gösterilebilir, böylece hem güvenilirlik (hata toleransı) hem de çeşitli açılardan performans (okuma/yazma hızı) kazanımları sağlanabilir.

RAID uygulamaları iki ana şekilde olabilir:
*   **Donanımsal RAID (Hardware RAID):** Özel bir RAID kontrol kartı (controller) tarafından yönetilir. İşlem yükünü CPU'dan alır, genellikle daha yüksek performans sunar ve işletim sisteminden bağımsızdır. Genellikle sunucularda bulunur.
*   **Yazılımsal RAID (Software RAID):** İşletim sistemi tarafından yönetilir (örn. Linux'ta `mdadm`). Ekstra donanım gerektirmez, daha esnektir ancak CPU kaynağı kullanır ve performansı donanımsal RAID kadar yüksek olmayabilir. Disk arızası durumunda sistemin çalışmaya devam etmesi veya kurtarma işlemleri donanımsal RAID'e göre daha karmaşık olabilir.

**Önemli Not:** RAID, bir **yedekleme (backup) mekanizması değildir!** RAID, disk arızalarına karşı koruma sağlayarak sistemin çalışmaya devam etmesini (hata toleransı) hedefler. Ancak yanlışlıkla dosya silme, yazılım hataları, virüsler, doğal afetler gibi durumlara karşı koruma sağlamaz. Veri kaybını önlemek için RAID ile birlikte düzenli yedekleme stratejileri uygulamak **zorunludur**.

Birden fazla RAID seviyesi (level) bulunmaktadır ve her birinin farklı avantajları, dezavantajları, performans ve hata toleransı özellikleri vardır. Doğru RAID seviyesini seçmek, ihtiyaçlarınıza (performans, kapasite, güvenilirlik) bağlıdır.

**Bu Bölümdeki Konular:**

*   [RAID Biçimleri (Seviyeleri)](raid-bicimleri.md): Yaygın RAID seviyeleri (RAID 0, 1, 5, 6, 10) ve özellikleri.
*   [Verinin Disklere Yayılması](verinin-disklere-yayilmasi.md): Striping, mirroring ve parity kavramları.
*   [Cache (Önbellek)](cache.md): RAID kontrolcülerindeki önbelleğin rolü.
*   [Terimler ve Tavsiyeler](terimler-ve-tavsiyeler.md): RAID ile ilgili terimler ve genel öneriler.
