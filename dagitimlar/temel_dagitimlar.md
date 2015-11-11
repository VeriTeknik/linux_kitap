# Temel Dağıtımlar ve Kurulum

Veri Merkezlerinde ve sunucu sistemlerinde kullanılan temel dağtımların kurulum aşamaları bu bölümde verilmiştir. Bu versiyonların masaüstü dağıtımlarının (Fedora ve Ubuntu gibi) kurulumu da benzerlikler göstermektedir.


## Kurulum yapımadan önce

Kurulum yapılmadan önce sunucu altyapısı incelenmeli, RAID sistemi tasarlanmalıdır. Günümüzde standart kurulumlarda dizin yapısı asgari / dizini ve SWAP partisyonunu içermelidir ancak tavsiye edilen dizin yapısı şu şekildedir:

|Dizin | Boyut | Açıklama|
|-- | -- | -- |
|- /boot | 500 MB | Açılış Dizini |
| SWAP | 2xRAM Miktarı | Disk Belleği |

