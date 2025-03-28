# IPTables

`iptables`, uzun yıllar boyunca Linux çekirdeğinin netfilter altyapısı için standart kullanıcı alanı (user-space) güvenlik duvarı yönetim aracı olmuştur. Paket filtreleme, NAT (Network Address Translation) ve paket manipülasyonu için kurallar tanımlamaya olanak tanır.

**Modern Alternatifler:**
Günümüzde `iptables`'ın yerini büyük ölçüde daha modern ve esnek olan **`nftables`** almaktadır. Çoğu yeni dağıtım `nftables`'ı varsayılan olarak kullanır, ancak geriye dönük uyumluluk için `iptables` komutlarını `nftables`'a çeviren bir katman (`iptables-nft`) sağlayabilirler. Ayrıca, `firewalld` ve `ufw` gibi daha kullanıcı dostu ön uç (front-end) araçlar, arka planda `iptables` veya `nftables` kullanarak güvenlik duvarı yönetimini basitleştirir.

Bu bölümde `iptables`'ın temel kavramları ve kullanımı, özellikle eski sistemlerle karşılaşıldığında veya düşük seviyeli kontrol gerektiğinde anlaşılması amacıyla ele alınacaktır. Yeni sistemlerde genellikle `firewalld` veya `ufw` kullanmak daha pratiktir.

## Temel Kavramlar

`iptables` kuralları, **tablolar (tables)** içinde gruplanmış **zincirler (chains)** üzerine eklenir. Bir paket çekirdeğe girdiğinde veya çekirdekten çıktığında, ilgili tablolardaki zincirlerden sırayla geçer ve eşleştiği ilk kurala göre işlem görür.

*   **Tablolar (Tables):** Farklı türdeki paket işleme görevleri için kullanılırlar. En yaygın tablolar şunlardır:
    *   `filter`: Varsayılan tablodur ve paket filtreleme (izin verme/engelleme) için kullanılır. En sık kullanılan tablodur.
    *   `nat`: Ağ Adresi Çevirisi (NAT) için kullanılır (örn. kaynak veya hedef IP/port değiştirme, masquerading).
    *   `mangle`: Paket başlıklarını değiştirmek gibi özel paket manipülasyonları için kullanılır.
    *   `raw`: Bağlantı izleme (connection tracking) mekanizmasından önce paketlere müdahale etmek için kullanılır.
*   **Zincirler (Chains):** Belirli bir noktada paketlerin geçtiği sıralı kurallar listesidir. Her tabloda önceden tanımlanmış zincirler bulunur:
    *   `filter` Tablosu Zincirleri:
        *   `INPUT`: Sunucunun kendisine gelen paketler için.
        *   `OUTPUT`: Sunucunun kendisinden çıkan paketler için.
        *   `FORWARD`: Sunucunun üzerinden geçip başka bir hedefe yönlendirilen paketler için (router/firewall olarak çalışıyorsa).
    *   `nat` Tablosu Zincirleri:
        *   `PREROUTING`: Paketler sisteme girdikten hemen sonra, yönlendirme kararı verilmeden önce (genellikle DNAT için).
        *   `POSTROUTING`: Paketler sistemden çıkmadan hemen önce (genellikle SNAT/Masquerade için).
        *   `OUTPUT`: Yerel olarak oluşturulan paketlerin hedef adresi değiştirilecekse.
*   **Kurallar (Rules):** Bir zincir içindeki her bir kural, belirli kriterlere (kaynak/hedef IP, port, protokol vb.) uyan paketleri tanımlar ve bu paketlere ne yapılacağını belirten bir **hedef (target)** içerir.
*   **Hedefler (Targets):** Bir kural eşleştiğinde pakete ne olacağını belirtir:
    *   `ACCEPT`: Paketin geçmesine izin verilir.
    *   `DROP`: Paket sessizce düşürülür (kaynağa bilgi gönderilmez).
    *   `REJECT`: Paket engellenir ve kaynağa bir hata mesajı (örn. ICMP "port unreachable") gönderilir.
    *   `LOG`: Paket hakkında log kaydı oluşturulur (genellikle `ACCEPT`, `DROP` veya `REJECT`'ten önce kullanılır).
    *   `MASQUERADE`: (Sadece `nat` tablosu, `POSTROUTING` zinciri) Kaynak IP adresini, paketin çıktığı arayüzün IP adresiyle değiştirir (dinamik IP'ler için SNAT).
    *   `DNAT`: (Sadece `nat` tablosu, `PREROUTING`/`OUTPUT` zincirleri) Hedef IP adresini/portunu değiştirir (port yönlendirme).
    *   `SNAT`: (Sadece `nat` tablosu, `POSTROUTING`/`INPUT` zincirleri) Kaynak IP adresini/portunu değiştirir (statik IP'ler için).
    *   Kullanıcı Tanımlı Zincirler: Kuralları gruplamak için kendi zincirlerinizi oluşturup bunlara atlayabilirsiniz.

## `iptables` Komutu

Kuralları yönetmek için `iptables` komutu kullanılır (`sudo` yetkisi gereklidir).

*   **Tablo Belirtme:** `-t <tablo_adı>` (örn. `-t nat`). Belirtilmezse varsayılan olarak `filter` tablosu kullanılır.
*   **Zincirleri Listeleme:** `-L [zincir_adı]` (örn. `iptables -L INPUT`). `-v` (verbose) ve `-n` (numeric) seçenekleri daha fazla detay gösterir.
    ```bash
    sudo iptables -L -v -n
    ```
*   **Kural Ekleme:** `-A <zincir>` (append - sona ekler), `-I <zincir> [kural_no]` (insert - başa veya belirtilen sıraya ekler).
*   **Kural Silme:** `-D <zincir> <kural_no>` (numarasına göre siler), `-D <zincir> <kural_tanımı>` (tanımına göre siler).
*   **Zinciri Boşaltma:** `-F [zincir_adı]` (flush - zincirdeki tüm kuralları siler).
*   **Zincir Politikasını Ayarlama:** `-P <zincir> <hedef>` (policy - zincirle eşleşen ama hiçbir kurala uymayan paketler için varsayılan hedefi belirler, örn. `iptables -P INPUT DROP`).
*   **Kural Kriterleri:**
    *   `-s <kaynak_ip/ağ>`: Kaynak adres.
    *   `-d <hedef_ip/ağ>`: Hedef adres.
    *   `-p <protokol>`: Protokol (`tcp`, `udp`, `icmp` vb.).
    *   `-i <arayüz>`: Gelen arayüz.
    *   `-o <arayüz>`: Giden arayüz.
    *   `--sport <port>`: Kaynak port (protokol `tcp` veya `udp` ise).
    *   `--dport <port>`: Hedef port (protokol `tcp` veya `udp` ise).
    *   `-m <modül>`: Ek eşleştirme modüllerini kullanır (örn. `-m state --state ESTABLISHED,RELATED`).
*   **Hedef Belirtme:** `-j <hedef>` (jump - örn. `-j ACCEPT`, `-j DROP`).

Bu bölümdeki diğer dosyalar, temel izinlerin nasıl ayarlanacağı ve IP/port engellemenin nasıl yapılacağı konusunda örnekler sunacaktır. `iptables` kurallarını kalıcı hale getirmek için genellikle `iptables-save` ve `iptables-restore` komutları veya dağıtıma özgü servisler (`iptables-persistent` vb.) kullanılır.
