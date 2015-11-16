# Temel Dağıtımlar ve Kurulum

Sunucularda kullanılan temel dağıtımların kurulum aşamaları bu bölümde anlatılmıştır. Bu versiyonların masaüstü dağıtımlarının (Fedora ve Ubuntu gibi) kurulumu da benzerlikler göstermektedir.


## Disk Biçimlendirme

Kurulumunuzu yapmadan önce, disklerinizi nasıl biçimlendireceğinize karar vermeniz gerekir. Disk yapınızın nasıl bir RAID yapısına sahip olacağı, hangi dizinlerin *mount* edileceği veya *swap space* kullanıp kullanmayacağı tercihinize bağlıdır ancak genellikle tavsiye edilen yapı aşağıdaki gibidir.


|Dizin | Boyut | Açıklama|
|-- | -- | -- |
|/boot | 500 MB | Açılış Dizini |
| swap | 2xRAM Miktarı | Alternatif Bellek Alanı |
|/ | Geri Kalan Tüm Alan |Kök Dizin |


