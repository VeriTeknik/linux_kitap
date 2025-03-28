# Temel İzinler ve Stateful Firewalling (iptables)

`iptables` ile bir güvenlik duvarı yapılandırırken, genellikle temel bir strateji izlenir: varsayılan olarak her şeyi engellemek ve sadece ihtiyaç duyulan trafiğe izin vermek. Bu bölümde, yaygın olarak kullanılan temel izin kuralları ve stateful (durum bilgisi olan) firewalling konsepti ele alınacaktır.

**Uyarı:** `iptables` kurallarını ayarlarken dikkatli olun. Yanlış bir kural, sunucuya erişiminizi (özellikle SSH üzerinden) engelleyebilir. Kuralları uygulamadan önce test etmek veya fiziksel erişiminiz olduğundan emin olmak önemlidir. Yeni sistemlerde `firewalld` veya `ufw` gibi araçlar genellikle daha kolay bir yönetim sunar.

## 1. Varsayılan Politikaları Ayarlama (`-P`)

Güvenli bir başlangıç noktası, zincirler için varsayılan politikayı `DROP` (sessizce engelle) veya `REJECT` (hata mesajıyla engelle) olarak ayarlamaktır. Özellikle `INPUT` ve `FORWARD` zincirleri için bu önemlidir. `OUTPUT` zincirini `ACCEPT` olarak bırakmak genellikle daha pratiktir, ancak daha sıkı güvenlik isteniyorsa o da kısıtlanabilir.

```bash
# Mevcut kuralları ve sayaçları sıfırla (dikkatli olun!)
sudo iptables -F # Tüm zincirleri boşalt (Flush)
sudo iptables -X # Kullanıcı tanımlı zincirleri sil
sudo iptables -Z # Sayaçları sıfırla

# Varsayılan politikaları ayarla
sudo iptables -P INPUT DROP   # Gelen tüm paketleri varsayılan olarak engelle
sudo iptables -P FORWARD DROP # Yönlendirilen tüm paketleri varsayılan olarak engelle
sudo iptables -P OUTPUT ACCEPT  # Giden tüm paketlere varsayılan olarak izin ver
```
**Not:** Politikaları `DROP` yapmadan önce, en azından SSH gibi temel erişimlere izin veren kuralları eklediğinizden emin olun!

## 2. Loopback Trafiğine İzin Verme

Sistemin kendisiyle iletişim kurabilmesi (127.0.0.1 üzerinden) genellikle gereklidir. Loopback arayüzü (`lo`) üzerindeki tüm trafiğe izin verin:
```bash
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT 
```
*   `-A INPUT`: Kuralı INPUT zincirinin sonuna ekler.
*   `-i lo`: Kuralın sadece `lo` arayüzünden gelen paketler için geçerli olduğunu belirtir.
*   `-o lo`: Kuralın sadece `lo` arayüzünden çıkan paketler için geçerli olduğunu belirtir.
*   `-j ACCEPT`: Eşleşen pakete izin verir.

## 3. Stateful Firewalling (Durum Bilgisi Olan Güvenlik Duvarı)

Modern güvenlik duvarları genellikle "stateful" çalışır, yani mevcut bağlantıları takip ederler. Bir bağlantı kurulduktan sonra (örneğin, dışarıya bir web isteği gönderildiğinde), o bağlantıyla ilgili geri dönen yanıtlara otomatik olarak izin verilir. Bu, `conntrack` (connection tracking) modülü ile yapılır.

Gelen trafiğin, daha önce içeriden başlatılmış veya zaten kurulmuş bir bağlantının parçası olup olmadığını kontrol eden kural genellikle en başlara eklenir:
```bash
# Kurulmuş veya ilişkili bağlantılardan gelen paketlere izin ver
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT 
```
*   `-m state`: `state` modülünü kullanır.
*   `--state ESTABLISHED,RELATED`: Paketin durumunu kontrol eder.
    *   `ESTABLISHED`: Zaten kurulmuş bir bağlantının parçası olan paketler (örn. web sunucusundan gelen yanıt).
    *   `RELATED`: Kurulmuş bir bağlantıyla ilişkili yeni bir bağlantı başlatan paketler (örn. FTP veri bağlantısı, bazı ICMP hataları).

