# Tarih ve Saat Ayarlama

## Sistem Saati

Linux üzerinde tarih ve saat ayarlama işlemleri **date** komutu ile gerçekleştirilir. Komutu tek başına yazdığınızda tarih ve saati çıktı olarak verir.

```bash
date
Sun Nov 15 19:22:23 EET 2015
```

date komutu ile istediğimiz biçimde çıktı alabiliriz.

```bash
date +%d_%m_%Y
15_11_2015
```

Daha kompleks işlemler için boşluklu parametreleri çift tırnak içinde birleştirip tek parametre olarak programa sunmak gerekir.

```bash
date +"Ay:%m / Yıl:%y"
Ay:11 / Yıl:15
```

Saati ve tarihi ayarlamak içinse aşağıdaki gibi bir yol izlenebilir.

```bash
date -s "2 NOV 2014 17:45:12"
Sun Nov 2 17:45:12 EET 2014
```

Sadece saati değiştirmek için,

```bash
date -s "15:12:13"
Sun Nov 2 15:12:13 EET 2014
```

Veya dilediğiniz biçimde tarih belirtmek için,

```bash
date +%Y%m%d%T -s "20120423 09:15:12"
```

## Fiziksel Saat

Sistem üzerine yazılan saatler, bilgisayarın pil ile tutulan fiziksel saatine doğrudan kaydedilmez. Fiziksel saatteki bilgiye erişmek için **hwclock** komutu kullanılmalıdır.

Fiziksel saatteki bilgiyi okumak için

```bash
hwclock -r
Sun 15 Nov 2015 07:37:57 PM EET  -0.960295 seconds
```

Yukarıdaki çıktıda -0.960295 saniye'lik bir fark görünüyor. Bu komutu verdiğimiz an ile, cihazdan okuduğumuz saat arasındaki fark. Kısacası cihaz bize neredeyse 1 saniye içerisinde cevap vermiş. Bu süre zarfında hwclock komutunun yaptıklarını daha detaylı görmek için **-D** (debug) parametresiyle çalıştırabilirsiniz.

```bash
hwclock -D
hwclock from util-linux 2.20.1
Using /dev interface to clock.
Last drift adjustment done at 1435275720 seconds after 1969
Last calibration done at 1435275720 seconds after 1969
Hardware clock is on UTC time
Assuming hardware clock is kept in UTC time.
Waiting for clock tick...
...got clock tick
Time read from Hardware Clock: 2015/11/15 17:39:39
Hw clock time : 2015/11/15 17:39:39 = 1447609179 seconds since 1969
Sun 15 Nov 2015 07:39:39 PM EET  -0.969248 seconds
```

Fiziksel saatteki değeri okuyup, sistem saatinizi bununla güncellemek isteyebilirsiniz. Aslında bilgisayarlarınızın açılışta yaptığı bir işlemdir bu. Böylelikle sisteminize hiç enerji verilmediği durumlarda saat pili ile sistem geçen zamanı tutmaya devam eder, sisteminiz tekrar açıldığında bu cihazdan (RTC: Real Time Clock) saati okur ve kendi tarihini bu değerle günceller.

```bash
hwclock -s
```

Öte yandan, **date** komutu ile sistem saatini güncellemeyi öğrenmiştik. Bu komut ile sistem saatinizi güncelledikten sonra fiziksel saatin değerinin değişmemesi normal. Eğer fiziksel saatin değerini de sistem saatinizdekine ayarlamak isterseniz, bu bilgiyi RTC modülüne yazmanız gerekir.

```bash
hwclock -w
```

## NTP ile Zaman Senkronizasyonu (Modern Yöntemler)

NTP (Network Time Protocol), ağ üzerindeki sunuculardan hassas zaman bilgisi alarak sistem saatini doğru tutmak için kullanılan standart protokoldür. Modern Linux dağıtımları genellikle zaman senkronizasyonu için `systemd-timesyncd` veya `chrony` servislerini kullanır. `ntpdate` komutu çoğu sistemde artık önerilmemektedir (deprecated).

### systemd-timesyncd

Birçok modern dağıtımda varsayılan olarak gelen basit bir NTP istemcisidir. Genellikle ek kurulum gerektirmez.

**Durumu Kontrol Etme:**
`timedatectl status` komutu ile hem zaman ayarlarını hem de NTP senkronizasyon durumunu görebilirsiniz:
```bash
timedatectl status
               Local time: Cum 2025-03-28 02:05:10 +03
           Universal time: Per 2025-03-27 23:05:10 UTC
                 RTC time: Per 2025-03-27 23:05:10
                Time zone: Europe/Istanbul (+03, +0300)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no 
```
`System clock synchronized: yes` ve `NTP service: active` satırları senkronizasyonun çalıştığını gösterir.

