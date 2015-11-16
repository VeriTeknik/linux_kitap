# Temel Dağıtımlar ve Kurulum

Sunucularda kullanılan temel dağıtımların kurulum aşamaları bu bölümde anlatılmıştır. Bu versiyonların masaüstü dağıtımlarının (Fedora ve Ubuntu gibi) kurulumu da benzerlikler göstermektedir.


## Disk Biçimlendirme

Kurulumunuzu yapmadan önce, disklerinizi nasıl biçimlendireceğinize karar vermeniz gerekir. Disk yapınızın nasıl bir RAID yapısına sahip olacağı, hangi dizinlerin *mount* edileceği veya *swap space* kullanıp kullanmayacağı tercihinize bağlıdır ancak genellikle tavsiye edilen yapı aşağıdaki gibidir.


|Dizin | Boyut | Açıklama|
|-- | -- | -- |
|/boot | 500 MB | Açılış Dizini |
| swap | 2xRAM Miktarı | Alternatif Bellek Alanı |
|/ | Geri Kalan Tüm Alan |Kök Dizin |

Bazı durumlarda, *tmp*, *home* gibi dizinlerin ayrıca partition olarak kullanıldığı görülebilir.

Modern işletim sistemlerinde doğrudan *ext4* biçimlendirme tercih edilir, ancak kullandığınız disk SSD (Solid State Disk) ise *ext2* kullanmanızı tavsiye ederiz. ext2 ile journaling yapılmadığından ve SSD ile buna ihtiyaç duymayacağınızdan, boş yere disk işlemleri yapılmaz. SSD üzerinde yapılan her işlem diskin ömrünü kısalttığından, journaling yapılmaması daha uzun süreli kullanım anlamına gelecektir.


