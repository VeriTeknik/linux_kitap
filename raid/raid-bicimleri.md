# RAID Seviyeleri (Biçimleri)

RAID, farklı performans, kapasite ve hata toleransı (redundancy) dengeleri sunan çeşitli **seviyelerde (levels)** uygulanır. En yaygın standart RAID seviyeleri şunlardır:

## RAID 0 (Striping - Şeritleme)

*   **Minimum Disk:** 2
*   **Konsept:** Veri blokları (şeritler - stripes) birden fazla diske bölünerek yazılır.
*   **Avantajları:**
    *   **Yüksek Performans:** Hem okuma hem de yazma işlemleri disk sayısıyla orantılı olarak hızlanır (teorik olarak N disk ile N kat).
    *   **Tam Kapasite:** Tüm disklerin toplam kapasitesi kullanılır.
*   **Dezavantajları:**
    *   **Hata Toleransı Yok:** Disklerden herhangi birinin arızalanması durumunda dizideki **tüm veri kaybedilir**. Güvenilirlik, tek bir diske göre daha düşüktür (disk sayısı arttıkça arıza olasılığı artar).
*   **Kullanım Alanları:** Hızın kritik olduğu ancak veri kaybının tolere edilebildiği durumlar (örn. geçici dosyalar, video düzenleme scratch diskleri). **Kesinlikle kritik veriler için kullanılmamalıdır.**

## RAID 1 (Mirroring - Aynalama)

*   **Minimum Disk:** 2
*   **Konsept:** Verinin aynısı, dizideki tüm disklere (genellikle 2 disk) aynı anda yazılır.
*   **Avantajları:**
    *   **Yüksek Hata Toleransı:** Disklerden biri arızalansa bile veri diğer disk(ler)de güvendedir. N diskli bir mirror'da N-1 disk arızasına tolerans gösterir.
    *   **Yüksek Okuma Performansı:** Okuma istekleri farklı disklerden paralel olarak yapılabileceği için okuma hızı artar (teorik olarak N disk ile N kat).
*   **Dezavantajları:**
    *   **Düşük Kapasite Verimliliği:** Kullanılabilir kapasite sadece tek bir diskin kapasitesi kadardır (N disk varsa Kapasite = Tek Disk Kapasitesi). Disk alanının yarısı (veya daha fazlası) yedeklilik için kullanılır.
    *   **Yazma Performansı:** Yazma hızı genellikle tek bir diskin yazma hızından daha yüksek değildir (hatta biraz daha yavaş olabilir).
*   **Kullanım Alanları:** İşletim sistemi diskleri, veritabanı logları gibi yüksek güvenilirlik ve iyi okuma performansı gerektiren durumlar.

## RAID 5 (Striping with Distributed Parity - Dağıtılmış Eşlikli Şeritleme)

*   **Minimum Disk:** 3
*   **Konsept:** Veri blokları (stripes) ve bu blokların **eşlik (parity)** bilgisi (XOR ile hesaplanır) tüm disklere dağıtılarak yazılır. Eşlik bilgisi, diğer disklerdeki verilerden hesaplanan bir sağlama değeridir.
*   **Avantajları:**
    *   **İyi Okuma Performansı:** Veri birden fazla diskten okunabilir (RAID 0 kadar hızlı değil).
    *   **İyi Kapasite Verimliliği:** Kullanılabilir kapasite, toplam disk kapasitesinin (N-1) disk kadarıdır (N disk varsa Kapasite = (N-1) * Tek Disk Kapasitesi). Sadece bir disk alanı eşlik bilgisi için kullanılır.
    *   **Hata Toleransı:** Herhangi **bir** diskin arızalanmasına tolerans gösterir. Arızalı disk değiştirildiğinde, kalan disklerdeki veri ve eşlik bilgisi kullanılarak kayıp veri yeniden oluşturulabilir (rebuild).
*   **Dezavantajları:**
    *   **Yazma Performansı Düşüklüğü:** Her yazma işlemi, hem verinin hem de eşlik bilgisinin yazılmasını gerektirir. Özellikle küçük ve rastgele yazma işlemlerinde performans düşüktür ("RAID 5 write penalty").
    *   **Rebuild Süresi ve Riski:** Bir disk arızalandığında rebuild işlemi uzun sürebilir ve bu süre zarfında ikinci bir disk arızalanırsa tüm veri kaybedilir. Büyük kapasiteli disklerde bu risk artar.
    *   **Write Hole Riski:** Geleneksel RAID 5'te, veri ve eşlik bilgisi yazılırken bir güç kesintisi olursa, eşlik bilgisi güncellenmeden kalabilir ve veri tutarsızlığı oluşabilir (ZFS RAID-Z gibi modern implementasyonlar bu sorunu çözer).
