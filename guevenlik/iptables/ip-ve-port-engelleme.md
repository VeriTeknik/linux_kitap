# IP ve Port Engelleme (iptables)

`iptables` kullanarak belirli IP adreslerinden, ağlardan veya belirli portlara yönelik gelen/giden trafiği engellemek mümkündür. Engelleme için genellikle `DROP` (paketi sessizce yok say) veya `REJECT` (paketi reddet ve kaynağa hata mesajı gönder) hedefleri kullanılır. `DROP` genellikle dışarıdan gelen istenmeyen trafik için tercih edilirken, `REJECT` bazen iç ağlarda veya giden trafiği engellerken daha bilgilendirici olabilir.

**Uyarı:** Kuralları eklerken dikkatli olun. Yanlış bir engelleme kuralı, meşru trafiği veya kendi erişiminizi engelleyebilir. Kuralların sırası önemlidir; genellikle engelleme kuralları, genel izin kurallarından önce gelmelidir (ancak `ESTABLISHED,RELATED` kuralı genellikle en başlarda yer alır).

## IP Adresi Engelleme

Belirli bir IP adresinden gelen tüm trafiği engellemek için `INPUT` zincirine bir kural eklenir:

```bash
# 1.2.3.4 IP adresinden gelen tüm paketleri düşür
sudo iptables -A INPUT -s 1.2.3.4 -j DROP 
```
*   `-A INPUT`: Kuralı INPUT zincirinin sonuna ekler. Daha önce eklenmiş bir `ACCEPT` kuralına takılmaması için, genellikle `-I INPUT 1` (veya uygun bir sıra numarası) ile zincirin başına yakın bir yere eklemek daha etkili olabilir (stateful kuraldan sonra).
*   `-s 1.2.3.4`: Kaynak (source) IP adresini belirtir.
*   `-j DROP`: Eşleşen paketleri düşürür.

Bir IP adresine giden trafiği engellemek için `OUTPUT` zinciri kullanılır (eğer varsayılan OUTPUT politikası `ACCEPT` ise):
```bash
# 1.2.3.4 IP adresine giden tüm paketleri reddet
sudo iptables -A OUTPUT -d 1.2.3.4 -j REJECT
```

## Alt Ağ (Subnet) Engelleme

Bir IP bloğunun tamamını engellemek için CIDR notasyonu kullanılır:
```bash
# 10.0.0.0/8 ağından gelen tüm paketleri düşür
sudo iptables -I INPUT -s 10.0.0.0/8 -j DROP 
```

## Port Engelleme

Belirli bir porta gelen veya giden trafiği engellemek için protokol (`-p tcp` veya `-p udp`) ve hedef/kaynak port (`--dport` veya `--sport`) belirtilir.

*   **Gelen Portu Engelleme:**
    ```bash
    # Sunucuya gelen tüm Telnet (TCP port 23) bağlantılarını reddet
    sudo iptables -A INPUT -p tcp --dport 23 -j REJECT

    # Belirli bir IP'den gelen SSH (TCP port 22) bağlantısını düşür
    sudo iptables -A INPUT -p tcp -s 5.6.7.8 --dport 22 -j DROP
    ```

*   **Giden Portu Engelleme:**
    ```bash
    # Sunucudan dışarıya yapılan tüm SMTP (TCP port 25) bağlantılarını düşür
    sudo iptables -A OUTPUT -p tcp --dport 25 -j DROP
    ```

## Kuralları Silme (`-D`)

Eklenmiş bir kuralı silmek için `-D` seçeneği kullanılır. Kural ya numarasıyla ya da tanımıyla belirtilir.

*   **Numara ile Silme:** Önce kuralları numaralarıyla listeleyin, sonra silin.
    ```bash
    # INPUT zincirindeki kuralları numaralarıyla listele
    sudo iptables -L INPUT --line-numbers 

    # INPUT zincirindeki 3 numaralı kuralı sil
    sudo iptables -D INPUT 3 
    ```
*   **Tanım ile Silme:** Kuralı eklerken kullanılan parametrelerin aynısını `-D` ile kullanarak silin.
    ```bash
    # Eklenen kural: sudo iptables -A INPUT -s 1.2.3.4 -j DROP
    # Silme komutu:
    sudo iptables -D INPUT -s 1.2.3.4 -j DROP
    ```

Unutmayın, bu komutlarla yapılan değişiklikler geçicidir. Kalıcı hale getirmek için [Temel İzinler](temel-izinler.md) bölümünde anlatılan `iptables-save` veya `iptables-persistent` gibi yöntemler kullanılmalıdır.
