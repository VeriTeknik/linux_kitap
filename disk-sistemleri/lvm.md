# LVM (Logical Volume Management)

LVM (Mantıksal Birim Yönetimi), Linux'ta disk depolama alanını yönetmek için kullanılan esnek bir katmandır. Geleneksel disk bölümleme (partitioning) yöntemlerinin (örn. MBR, GPT) üzerine bir soyutlama katmanı ekleyerek, disk alanını daha dinamik ve kolay yönetilebilir hale getirir. Özellikle sunucu ortamlarında ve disk alanı ihtiyaçlarının zamanla değişebildiği durumlarda çok kullanışlıdır.

## LVM'nin Avantajları

*   **Esnek Alan Yönetimi:** Mantıksal birimleri (Logical Volumes - LV'ler) kolayca yeniden boyutlandırma (büyütme ve bazen küçültme) imkanı sunar.
*   **Diskleri Birleştirme:** Birden fazla fiziksel diski veya disk bölümünü tek bir büyük depolama havuzunda (Volume Group - VG) birleştirebilir.
*   **Birimleri Yayma (Spanning):** Tek bir mantıksal birimi birden fazla fiziksel diske yayabilirsiniz.
*   **Anlık Görüntüler (Snapshots):** Bir mantıksal birimin belirli bir andaki durumunun anlık görüntüsünü oluşturabilirsiniz. Bu, veri kaybı riski olmadan yedekleme yapmak veya testler gerçekleştirmek için çok kullanışlıdır.
*   **Thin Provisioning:** Gerçekte fiziksel olarak ayrılmamış alanı mantıksal birimlere atayarak disk alanının daha verimli kullanılmasını sağlar (alan sadece ihtiyaç duyulduğunda ayrılır).

## Temel Kavramlar

LVM'nin üç ana bileşeni vardır:

1.  **Fiziksel Birimler (Physical Volumes - PVs):** LVM tarafından kullanılacak olan temel depolama bloklarıdır. Bunlar genellikle tam diskler (`/dev/sda`, `/dev/nvme0n1`) veya disk bölümleridir (`/dev/sdb1`, `/dev/sdc2`). Bir diski veya bölümü LVM için hazırlamak amacıyla `pvcreate` komutu ile işaretlenirler.
2.  **Birim Grupları (Volume Groups - VGs):** Bir veya daha fazla Fiziksel Birim'in bir araya getirilmesiyle oluşturulan depolama havuzlarıdır (`vgcreate`). Mantıksal Birimler bu havuzdan oluşturulur. Bir VG'ye daha sonra yeni PV'ler eklenerek havuzun boyutu artırılabilir (`vgextend`).
3.  **Mantıksal Birimler (Logical Volumes - LVs):** Birim Grubu içindeki boş alandan ayrılan, kullanılabilir "sanal" disk bölümleridir (`lvcreate`). Kullanıcılar ve işletim sistemi, doğrudan LV'ler üzerinde işlem yapar (formatlama, bağlama vb.). LV'ler genellikle `/dev/<vg_adı>/<lv_adı>` veya `/dev/mapper/<vg_adı>-<lv_adı>` gibi yollarla erişilir. LV'ler, VG'de yeterli boş alan olduğu sürece kolayca büyütülebilir (`lvextend`).

LVM, depolama alanını **Fiziksel Kapsamlar (Physical Extents - PE)** adı verilen küçük, sabit boyutlu bloklara böler. Tüm LVM işlemleri (alan ayırma, taşıma vb.) bu PE'ler üzerinden yapılır.

## Temel LVM İş Akışı

1.  **Diskleri/Bölümleri Hazırlama:** LVM için kullanılacak diskleri (`/dev/sdb`, `/dev/sdc` vb.) veya disk bölümlerini (`fdisk`, `parted` gibi araçlarla) oluşturun. Bölüm tipi olarak "Linux LVM" (ID: 8e) ayarlamak iyi bir pratiktir.
2.  **Fiziksel Birimleri (PV) Oluşturma:** Seçilen diskleri/bölümleri `pvcreate` ile LVM için işaretleyin:
    ```bash
    sudo pvcreate /dev/sdb1 /dev/sdc1
    ```
    Mevcut PV'leri listelemek için: `sudo pvs` veya `sudo pvdisplay`.
3.  **Birim Grubunu (VG) Oluşturma:** PV'leri kullanarak bir VG oluşturun:
    ```bash
    sudo vgcreate my_volume_group /dev/sdb1 /dev/sdc1 
    ```
    *   `my_volume_group`: Oluşturulacak VG'nin adı.
    Mevcut VG'leri listelemek için: `sudo vgs` veya `sudo vgdisplay`.
4.  **Mantıksal Birimleri (LV) Oluşturma:** VG içindeki boş alandan LV'ler oluşturun:
    ```bash
    # Boyut belirterek (10 Gigabyte)
    sudo lvcreate -L 10G -n data_lv my_volume_group 

    # VG'deki kalan tüm boş alanı kullanarak
    sudo lvcreate -l 100%FREE -n logs_lv my_volume_group 
    ```
    *   `-L <boyut>`: LV boyutunu belirtir (örn. `10G`, `500M`).
    *   `-l <+/-yüzde>%FREE/VG/PVS`: VG'deki boş alanın yüzdesini kullanır.
    *   `-n <lv_adı>`: Oluşturulacak LV'nin adını belirtir.
    Mevcut LV'leri listelemek için: `sudo lvs` veya `sudo lvdisplay`. LV'ler genellikle `/dev/mapper/my_volume_group-data_lv` gibi görünür.
5.  **Dosya Sistemini Oluşturma:** Oluşturulan LV'leri istediğiniz dosya sistemiyle formatlayın:
    ```bash
    sudo mkfs.ext4 /dev/my_volume_group/data_lv
    sudo mkfs.xfs /dev/my_volume_group/logs_lv
    ```
6.  **Bağlama (Mount):** Formatlanan LV'leri sistemdeki dizinlere bağlayın ve kalıcı olması için `/etc/fstab` dosyasına ekleyin:
    ```bash
    sudo mkdir /mnt/data /mnt/logs
    sudo mount /dev/my_volume_group/data_lv /mnt/data
    sudo mount /dev/my_volume_group/logs_lv /mnt/logs

    # /etc/fstab'a ekleme (UUID veya aygıt yolu ile)
    # /dev/my_volume_group/data_lv    /mnt/data    ext4    defaults    0    2
    # /dev/my_volume_group/logs_lv     /mnt/logs    xfs     defaults    0    2
    ```

## Yaygın Yönetim İşlemleri

*   **VG'yi Genişletme:** Yeni bir PV'yi mevcut bir VG'ye ekleme:
    ```bash
    sudo pvcreate /dev/sdd1
    sudo vgextend my_volume_group /dev/sdd1
    ```
*   **LV'yi Genişletme:** Bir LV'nin boyutunu artırma (VG'de yeterli boş alan olmalı):
    ```bash
    # data_lv'ye 5GB ekle
    sudo lvextend -L +5G /dev/my_volume_group/data_lv 
    # veya VG'deki tüm boş alanı kullanacak şekilde genişlet
    # sudo lvextend -l +100%FREE /dev/my_volume_group/data_lv

    # Dosya sistemini de genişletmeyi unutmayın!
    # ext4 için (online yapılabilir):
    sudo resize2fs /dev/my_volume_group/data_lv 
    # xfs için (online yapılabilir, bağlı olmalı):
    # sudo xfs_growfs /mnt/data 
    ```
*   **LV'yi Küçültme:** (Daha riskli, dikkatli yapılmalıdır!)
    1.  Dosya sistemini **bağlı değilken** küçültün (`resize2fs` ext4 için, XFS küçültmeyi desteklemez).
    2.  LV'yi `lvreduce` ile küçültün.
    3.  Dosya sistemini tekrar bağlayın.
    ```bash
    sudo umount /mnt/data
    sudo e2fsck -f /dev/my_volume_group/data_lv # Önce kontrol et
    sudo resize2fs /dev/my_volume_group/data_lv 45G # Dosya sistemini küçült
    sudo lvreduce -L 45G /dev/my_volume_group/data_lv # LV'yi küçült
    sudo mount /dev/my_volume_group/data_lv /mnt/data
    ```
*   **Snapshot Oluşturma:** Bir LV'nin anlık görüntüsünü oluşturma (örn. yedekleme öncesi):
    ```bash
    # data_lv için 1GB boyutunda bir snapshot oluştur
    sudo lvcreate -s -L 1G -n data_snapshot /dev/my_volume_group/data_lv 
    ```
    Snapshot, oluşturulduğu andaki LV'nin durumunu tutar. Orijinal LV değişmeye devam ederken, snapshot değişmez (veya sadece değişiklikleri depolar - Copy-on-Write). Snapshot üzerinden yedek alınabilir. İş bitince snapshot kaldırılmalıdır (`lvremove`).
*   **Kaldırma İşlemleri:**
    ```bash
    sudo lvremove /dev/my_volume_group/data_lv
    sudo vgremove my_volume_group
    sudo pvremove /dev/sdb1 /dev/sdc1
    ```
    Bu işlemler veri kaybına neden olur, dikkatli kullanılmalıdır!

LVM, disk yönetiminde büyük esneklik sağlar ve özellikle sunucu ortamlarında disk alanını verimli bir şekilde yönetmek için standart bir araç haline gelmiştir.
