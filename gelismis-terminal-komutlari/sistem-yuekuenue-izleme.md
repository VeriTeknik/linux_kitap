# Sistem Yükünü İzleme

## top

Linux üzerinde sistem kaynaklarınızı ve çalışan işlemleri gerçek zamanlı olarak izlemek için kullanılan temel programlardan birisi **top** programıdır. Varsayılan olarak işlemleri CPU kullanımına göre sıralı olarak gösterir.

**`top` Ekranını Anlamak:**

*   **İlk Satır:** `uptime` komutunun çıktısına benzer bilgiler içerir: mevcut saat, sistemin ne kadar süredir çalıştığı (up time), kaç kullanıcının bağlı olduğu ve sistem yük ortalamaları (load average) (son 1, 5 ve 15 dakika için). Yük ortalaması, çalışmak için bekleyen veya çalışan işlem sayısının bir ölçüsüdür; 1.00 değeri tek çekirdekli bir CPU'nun tam kapasite çalıştığını gösterir. Çok çekirdekli sistemlerde bu değer çekirdek sayısı ile orantılı olarak artabilir.
*   **İkinci Satır (Tasks):** Toplam işlem sayısı ve durumlarına göre (çalışan, uyuyan, durmuş, zombi) dağılımı.
*   **Üçüncü Satır (%Cpu(s)):** CPU kullanımının farklı modlardaki yüzdesi: `us` (user space), `sy` (system/kernel space), `ni` (nice değeri değiştirilmiş user space), `id` (idle/boşta), `wa` (I/O bekleme), `hi` (hardware interrupts), `si` (software interrupts), `st` (steal time - sanal makinelerde).
*   **Dördüncü/Beşinci Satır (Mem/Swap):** Fiziksel bellek (RAM) ve takas alanı (swap) kullanımı hakkında bilgi (toplam, kullanılan, boş, buffer/cache).
*   **İşlem Listesi:** Çalışan işlemler ve onlarla ilgili bilgiler (PID, kullanıcı, öncelik (PR), nice değeri (NI), sanal bellek (VIRT), fiziksel bellek (RES), paylaşılan bellek (SHR), durum (S), %CPU, %MEM, çalışma süresi (TIME+), komut (COMMAND)).

![](../.gitbook/assets/top.png)

_Tipik bir `top` ekranı_

**Etkileşimli `top` Komutları:**

`top` çalışırken aşağıdaki tuşlarla etkileşimde bulunabilirsiniz:

*   **`1`**: Her bir CPU çekirdeğinin kullanımını ayrı ayrı gösterir/gizler.
*   **`h`** veya **`?`**: Yardım ekranını gösterir.
*   **`q`**: `top` programından çıkar.
*   **`k`**: Bir işleme sinyal göndermek (genellikle sonlandırmak) için kullanılır. PID ve sinyal numarası (varsayılan 15/SIGTERM) istenir.
*   **`r`**: Bir işlemin nice değerini (önceliğini) değiştirmek için kullanılır (renice). PID ve yeni nice değeri istenir.
*   **`M`**: İşlemleri bellek kullanımına (%MEM) göre sıralar.
*   **`P`**: İşlemleri CPU kullanımına (%CPU) göre sıralar (varsayılan).
*   **`u`**: Belirli bir kullanıcının işlemlerini filtreler.
*   **`f`**: Gösterilecek alanları (sütunları) ve sıralama alanını seçmek için kullanılır.
*   **`E`**: Bellek birimlerini (KiB, MiB, GiB vb.) değiştirir.
*   **`z`**: Renkli gösterimi açar/kapatır.

![](../.gitbook/assets/top2.png)

_Her CPU çekirdeğini ayrı gösteren `top` ekranı (`1` tuşuna basıldıktan sonra)_

**htop**

`top` programının renklendirilmiş, daha kullanıcı dostu ve etkileşimli bir alternatifidir. Genellikle sistemlerde önyüklü gelmez, ancak paket yöneticisi ile kolayca kurulabilir (`sudo apt install htop` veya `sudo dnf install htop`).

`htop`'ın avantajları:
*   Renkli ve daha okunaklı arayüz.
*   Fare ile veya ok tuşları ile işlemler arasında gezinme ve seçme.
*   Fonksiyon tuşları (F1-F10) ile kolayca işlem sonlandırma (F9 - Kill), nice değerini değiştirme (F7/F8 - Nice +/-), arama (F3 - Search), sıralama (F6 - SortBy), ağaç görünümü (F5 - Tree) gibi işlemleri yapma.
*   Sistem yükü, bellek ve swap kullanımı için daha görsel çubuklar.
*   Kolay yapılandırılabilir arayüz (F2 - Setup).

![](../.gitbook/assets/htop.png)

_htop ekran görüntüsü_

## free

Sistemin genel bellek tüketimini görmek için **free** komutu kullanılabilir.

