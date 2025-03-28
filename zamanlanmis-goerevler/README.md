# Zamanlanmış Görevler

Linux sistemlerinde, belirli komutların veya betiklerin (script) düzenli aralıklarla ya da belirli bir zamanda otomatik olarak çalıştırılması yaygın bir ihtiyaçtır. Bu bölümde, bu tür görevleri zamanlamak için kullanılan geleneksel araçlar ele alınacaktır.

İncelenecek konular şunlardır:

*   **`cron`**: Tekrarlayan görevleri (örn. her saat başı, her gün belirli bir saatte, her ayın ilk günü vb.) zamanlamak için kullanılan standart Unix aracıdır. Kullanıcı bazında veya sistem genelinde tanımlanabilir.
    *   [crontab](crontab.md)
*   **`at`**: Bir komutun veya betiğin gelecekte **belirli bir zamanda sadece bir kez** çalıştırılmasını sağlar.

**Modern Alternatif: systemd Timers**

Modern systemd tabanlı sistemlerde, `cron` ve `at`'in işlevlerini daha esnek ve güçlü bir şekilde yerine getiren **systemd timer** birimleri (`.timer` uzantılı dosyalar) bulunmaktadır. Timer birimleri, başka bir systemd birimini (genellikle bir `.service` birimini) belirli zamanlarda veya olaylara bağlı olarak tetiklemek için kullanılır. Daha iyi loglama, kaynak yönetimi ve bağımlılık kontrolü gibi avantajlar sunarlar. Bu kitapta geleneksel `cron` ve `at` araçlarına odaklanılsa da, systemd timer'larının modern sistemlerde güçlü bir alternatif olduğunu belirtmek gerekir.
