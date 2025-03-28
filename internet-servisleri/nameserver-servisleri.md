# Nameserver (DNS) Servisleri

Alan Adı Sistemi (DNS - Domain Name System), internetin telefon rehberi gibidir; insanların okuyabildiği alan adlarını (`www.example.com`) bilgisayarların anlayabildiği IP adreslerine (`192.0.2.1`) çevirir. DNS servisleri temel olarak iki ana role ayrılır:

1.  **Yetkili (Authoritative) Sunucular:** Belirli bir alan adı (zone) hakkındaki kesin ve orijinal bilgileri (kayıtları) tutan sunuculardır. Bir alan adının sahibi tarafından yönetilirler. Örneğin, `example.com`'un yetkili sunucusu, `www.example.com`'un IP adresini bilir.
2.  **Önbellekleyici/Tekrarlayıcı (Caching/Recursive) Sunucular:** Kullanıcılardan veya uygulamalardan gelen DNS sorgularını alır ve yanıtı bulmak için hiyerarşik olarak diğer DNS sunucularını (kök sunucular, TLD sunucuları, yetkili sunucular) sorgular. Bulduğu yanıtı hem istemciye iletir hem de belirli bir süre (TTL - Time To Live) kendi önbelleğinde saklar. İnternet servis sağlayıcılarının sunduğu DNS sunucuları veya Google (`8.8.8.8`), Cloudflare (`1.1.1.1`) gibi genel DNS sunucuları bu tiptedir.

Bir sunucu her iki rolü de üstlenebilir (örn. BIND), ancak genellikle güvenlik ve performans nedenleriyle bu roller ayrılır. Örneğin, sadece yerel ağa hizmet veren bir caching/recursive sunucu (örn. Unbound) ve sadece belirli alan adları için dışarıya hizmet veren yetkili sunucular (örn. Knot DNS, PowerDNS, BIND) kullanmak yaygındır. Bu bölümde, hem yetkili hem de recursive özelliklere sahip olabilen **BIND (Berkeley Internet Name Domain)** yazılımı üzerinden temel kavramlar ve yapılandırma ele alınacaktır.

## DNS Kayıt Tipleri

Bir alan adı (zone) dosyası, o alan adı hakkındaki bilgileri içeren çeşitli **kayıt (record)** türlerinden oluşur. En sık kullanılanlar şunlardır:

| Kayıt Tipi | Açıklama                                                     | Örnek Değer (rackdc.com için) |
| :--------- | :----------------------------------------------------------- | :---------------------------- |
| **SOA**    | **Start of Authority:** Alan adının temel bilgilerini (yetkili sunucu, yönetici e-postası, seri numarası, zamanlama ayarları) içerir. Her zone dosyasında bir tane olmalıdır. | (Detayı aşağıda)              |
| **NS**     | **Name Server:** Alan adı için yetkili olan nameserver'ların adlarını belirtir. | `ns1.rackdc.com.`             |
| **A**      | **Address (IPv4):** Bir ana bilgisayar adını (hostname) bir IPv4 adresine eşler. | `rackdc.com. A 192.0.2.1`     |
| **AAAA**   | **Address (IPv6):** Bir ana bilgisayar adını bir IPv6 adresine eşler. | `rackdc.com. AAAA 2001:db8::1` |
| **CNAME**  | **Canonical Name:** Bir alan adına takma ad (alias) tanımlar. Sorgu yapıldığında, CNAME'in işaret ettiği asıl isme yönlendirme yapılır ve onun kaydı (genellikle A veya AAAA) döndürülür. Aşırı kullanımı ek sorgulara neden olabilir. | `www CNAME rackdc.com.`       |
| **MX**     | **Mail Exchanger:** Alan adına gönderilen e-postaları kabul edecek posta sunucularını ve önceliklerini belirtir (düşük sayı daha yüksek öncelik). Değer olarak IP değil, hostname kullanılmalıdır. | `rackdc.com. MX 10 mail.rackdc.com.` |
| **TXT**    | **Text:** Alan adıyla ilgili rastgele metin bilgileri saklamak için kullanılır. Özellikle SPF (Sender Policy Framework), DKIM (DomainKeys Identified Mail) gibi e-posta doğrulama mekanizmaları ve alan adı sahipliği doğrulama için yaygın olarak kullanılır. | `rackdc.com. TXT "v=spf1 include:_spf.google.com ~all"` |
| **SRV**    | **Service Locator:** Belirli bir servis için sunucu ve port bilgilerini bulmak için kullanılır (örn. SIP, LDAP, Kerberos). |                               |
| **PTR**    | **Pointer:** Bir IP adresini bir ana bilgisayar adına eşler (Ters DNS çözümlemesi - Reverse DNS). Genellikle IP adres bloklarının sahibi tarafından özel `in-addr.arpa` (IPv4) veya `ip6.arpa` (IPv6) zone'larında tanımlanır. | `1.2.0.192.in-addr.arpa. PTR server.example.com.` |