Sistemin genel bellek (RAM) ve takas alanı (swap) kullanımını görmek için **free** komutu kullanılır.

```bash
free
              total        used        free      shared  buff/cache   available
Mem:        7947480     6307236      851532      482716     1640244     1640244
Swap:       7193596         276     7193320
```
*   **total:** Toplam fiziksel bellek/swap alanı.
*   **used:** Kullanılan bellek/swap alanı.
*   **free:** Tamamen boş olan bellek/swap alanı.
*   **shared:** Birden fazla işlem tarafından paylaşılan bellek (genellikle tmpfs).
*   **buff/cache:** Çekirdek tarafından tampon (buffer) ve sayfa önbelleği (page cache) için kullanılan bellek. Bu bellek, ihtiyaç duyulduğunda uygulamalar için serbest bırakılabilir.
*   **available:** Yeni uygulamaların başlatılması için (swap kullanmadan) kullanılabilecek tahmini bellek miktarı. `free` ve `buff/cache`'in bir kısmını içerir ve genellikle sistemin gerçek boş bellek durumunu `free` sütunundan daha iyi yansıtır.

Daha okunaklı (Megabyte, Gigabyte cinsinden) çıktı almak için `-h` parametresi kullanılır:
```bash
free -h
              total        used        free      shared  buff/cache   available
Mem:           7.6G        6.0G        831M        471M        1.6G        1.6G
Swap:          6.9G        276K        6.9G
```
Eski `free` sürümlerinde görülen `- / + buffers / cache` satırı artık kullanılmamaktadır ve `available` sütunu daha doğru bir bilgi verir.

## atop

Sistem kaynaklarının tümünü görmek istediğinizde en iyi seçeneklerden birisi atop'tır. Tüm donanımı ve prosesleri aynı pencerede görüntüleyebilen uygulama, yavaşlamaya neden olan donanım ya da yazılımın kolayca bulunabilmesini sağlamaktadır. Yapı olarak top'a çok benzer ancak daha detaylıdır ve sorunlu kaynakları daha parlak renkte gösterir.

![](../.gitbook/assets/atop.jpg)

## kill

**top** kullanılırken işlemleri öldürmek mümkündür. Ekranda **k** tuşuna bastığınızda, doğrudan en üstteki işlem seçilecektir. Bunun dışında bir işlem öldürülmek istenirse, ilgili işlemin PID'si yazılabilir. Ardından hangi sinyal ile öldürüleceği belirtilmelidir. Genel kanı **15** sinyali ile öldürmektir.

![](../.gitbook/assets/top-pid.png)

`top` veya `htop` içinden işlem sonlandırma genellikle arka planda `kill` komutunu kullanır. `kill` komutu, belirtilen PID'ye sahip işleme bir sinyal gönderir.

```bash
# 6392 PID'li işleme varsayılan sinyali (SIGTERM) gönder
kill 6392

# 6392 PID'li işleme SIGTERM (15) sinyalini gönder
kill -15 6392 
# veya
kill -TERM 6392

# 6392 PID'li işleme SIGKILL (9) sinyalini gönder (zorla kapatma)
kill -9 6392
# veya
kill -KILL 6392
```

*   **SIGTERM (15):** Programa sonlanması için nazik bir istek gönderir. Program bu sinyali yakalayıp dosyalarını kaydedebilir, bağlantılarını kapatabilir ve düzgün bir şekilde çıkabilir. Genellikle ilk denenmesi gereken sinyal budur.
*   **SIGKILL (9):** Programa sonlanması için zorlayıcı bir komut gönderir. Program bu sinyali yakalayamaz veya görmezden gelemez; çekirdek tarafından anında sonlandırılır. Bu, veri kaybına veya sistemde tutarsızlıklara yol açabilir, bu yüzden sadece SIGTERM işe yaramadığında kullanılmalıdır.
*   **SIGHUP (1):** "Hang Up" sinyalidir. Birçok servis (daemon), bu sinyali aldığında yapılandırma dosyalarını yeniden okumak veya kendini yeniden başlatmak (restart) üzere programlanmıştır (örneğin, Apache, Nginx).
*   **SIGINT (2):** "Interrupt" sinyalidir. Genellikle terminalde çalışan bir programa `CTRL+C` gönderildiğinde bu sinyal iletilir.

Sistemdeki tüm sinyallerin listesini görmek için `kill -l` komutu kullanılabilir.

**pkill ve killall:**

PID yerine işlem adına veya başka kriterlere göre sinyal göndermek için `pkill` ve `killall` komutları daha kullanışlıdır:

```bash
# Adı "firefox" olan tüm işlemlere SIGTERM gönder
pkill firefox

# "emre" kullanıcısına ait tüm "sleep" işlemlerine SIGKILL gönder
pkill -9 -u emre sleep

# Adı tam olarak "apache2" olan tüm işlemlere SIGHUP gönder (yapılandırmayı yeniden yükle)
killall -HUP apache2 
```
Bu komutları kullanırken dikkatli olunmalıdır, çünkü yanlışlıkla istenmeyen işlemleri sonlandırabilirler.

