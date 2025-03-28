# Ortak Network Dosyaları

Her ne kadar dağıtımların network ayarlarını yaparken farklılıklar olduğunu gördüysek de, bazı dosyalar tüm sistemlerde aynı işlevi görmektedir. Bu bölümde bu dosyaları inceleyeceğiz.

## hostname

Sistemin kendi ismini bilmesi için düzenlenmesi gereken iki dosyadan biridir. `/etc/hostname` yolundadır. Bu dosyanın içinde tek satır bulunur.

```bash
eaydin@dixon ~ $ cat /etc/hostname
dixon
```

Bu dosyanın düzenlenmesi eskisi kadar önem arz etmemektedir, aşağıdaki dosyalar daha kritiktir.

## hosts

Sistemin adreslere karşılık isim tuttuğu dosyadır.

```bash
eaydin@dixon ~ $ cat /etc/hosts
127.0.0.1    localhost
127.0.1.1    dixon

94.103.47.66 test-centos1 
94.103.47.78 test-debian1

94.103.33.130 plugged.in
```

Yukarıdan görüleceği gibi, sistemin localhost'u tanıması için 127.0.0.1'e işaret etmesi gerekir. Benzer şekilde hostname'de tanımladığımız değer de burada belirtilmelidir.

Alttaki satırlar ise kullanıcının kendince yarattığı kısa yollardır. Burada gördüğünüz plugged.in değeri sitenin gerçek adresidir. `/etc/hosts` dosyasında yer alması, herhangi bir şekilde DNS'lerden alınan bilginin önüne geçilmesi anlamına gelir. Kısacası artık sistem üzerinde her program plugged.in adresine gitmek için 94.103.33.130 IP'sini kullanacaktır. Eğer buraya başka bir değer, örneğin 94.103.32.32 yazsaydık, sistem üzerinde her program plugged.in'e gidecekken DNS'lere bakmadan 94.103.32.32'ye yönlenecekti, ve veritech.net sayfası cevap verecekti.

host dosyası manual dosyasına erişmek için

```bash
man 5 hosts
```

## /etc/nsswitch.conf

Bu dosya, sistemin isim çözme (örneğin, ana bilgisayar adlarını IP adreslerine çevirme) gibi işlemler için hangi kaynakları (örn. `files` - `/etc/hosts`, `dns`) ve hangi sırayla kullanacağını belirler. Özellikle `hosts:` satırı önemlidir:
```
# Örnek /etc/nsswitch.conf satırı
hosts:      files dns myhostname
```
Bu satır, bir ana bilgisayar adı çözümlenirken önce `/etc/hosts` dosyasına (`files`), sonra DNS sunucularına (`dns`), sonra da sistemin kendi ana bilgisayar adına (`myhostname`) bakılacağını belirtir.

## /etc/resolv.conf

Bu dosya, sistemin alan adı çözümlemesi (DNS) için kullanacağı nameserver (DNS sunucusu) adreslerini ve alan adı arama listesini (search domain) tanımlamak için kullanılır.

**Ancak Dikkat:** Modern Linux sistemlerinde `/etc/resolv.conf` dosyası genellikle **doğrudan düzenlenmez**. Çoğu zaman bu dosya, NetworkManager veya `systemd-resolved` gibi ağ yönetim servisleri tarafından **otomatik olarak oluşturulur ve yönetilir**. Manuel olarak yapılan değişiklikler, servis yeniden başladığında veya ağ bağlantısı değiştiğinde kaybolabilir.

**Yapısı:**
Eğer manuel olarak düzenleniyorsa veya otomatik olarak oluşturulduğunda, genellikle aşağıdaki gibi görünür:

```
# Otomatik olarak NetworkManager veya systemd-resolved tarafından oluşturulmuş olabilir
search example.com internal.example.com # Alan adı arama listesi
nameserver 8.8.8.8                     # Birincil DNS sunucusu
nameserver 8.8.4.4                     # İkincil DNS sunucusu
```
*   `nameserver <IP_adresi>`: Kullanılacak DNS sunucusunun IP adresini belirtir. Genellikle birden fazla `nameserver` satırı bulunabilir (yedeklilik için).
*   `search <alan_adı1> <alan_adı2> ...`: Tam olmayan ana bilgisayar adları çözümlenirken denenecek alan adlarının listesini belirtir. Örneğin, `ping server1` komutu çalıştırıldığında sistem önce `server1.example.com`'u, sonra `server1.internal.example.com`'u çözmeye çalışır.
*   `domain <alan_adı>`: Sistemin yerel alan adını belirtir. `search` listesindeki ilk alan adı olarak davranır. Genellikle `search` veya `domain` direktiflerinden sadece biri kullanılır.

**Modern Sistemlerde Yönetim:**

*   **NetworkManager:** DHCP'den alınan veya bağlantı ayarlarında statik olarak belirtilen DNS sunucularını kullanarak `/etc/resolv.conf` dosyasını (genellikle `systemd-resolved` aracılığıyla veya doğrudan) yönetir. DNS ayarlarını değiştirmek için `nmcli` veya `nmtui` kullanılmalıdır.
*   **systemd-resolved:** Bu servis aktif olduğunda, `/etc/resolv.conf` genellikle `/run/systemd/resolve/stub-resolv.conf` dosyasına bir sembolik link olur. Bu `stub-resolv.conf` dosyası sadece `127.0.0.53` adresini içerir. Gerçek DNS sunucuları `systemd-resolved` tarafından yönetilir ve `resolvectl status` komutu ile görülebilir. DNS sunucuları `systemd-networkd` yapılandırması (`.network` dosyaları) veya NetworkManager aracılığıyla `systemd-resolved`'a iletilir.

Özetle, `/etc/resolv.conf` dosyasının içeriğini anlamak önemli olsa da, modern sistemlerde bu dosyayı doğrudan düzenlemek yerine, ağı yöneten servisin (NetworkManager, systemd-networkd) yapılandırma araçlarını kullanmak gerekir.

Detaylı bilgi için `resolv.conf(5)` ve `nsswitch.conf(5)` man sayfalarına bakılabilir.