*   **Kullanım Alanları:** Okuma ağırlıklı iş yükleri, dosya sunucuları, arşivleme gibi durumlarda maliyet/kapasite/güvenilirlik dengesi için kullanılır. Ancak yazma performansı ve rebuild riski nedeniyle kritik sistemlerde RAID 10 veya RAID 6 daha çok tercih edilebilir.

## RAID 6 (Striping with Double Distributed Parity - Çift Dağıtılmış Eşlikli Şeritleme)

*   **Minimum Disk:** 4
*   **Konsept:** RAID 5'e benzer, ancak veri blokları için **iki farklı** eşlik bilgisi hesaplanır ve tüm disklere dağıtılır.
*   **Avantajları:**
    *   **Yüksek Hata Toleransı:** Herhangi **iki** diskin aynı anda arızalanmasına tolerans gösterir. Bu, RAID 5'e göre daha yüksek güvenilirlik sağlar.
    *   **İyi Okuma Performansı:** RAID 5'e benzer.
    *   **İyi Kapasite Verimliliği:** Kullanılabilir kapasite, toplam disk kapasitesinin (N-2) disk kadarıdır.
*   **Dezavantajları:**
    *   **Daha Düşük Yazma Performansı:** İki ayrı eşlik bilgisinin hesaplanması ve yazılması gerektiği için yazma performansı RAID 5'ten daha düşüktür.
    *   **Daha Uzun Rebuild Süresi:** Rebuild işlemi RAID 5'e göre daha karmaşık ve uzun sürebilir.
*   **Kullanım Alanları:** Yüksek kapasite ve yüksek güvenilirliğin önemli olduğu durumlar, arşivleme, büyük veri depolama. RAID 5'in rebuild riskinin yüksek olduğu büyük diskli sistemlerde tercih edilir.

## RAID 10 (veya RAID 1+0) (Mirroring + Striping)

*   **Minimum Disk:** 4 (ve çift sayıda olmalı)
*   **Konsept:** Önce diskler ikili gruplar halinde **aynalanır (RAID 1)**, sonra bu aynalanmış gruplar **şeritlenir (RAID 0)**.
*   **Avantajları:**
    *   **Yüksek Performans:** Hem okuma hem de yazma performansı yüksektir (RAID 0'ın hızını RAID 1'in okuma avantajıyla birleştirir).
    *   **İyi Hata Toleransı:** Her bir mirror grubundan bir disk arızalanabilir (toplamda N/2 diske kadar, ancak aynı gruptan iki disk arızalanmamalıdır).
    *   **Hızlı Rebuild:** Arızalı bir diskin yerine yenisi takıldığında, sadece ilgili mirror grubundaki diğer diskten veri kopyalanır, bu da RAID 5/6'ya göre çok daha hızlıdır.
*   **Dezavantajları:**
    *   **Düşük Kapasite Verimliliği:** Kullanılabilir kapasite, toplam disk kapasitesinin yarısıdır (RAID 1 gibi).
*   **Kullanım Alanları:** Yüksek performans (özellikle yazma) ve yüksek güvenilirliğin aynı anda gerektiği durumlar (örn. veritabanları, sanallaştırma ortamları). Maliyeti RAID 5/6'ya göre daha yüksektir.

## Diğer RAID Seviyeleri (Nested RAID)

*   **RAID 0+1:** Önce diskler şeritlenir (RAID 0), sonra bu şeritlenmiş gruplar aynalanır (RAID 1). RAID 10'a benzer ancak hata toleransı daha düşüktür (tek bir disk arızası tüm bir RAID 0 grubunu bozabilir ve bu da tüm diziyi etkileyebilir). Genellikle RAID 10 tercih edilir.
*   **RAID 50 (RAID 5+0):** RAID 5 grupları oluşturulur ve bunlar RAID 0 ile şeritlenir. RAID 5'ten daha iyi yazma performansı ve daha hızlı rebuild sunar. En az 6 disk gerektirir.
*   **RAID 60 (RAID 6+0):** RAID 6 grupları oluşturulur ve bunlar RAID 0 ile şeritlenir. RAID 6'dan daha iyi performans sunar ve çok yüksek hata toleransı sağlar. En az 8 disk gerektirir.

## Yazılımsal RAID (`mdadm`)

Linux'ta yazılımsal RAID dizileri oluşturmak ve yönetmek için standart araç `mdadm`'dir (multiple disk admin). `mdadm`, yukarıda bahsedilen standart RAID seviyelerinin çoğunu (RAID 0, 1, 4, 5, 6, 10) ve bazı ek özellikleri (örn. hot spare diskler) destekler. `mdadm` ile oluşturulan RAID dizileri `/dev/mdX` (örn. `/dev/md0`, `/dev/md127`) gibi aygıt dosyaları olarak görünür ve bunlar üzerine LVM kurulabilir veya doğrudan dosya sistemi oluşturulabilir.

Doğru RAID seviyesini seçmek, uygulamanın ihtiyaçları (performans, kapasite, güvenilirlik) ve bütçe arasındaki dengeye bağlıdır.
