# PCI-DSS ve Linux Sistemleri

Bu bölüm, Kredi Kartı Sektörü Veri Güvenliği Standardı (Payment Card Industry Data Security Standard - PCI-DSS) ve bu standardın Linux sistemleri yönetimiyle ilgili temel gereksinimlerine bir giriş yapmaktadır.

**Not:** PCI-DSS uyumluluğu karmaşık bir süreçtir ve bu bölüm sadece genel bir farkındalık sağlamayı amaçlar, tam bir uyumluluk rehberi değildir. Uyumluluk için mutlaka bir QSA (Qualified Security Assessor) ile çalışılmalı ve güncel PCI-DSS standartları referans alınmalıdır.

**İncelenecek Konular:**

*   PCI-DSS Nedir? Amacı ve Kapsamı
*   PCI-DSS'in 12 Temel Gereksinimi (Ana Başlıklar)
*   Linux Sistemleri için Önemli PCI-DSS Gereksinimleri:
    *   Güvenlik Duvarı Yapılandırması (Gereksinim 1)
    *   Varsayılan Parolaların ve Güvenlik Ayarlarının Değiştirilmesi (Gereksinim 2)
    *   Kart Sahibi Verisinin Korunması (Gereksinim 3) - (Linux'ta doğrudan kart verisi saklanmamalıdır)
    *   Açık Ağlarda Kart Sahibi Verisinin Şifrelenmesi (Gereksinim 4) - (TLS kullanımı)
    *   Antivirüs Yazılımı Kullanımı ve Güncellenmesi (Gereksinim 5) - (Linux için ClamAV vb.)
    *   Güvenli Sistemler ve Uygulamalar Geliştirme ve Sürdürme (Gereksinim 6) - (Yama yönetimi, güvenli yapılandırma)
    *   Kart Sahibi Verisine Erişimin Kısıtlanması (Gereksinim 7) - (Yetkilendirme, en az yetki prensibi)
    *   Sistem Bileşenlerine Erişimin Kimlik Doğrulaması (Gereksinim 8) - (Güçlü şifreler, MFA, kullanıcı yönetimi)
    *   Kart Sahibi Verisine Fiziksel Erişimin Kısıtlanması (Gereksinim 9) - (Sunucu ortamı güvenliği)
    *   Ağ Kaynaklarına ve Kart Sahibi Verisine Erişimin İzlenmesi ve Takibi (Gereksinim 10) - (Loglama, `auditd`, merkezi loglama)
    *   Güvenlik Sistemlerinin ve Süreçlerinin Düzenli Olarak Test Edilmesi (Gereksinim 11) - (Zafiyet taramaları, sızma testleri, dosya bütünlüğü izleme)
    *   Bilgi Güvenliği Politikalarının Sürdürülmesi (Gereksinim 12)
*   Linux Sistemlerini PCI-DSS Uyumlu Hale Getirmek İçin İpuçları (Özet)
