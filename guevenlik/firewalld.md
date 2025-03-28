# `firewalld` ile Güvenlik Duvarı Yönetimi

`firewalld`, modern RHEL tabanlı dağıtımlarda (RHEL 7+, CentOS 7+, Fedora) ve bazı diğer dağıtımlarda kullanılan dinamik bir güvenlik duvarı (firewall) yönetim aracıdır. Arka planda `nftables` (veya eski sürümlerde `iptables`) kullanarak ağ trafiğini filtreler, ancak `iptables` komutlarına göre daha kullanıcı dostu ve dinamik bir yapılandırma sunar. Özellikle ağ bağlantılarının sık değişebildiği (örn. farklı Wi-Fi ağları) veya farklı güvenlik seviyelerine sahip ağ arayüzlerinin olduğu durumlar için tasarlanmıştır.

## Temel Kavramlar

*   **Zone (Bölge):** `firewalld`'nin temel konseptidir. Ağ bağlantıları veya kaynak IP adresleri için farklı güven seviyelerini temsil eden önceden tanımlanmış veya özel olarak oluşturulmuş bölgelerdir. Her ağ arayüzü sadece bir bölgeye atanabilir. Gelen trafiğe uygulanacak kurallar, trafiğin geldiği kaynağın veya arayüzün atandığı bölgeye göre belirlenir. Yaygın bölgeler:
    *   `public`: Güvenilmeyen genel ağlar için varsayılan bölge. Genellikle sadece seçili gelen bağlantılara izin verilir.
    *   `internal`: İç ağdaki diğer bilgisayarlar için daha fazla güven varsayılan bölge.
    *   `dmz`: Dışarıya açık servisler (web sunucusu vb.) için kullanılan, iç ağdan izole edilmiş bölge (Demilitarized Zone).
    *   `trusted`: Ağdaki tüm cihazlara tamamen güvenilen bölge (dikkatli kullanılmalıdır!).
    *   `block`: Gelen tüm bağlantıları ICMP "host prohibited" mesajı ile reddeder.
    *   `drop`: Gelen tüm bağlantıları sessizce düşürür (yanıt vermez).
*   **Services (Servisler):** Belirli bir ağ hizmeti için gerekli olan standart port ve protokol tanımlarını içeren önceden tanımlanmış isimlerdir (örn. `ssh`, `http`, `https`, `smtp`). Bir bölgede bir servise izin vermek, o servisin kullandığı portları otomatik olarak açar.
*   **Ports:** Belirli bir TCP veya UDP portuna doğrudan izin vermek veya engellemek için kullanılır.
*   **Runtime vs. Permanent:** `firewall-cmd` ile yapılan değişiklikler varsayılan olarak sadece o anki çalışma durumunu (runtime) etkiler ve servis yeniden başlatıldığında veya sistem yeniden başladığında kaybolur. Değişiklikleri kalıcı hale getirmek için komutlara `--permanent` seçeneği eklenmeli ve ardından `firewall-cmd --reload` komutu ile kalıcı yapılandırma çalışma zamanına yüklenmelidir.

## Kurulum ve Servis Yönetimi

`firewalld` genellikle RHEL tabanlı modern dağıtımlarda varsayılan olarak kuruludur.

```bash
# Kurulum (gerekirse)
sudo dnf install firewalld -y

# Servisi başlatma
sudo systemctl start firewalld

# Sistem başlangıcında otomatik çalışmasını sağlama
sudo systemctl enable firewalld

# Servisin durumunu kontrol etme
sudo systemctl status firewalld 
# veya
sudo firewall-cmd --state 
```

## `firewall-cmd` ile Yönetim

`firewalld`'yi yönetmek için ana komut satırı aracı `firewall-cmd`'dir. Komutların çoğu `sudo` yetkisi gerektirir.

**Zone İşlemleri:**

```bash
# Tanımlı tüm bölgeleri listele
firewall-cmd --get-zones

# Aktif bölgeleri ve atanan arayüzleri/kaynakları listele
firewall-cmd --get-active-zones

# Varsayılan bölgeyi göster
firewall-cmd --get-default-zone

# Varsayılan bölgeyi değiştir (örn. internal yap)
sudo firewall-cmd --set-default-zone=internal

# Belirli bir arayüzün hangi bölgede olduğunu göster
firewall-cmd --get-zone-of-interface=eth0

# Bir arayüzü belirli bir bölgeye ata (kalıcı)
sudo firewall-cmd --zone=internal --change-interface=eth1 --permanent
# Değişikliği uygulamak için: sudo firewall-cmd --reload

# Belirli bir kaynak IP/Subnet'i bir bölgeye ata (kalıcı)
sudo firewall-cmd --zone=internal --add-source=192.168.1.0/24 --permanent
sudo firewall-cmd --reload
```

**Servis ve Port İşlemleri:**

Değişiklikler varsayılan olarak aktif bölgeye uygulanır. Farklı bir bölge için `--zone=<bölge_adı>` belirtilmelidir. Kalıcı değişiklikler için `--permanent` eklenmelidir.

```bash
# Aktif bölgedeki izin verilen servisleri/portları listele
firewall-cmd --list-all

# Belirli bir bölgedeki servisleri listele
firewall-cmd --zone=public --list-services

# Tanımlı tüm servisleri listele
firewall-cmd --get-services

# HTTP servisine izin ver (geçici)
sudo firewall-cmd --add-service=http

# HTTP servisine kalıcı olarak izin ver
sudo firewall-cmd --permanent --add-service=http

# 8080 TCP portuna izin ver (kalıcı)
sudo firewall-cmd --permanent --add-port=8080/tcp

# Bir servisi kaldır (kalıcı)
sudo firewall-cmd --permanent --remove-service=http

# Bir portu kaldır (kalıcı)
sudo firewall-cmd --permanent --remove-port=8080/tcp

# Kalıcı değişiklikleri uygula
sudo firewall-cmd --reload 
```

**Diğer Komutlar:**

*   **Rich Rules:** Daha karmaşık kurallar (belirli IP'den belirli porta izin verme/engelleme, loglama vb.) tanımlamak için kullanılır.
    ```bash
    # Örnek: 10.0.0.5 IP'sinden gelen SSH bağlantılarını kabul et
    sudo firewall-cmd --permanent --zone=public --add-rich-rule='rule family="ipv4" source address="10.0.0.5/32" service name="ssh" accept'
    sudo firewall-cmd --reload
    ```
*   **Panic Mode:** Tüm gelen ve giden trafiği acil durumlarda engellemek için kullanılır.
    ```bash
    # Panik modunu aç
    sudo firewall-cmd --panic-on
    # Panik modunu kapat
    sudo firewall-cmd --panic-off
    # Panik modunun durumunu sorgula
    sudo firewall-cmd --query-panic
    ```

## Grafiksel Arayüz (`firewall-config`)

`firewall-config` paketi, `firewalld` için bir grafiksel yapılandırma aracı sunar. Komut satırına alternatif olarak kullanılabilir.
```bash
sudo dnf install firewall-config -y
firewall-config 
```

`firewalld`, özellikle dinamik ortamlarda ve farklı güven seviyelerine sahip ağlar için esnek bir güvenlik duvarı çözümü sunar. Zone tabanlı yaklaşımı, kuralların yönetimini basitleştirmeyi hedefler.
