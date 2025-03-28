# Kernel Boot Süreci

Bu bölüm, bir Linux sisteminin açılış (boot) sürecinin temel adımlarını ele almaktadır. Bilgisayarın güç düğmesine basıldığı andan itibaren işletim sistemi çekirdeğinin (kernel) yüklenmesi ve ilk kullanıcı alanı (user space) işlemlerinin başlamasına kadar geçen süreç incelenecektir.

İncelenecek konular şunlardır:

*   [Sunucu Başlangıcına Genel Bakış](sunucu-baslangicina-genel-bakis.md): BIOS/UEFI, MBR/GPT ve bootloader arasındaki ilişki.
*   [Bootloader Nedir?](bootloader-nedir.md): GRUB gibi önyükleyicilerin görevleri.
*   [Çekirdeğin Yüklenmesi ve Boot Seçenekleri](cekirdegin-yueklenmesi-ve-boot-secenekleri.md): Çekirdek imajı, initramfs/initrd ve önyükleyici menüsü.
*   [Çekirdek Parametreleri](cekirdek-parametreleri.md): Çekirdeğin davranışını değiştirmek için kullanılan parametreler.

Bu bölüm, sistemin nasıl başladığını anlamak ve olası açılış sorunlarını gidermek için temel bilgiler sunar.