**Not:** Alan adlarının sonundaki `.` (nokta) işareti, alan adının tam ve kök dizinden itibaren belirtildiğini ifade eder (Fully Qualified Domain Name - FQDN). Eğer sonda nokta yoksa, BIND genellikle zone'un kendi adını (SOA kaydındaki) sona ekleyerek adı tamamlamaya çalışır. Örneğin, `rackdc.com.` zone dosyasında `www` yazmak `www.rackdc.com.` anlamına gelirken, `mail.example.com.` yazmak tam olarak o adresi ifade eder.

## BIND Kurulumu ve Yapılandırması

BIND DNS sunucusu (genellikle `bind` veya `bind9` paket adıyla) çoğu Linux dağıtımının standart depolarında bulunur.

```bash
# RHEL/CentOS/Fedora
sudo dnf install bind bind-utils -y 

# Debian/Ubuntu
sudo apt update && sudo apt install bind9 bind9utils bind9-doc -y
```
(`bind-utils` paketi `dig`, `nslookup` gibi araçları içerir).

Ana yapılandırma dosyası genellikle `/etc/named.conf` (RHEL tabanlı) veya `/etc/bind/named.conf` (Debian tabanlı)'dır. Zone dosyaları ise `/var/named/` (RHEL) veya `/etc/bind/` (Debian) gibi dizinlerde tutulabilir.

### `named.conf` Temel Ayarları

`named.conf` dosyası, BIND'ın genel çalışma şeklini, hangi zone'lardan sorumlu olduğunu ve güvenlik ayarlarını belirler.

*   **ACL (Access Control List):** Belirli IP adreslerini veya ağları gruplamak için kullanılır. Genellikle `allow-recursion` veya `allow-transfer` gibi direktiflerde kullanılır.
    ```
    acl "trusted_clients" {
        localhost;       # Sunucunun kendisi
        192.168.1.0/24;  # Yerel ağ
        <diger_guvenilir_ip>;
    };

    acl "slave_servers" {
        <slave_sunucu_1_ip>;
        <slave_sunucu_2_ip>;
    };
    ```
*   **Options Bloğu:** Genel ayarları içerir.
    ```
    options {
        directory "/var/named"; // Zone dosyalarının bulunduğu dizin (RHEL)
        // directory "/etc/bind"; // (Debian)

        // Hangi IP ve portlarda dinlenecek?
        listen-on port 53 { localhost; 192.168.1.10; }; // Sadece localhost ve belirli bir IP
        // listen-on port 53 { any; }; // Tüm IPv4 arayüzleri (dikkatli kullanın!)
        listen-on-v6 port 53 { ::1; }; // Sadece IPv6 localhost
        // listen-on-v6 port 53 { any; }; // Tüm IPv6 arayüzleri

        // Kimler recursive sorgu yapabilir? (Sadece caching/recursive sunucu ise)
        allow-recursion { trusted_clients; }; 
        // Yetkili sunucu ise genellikle: allow-recursion { none; };

        // Kimler zone transferi yapabilir? (Master sunucu ise)
        allow-transfer { slave_servers; }; 
        // Slave veya sadece caching ise: allow-transfer { none; };

        // Güvenlik ve performans ayarları
        dnssec-validation auto; // DNSSEC doğrulaması (recursive sunucu için)
        // dnssec-enable yes; // DNSSEC'i etkinleştir (yetkili sunucu için)
        
        version "Not Available"; // Versiyon bilgisini gizle
        recursion yes; // Recursive sorgulara izin ver (caching/recursive ise)
        // recursion no; // (Yetkili sunucu ise)
    };
    ```
    **Güvenlik Notları:**
    *   `listen-on`: Sadece gerekli IP adreslerinde dinleme yapın. `any` kullanmaktan kaçının veya firewall ile koruyun.
    *   `allow-recursion`: İnternete açık bir sunucuda `any` olarak bırakmak, sunucunuzun DNS amplification saldırılarında kullanılmasına yol açabilir. Sadece güvendiğiniz IP'lere izin verin veya `none` yapın.
    *   `allow-transfer`: Zone transferini sadece slave sunucularınıza izin verin. `none` olarak bırakmak slave'lerin zone'u çekmesini engeller.