**Etkinleştirme/Devre Dışı Bırakma:**
`timedatectl` komutu ile NTP senkronizasyonunu açıp kapatabilirsiniz:
```bash
# NTP senkronizasyonunu etkinleştir
sudo timedatectl set-ntp true

# NTP senkronizasyonunu devre dışı bırak
sudo timedatectl set-ntp false 
```
`systemd-timesyncd` servisi genellikle `/etc/systemd/timesyncd.conf` dosyasından yapılandırılır ve varsayılan olarak dağıtımın belirlediği NTP sunucu havuzlarını (pool) kullanır.

### chrony

`ntpd`'ye göre daha modern, hızlı ve esnek bir NTP istemcisi ve sunucusudur. Özellikle sık sık uyku moduna giren veya ağ bağlantısı kararsız olan sistemler için daha uygundur. Bazı dağıtımlar (örneğin RHEL/CentOS 7 ve sonrası) varsayılan olarak `chrony` kullanır.

**Kurulum (Gerekliyse):**
```bash
# Debian/Ubuntu
sudo apt install chrony

# RHEL/CentOS/Fedora
sudo dnf install chrony 
```

**Durumu Kontrol Etme:**
`chronyc` komutu ile senkronizasyon durumu ve kaynaklar hakkında detaylı bilgi alınabilir:
```bash
chronyc sources -v
```
```bash
chronyc tracking
```

**Yapılandırma:**
`chrony` genellikle `/etc/chrony/chrony.conf` (veya `/etc/chrony.conf`) dosyasından yapılandırılır. Kullanılacak NTP sunucuları bu dosyada `server` veya `pool` direktifleri ile belirtilir.

**Servis Yönetimi:**
`chrony` bir sistem servisidir ve `systemctl` ile yönetilir:
```bash
sudo systemctl start chronyd
sudo systemctl enable chronyd
sudo systemctl status chronyd
```

**Önemli Not:** Sistemde aynı anda sadece bir NTP istemcisinin (örneğin `systemd-timesyncd` veya `chrony` veya eski `ntpd`) aktif olması önerilir. Çakışmaları önlemek için kullanılmayacak olan servisleri devre dışı bırakmak gerekir.

Zaman senkronizasyonu yapıldıktan sonra, sistem saatindeki değişikliğin donanım saatine (RTC) yazılması genellikle bu servisler tarafından otomatik olarak veya periyodik olarak yapılır. Ancak manuel olarak yapmak isterseniz `hwclock -w` komutunu kullanabilirsiniz.

## Saat Diliminin Ayarlanması

Sistem saat dilimi (timezone), sistemin yerel saati doğru göstermesi için önemlidir.

### timedatectl ile Ayarlama (Önerilen Yöntem)

Modern systemd tabanlı sistemlerde saat dilimini ayarlamanın en kolay ve önerilen yolu `timedatectl` komutunu kullanmaktır.

**Mevcut Saat Dilimini Görme:**
```bash
timedatectl status | grep "Time zone"
# veya sadece:
timedatectl
```

**Kullanılabilir Saat Dilimlerini Listeleme:**
```bash
timedatectl list-timezones
# Belirli bir bölgeyi filtrelemek için grep kullanılabilir:
timedatectl list-timezones | grep Europe
timedatectl list-timezones | grep Istanbul
```

**Saat Dilimini Ayarlama:**
Örneğin, saat dilimini İstanbul olarak ayarlamak için:
```bash
sudo timedatectl set-timezone Europe/Istanbul
```
Bu komut, gerekli sembolik linki (`/etc/localtime` -> `/usr/share/zoneinfo/...`) otomatik olarak oluşturur veya günceller.

### Manuel Yöntem (Eski veya systemd olmayan sistemler)

`timedatectl` komutunun bulunmadığı sistemlerde veya manuel olarak yapmak istenirse, `/etc/localtime` dosyası, `/usr/share/zoneinfo` altındaki doğru saat dilimi dosyasına işaret eden bir sembolik link olarak ayarlanır.

Önce mevcut link (varsa) kaldırılır, sonra yenisi oluşturulur:
```bash
# Önce mevcut linki kaldır (varsa)
sudo rm /etc/localtime

# Yeni sembolik linki oluştur (örnek: İstanbul)
sudo ln -s /usr/share/zoneinfo/Europe/Istanbul /etc/localtime 
```
Bazı eski sistemlerde `/etc/timezone` gibi metin tabanlı bir yapılandırma dosyası da bulunabilir ve bunun da güncellenmesi gerekebilir (örneğin içine `Europe/Istanbul` yazmak). Ancak modern sistemlerde genellikle sadece `/etc/localtime` linki yeterlidir.

`/etc/localtime` dosyası, saat dilimi kurallarını içeren _binary_ bir dosyaya işaret eden bir sembolik linktir. Bu dosyanın doğrudan kopyalanması yerine sembolik link kullanılması, `tzdata` paketi güncellendiğinde saat dilimi kurallarının da otomatik olarak güncellenmesini sağlar. `/etc/localtime` dosyasının veya linkinin olmaması durumunda, sistem genellikle saat dilimini UTC (GMT+0) olarak varsayar.