Bu tek kural, dışarıya başlattığınız bağlantıların (web gezintisi, DNS sorguları, güncellemeler vb.) geri dönen yanıtlarının otomatik olarak kabul edilmesini sağlar ve birçok `OUTPUT` kuralı tanımlama ihtiyacını ortadan kaldırır.

## 4. Belirli Gelen Servislere İzin Verme

Varsayılan `INPUT` politikası `DROP` olduğu için, dışarıdan erişilmesini istediğiniz servislere (portlara) açıkça izin vermeniz gerekir.

*   **SSH (Port 22):**
    ```bash
    sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -j ACCEPT 
    ```
    *   `-p tcp`: Protokolün TCP olduğunu belirtir.
    *   `--dport 22`: Hedef portun 22 olduğunu belirtir.
    *   `-m state --state NEW`: Sadece yeni bağlantı kurma isteklerini kabul eder (isteğe bağlı ama önerilir, `ESTABLISHED,RELATED` kuralı zaten diğerlerini kapsar).

*   **HTTP (Port 80):**
    ```bash
    sudo iptables -A INPUT -p tcp --dport 80 -m state --state NEW -j ACCEPT
    ```

*   **HTTPS (Port 443):**
    ```bash
    sudo iptables -A INPUT -p tcp --dport 443 -m state --state NEW -j ACCEPT
    ```

*   **Belirli IP'den SSH Erişimi:**
    ```bash
    sudo iptables -A INPUT -p tcp -s 1.2.3.4 --dport 22 -m state --state NEW -j ACCEPT 
    ```
    *   `-s 1.2.3.4`: Sadece `1.2.3.4` kaynak IP adresinden gelenlere izin verir.

## 5. ICMP (Ping) İzinleri

Ağ tanılama için `ping` (ICMP echo request) isteklerine izin vermek genellikle faydalıdır:
```bash
# Gelen ping isteklerine izin ver
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT 

# İlişkili ICMP mesajlarına zaten ESTABLISHED,RELATED kuralı izin verir
# (örn. echo-reply, destination-unreachable)
```

## Kuralları Kalıcı Hale Getirme

`iptables` komutu ile eklenen kurallar geçicidir ve sistem yeniden başlatıldığında kaybolur. Kuralları kalıcı hale getirmek için dağıtıma özgü yöntemler kullanılır:

*   **`iptables-save` ve `iptables-restore`:**
    Mevcut kuralları bir dosyaya kaydeder ve bu dosyadan geri yükler.
    ```bash
    # Kuralları kaydet
    sudo iptables-save > /etc/iptables/rules.v4 
    # IPv6 için: sudo ip6tables-save > /etc/iptables/rules.v6

    # Kuralları geri yükle (genellikle sistem başlangıcında yapılır)
    sudo iptables-restore < /etc/iptables/rules.v4
    # IPv6 için: sudo ip6tables-restore < /etc/iptables/rules.v6
    ```
*   **`iptables-persistent` (Debian/Ubuntu):**
    Bu paket, kuralları otomatik olarak kaydedip sistem başlangıcında yükleyen bir servis sağlar.
    ```bash
    sudo apt install iptables-persistent
    # Kurulum sırasında mevcut kuralları kaydetmek isteyip istemediğinizi sorar.
    # Sonrasında kuralları kaydetmek için:
    sudo netfilter-persistent save 
    ```
*   **`iptables` Servisi (Eski RHEL/CentOS):**
    `firewalld` yerine `iptables` servisi kullanılıyorsa, kurallar genellikle `/etc/sysconfig/iptables` dosyasına kaydedilir ve servis aracılığıyla yönetilir.
    ```bash
    sudo service iptables save 
    sudo systemctl enable iptables
    sudo systemctl start iptables
    ```

Bu temel kurallar, çoğu sunucu için makul bir başlangıç noktası oluşturur. İhtiyaçlara göre daha spesifik kurallar (belirli IP'leri engelleme, port yönlendirme vb.) eklenebilir.