*   **Zone Tanımları:** Sunucunun yetkili olduğu (master veya slave) veya özel olarak ele aldığı (örn. root hints) zone'ları tanımlar.
    ```
    // Kök sunucu bilgilerini içeren dosya (hint zone)
    zone "." IN {
        type hint;
        file "named.ca"; // veya named.root
    };

    // Localhost için gerekli zone'lar
    zone "localhost" IN {
        type master;
        file "named.localhost";
        allow-update { none; };
    };
    zone "1.0.0.127.in-addr.arpa" IN {
        type master;
        file "named.loopback";
        allow-update { none; };
    };

    // Yönetilen bir alan adı için master zone tanımı
    zone "rackdc.com" IN {
        type master;
        file "rackdc.com.db"; // Zone dosyasının adı (/var/named/ veya /etc/bind/ altında)
        allow-transfer { slave_servers; }; // Slave'lere transfer izni
        // notify yes; // Değişiklik olduğunda slave'lere haber ver (varsayılan)
    };

    // Başka bir sunucudan çekilen slave zone tanımı
    zone "ornekalanadi.com" IN {
        type slave;
        file "slaves/ornekalanadi.com.db"; // Slave zone dosyasının kaydedileceği yer
        masters { <master_sunucu_ip>; }; // Master sunucunun IP adresi
        // allow-notify { <master_sunucu_ip>; }; // Sadece master'dan gelen notify'ları kabul et
    };
    ```

### Zone Dosyası Örneği (`/var/named/rackdc.com.db`)

```
$TTL 14400      ; Varsayılan TTL (Time To Live) - 4 saat
@       IN      SOA     ns1.rackdc.com. hostmaster.rackdc.com. (
                        2023102701    ; Serial (YYYYMMDDNN formatı - HER DEĞİŞİKLİKTE ARTIRILMALI!)
                        7200          ; Refresh (Slave'lerin ne sıklıkla kontrol edeceği - 2 saat)
                        3600          ; Retry (Başarısız kontrolden sonra tekrar deneme süresi - 1 saat)
                        1209600       ; Expire (Slave'in master'a ulaşamazsa veriyi ne kadar geçerli sayacağı - 2 hafta)
                        3600          ; Minimum TTL (Negatif cevapların önbellekte kalma süresi - 1 saat)
                        )

; Name Server (NS) Kayıtları - Bu alan adı için yetkili sunucular
@               IN      NS      ns1.rackdc.com.
@               IN      NS      ns2.rackdc.com.

; NS kayıtlarına karşılık gelen A/AAAA (Glue) Kayıtları (Eğer NS'ler aynı domaindeyse)
ns1             IN      A       192.0.2.1
ns1             IN      AAAA    2001:db8::1
ns2             IN      A       192.0.2.2
ns2             IN      AAAA    2001:db8::2

; Mail Exchanger (MX) Kayıtları - E-postaları kabul edecek sunucular
@               IN      MX      10 mail.rackdc.com.
@               IN      MX      20 backupmail.rackdc.com.

; Host (A/AAAA) Kayıtları
@               IN      A       192.0.2.10      ; Alan adının kendisi (rackdc.com)
@               IN      AAAA    2001:db8::10
www             IN      A       192.0.2.10      ; www.rackdc.com
www             IN      AAAA    2001:db8::10
mail            IN      A       192.0.2.20      ; mail.rackdc.com
backupmail      IN      A       192.0.2.21      ; backupmail.rackdc.com
ftp             IN      CNAME   www             ; ftp.rackdc.com, www ile aynı IP'ye yönlenir

; TXT Kayıtları (Örnek: SPF)
@               IN      TXT     "v=spf1 mx -all" 
```
**Önemli:** Zone dosyasında her değişiklik yapıldığında, SOA kaydındaki **seri numarası mutlaka artırılmalıdır**. Aksi takdirde slave sunucular değişikliği algılamaz ve zone transferi yapmaz. YYYYMMDDNN formatı yaygın bir pratiktir.

## Servis Yönetimi

BIND servisi (genellikle `named` veya `bind9`) systemd ile yönetilir:

```bash
# Servisi başlat
sudo systemctl start named 

# Servisi durdur
sudo systemctl stop named

# Servisi yeniden başlat
sudo systemctl restart named

# Sistem başlangıcında otomatik çalışmasını sağla
sudo systemctl enable named

# Sistem başlangıcında otomatik çalışmasını engelle
sudo systemctl disable named

# Servisin durumunu kontrol et
sudo systemctl status named
```

Zone dosyalarında değişiklik yaptıktan sonra BIND'ın bu değişiklikleri yeniden yüklemesi için `rndc reload` komutu kullanılır (servisi yeniden başlatmaya gerek kalmaz):
```bash
sudo rndc reload
# veya belirli bir zone için:
# sudo rndc reload rackdc.com
```
`named.conf` dosyasında yapılan değişiklikler için ise genellikle servisi yeniden başlatmak (`sudo systemctl restart named`) gerekir. Yapılandırma dosyasının doğruluğunu kontrol etmek için `named-checkconf` komutu kullanılabilir. Zone dosyalarının doğruluğunu kontrol etmek için `named-checkzone <zone_adı> <zone_dosyası>` komutu kullanılır.