## uptime

Sistem hakkında bilgi edinmeyi sağlayan bir diğer komut **uptime** komutudur. Sistemin ne kadar zamandır çalıştığını, kaç kişinin (terminalin) açık olduğunu, son birkaç dakikanın işlemci yükünün ortalamasını gösterir.

Sistemin ne zaman başlatıldığını görmek için `who -b` komutu da oldukça faydalıdır.

Öte yandan sisteme bağlı terminallerin bir listesini elde etmek için `w` programı kullanılabilir.

## ps

**ps** programı (_process status_), sistemde o an çalışan işlemler hakkında anlık bilgi almak için kullanılır. `top` veya `htop` gibi sürekli güncellenmez, çalıştırıldığı andaki durumu gösterir.

İki yaygın kullanım stili vardır:

1.  **BSD Stili:** Genellikle `-` olmadan kullanılan parametreler.
    ```bash
    ps aux 
    ```
    *   `a`: Tüm kullanıcılara ait işlemleri gösterir (terminali olanlar).
    *   `u`: Kullanıcı odaklı formatta gösterir (sahip, CPU/MEM kullanımı vb.).
    *   `x`: Terminali olmayan işlemleri de (servisler vb.) gösterir.

2.  **System V Stili:** Genellikle `-` ile başlayan parametreler.
    ```bash
    ps -ef
    ```
    *   `-e`: Tüm işlemleri gösterir (`a` ve `x`'in birleşimi gibi).
    *   `-f`: Tam formatta listeleme yapar (UID, PID, PPID, C, STIME, TTY, TIME, CMD).

Her iki komut da benzer bilgileri farklı formatlarda sunar. Çıktı genellikle uzundur, bu yüzden `less`, `grep` gibi araçlarla birlikte kullanılır:
```bash
ps aux | less
ps -ef | grep apache
```

İşlem ağacını (hangi işlemin hangisini başlattığını) hiyerarşik olarak görmek için `pstree` komutu veya `ps`'in bazı özel formatlama seçenekleri (`ps axjf` veya `ps -ejH`) kullanılabilir.

```bash
pstree
```

GNU/Linux üzerinde her zaman 1 numaralı **PID**'ye (Process ID) sahip işlem, sistemin başlangıç işlemidir (genellikle `init` veya modern sistemlerde `systemd`). Diğer tüm işlemler doğrudan veya dolaylı olarak bu işlem tarafından başlatılır (fork edilir). 1 numaralı işlem sonlandırılırsa sistem durur, bu nedenle çekirdek tarafından korunur ve genellikle `SIGKILL` gibi sinyallere yanıt vermez. Bir işlemin başlattığı işleme "child process", başlatan işleme ise "parent process" denir. Genellikle bir parent process öldüğünde, child process'leri de sonlanır veya 1 numaralı işlem tarafından evlat edinilir (adopted).

## nice

Linux üzerinde işlemlerin CPU zamanlayıcısındaki önceliği, "nice" değeri ile ayarlanabilir. Nice değeri, bir işlemin diğer işlemlere karşı ne kadar "nazik" (nice) olacağını belirtir.

*   Nice değeri **-20** (en yüksek öncelik, en az nazik) ile **+19** (en düşük öncelik, en nazik) arasında değişir.
*   Varsayılan nice değeri genellikle **0**'dır.
*   Daha düşük nice değerine sahip işlemler, CPU zamanlayıcısı tarafından daha öncelikli olarak çalıştırılır.
*   Normal kullanıcılar sadece kendi işlemlerinin nice değerini artırabilir (önceliği düşürebilir, yani daha "nazik" yapabilir). Nice değerini düşürmek (önceliği artırmak) için genellikle root yetkisi gerekir.

**top** ve `htop` komutlarının çıktısındaki **NI** sütunu, ilgili işlemin nice değerini gösterir.

Bir programı belirli bir _nice_ değeri ile çalıştırmak istersek, programı çalıştırma esnasında bu değeri belirtmek gerekir.

```bash
nice -n 12 crc8
```

Öte yandan, mevcut bir programın _nice_ değerini değiştirmek istersek, programın **PID** değerini parametre olarak vermek gerekir.

```bash
renice -3 5486
```

Komutların mevcut nice değerini **ps** ile öğrenmek için, ps çıktısından bunu talep edebiliriz.

```bash
ps ax -o pid,ni,cmd
```

**PROBLEM:** Sistem üzerinde 20 dakikadan daha uzun süredir çalışan belirli bir işlemi bulun. **ps** komutunun uygun parametreleriyle (`etime` veya `bsdtime` gibi) elde edilebilir. Bu işlemi öldürün.
