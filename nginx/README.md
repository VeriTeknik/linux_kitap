# Nginx Web Sunucusu

Bu bölüm, yüksek performanslı, olay tabanlı (event-driven) mimarisiyle bilinen popüler açık kaynaklı web sunucusu ve ters proxy (reverse proxy) yazılımı olan Nginx'i ele almaktadır. Apache'ye güçlü bir alternatif olarak sıklıkla kullanılır.

**İncelenecek Konular:**

*   Nginx Nedir? Apache'den Farkları Nelerdir? (Mimari, Performans, Kullanım Alanları)
*   Nginx Kurulumu (Paket Yöneticisi ile)
*   Temel Yapılandırma Dosyaları ve Sözdizimi (`nginx.conf`, `sites-available`/`sites-enabled`, `conf.d`)
*   Servis Yönetimi (`systemctl`)
*   Temel Direktifler (`worker_processes`, `events`, `http`, `server`, `location`)
*   Sanal Ana Bilgisayar (Server Blocks) Yapılandırması
*   Statik İçerik Sunma
*   Ters Proxy (Reverse Proxy) Olarak Kullanım (Load Balancing, Caching)
*   PHP-FPM ile Entegrasyon (`fastcgi_pass`)
*   HTTPS/TLS (SSL) Yapılandırması (Let's Encrypt ile)
*   Yaygın `location` Direktifleri ve Eşleştirme Mantığı
*   Performans İpuçları
