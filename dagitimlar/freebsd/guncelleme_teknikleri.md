# Güncelleme Teknikleri

Diğer dağıtımlarda olduğu gibi FreeBSD üzerinde de birden çok paket ve dağıtım güncelleme yöntemi vardır. Ancak bu yöntemler diğer dağıtyımlardaki gibi sadece bir yazılım üzerinde yoğunlaşmamıştır ve neredeyse hepsi aynı popülerliktedir.

Çoğunlukla bir update yönteminin çalışmadığı noktada diğer update yöntemi çalışacak ya da hangi update yöntemi ile başlanıldıysa onunla devam etmek doğru olacaktır.

### Major Güncellemeler
FreeBSD üzerinden Major (X.0) güncellemeleri yapılabilmektedir, portların uyumluluğu gözden geçirildikten sonra bir sorun yaşamadan büyük update gerçekleştirebilirsiniz. FreeBSD üzerinde tüm kodları yeniden derlemek gibi bir opsiyonunuz olsa dahi, 6.3 versiyonundan bu yana binary güncellemeleri de sunulmakta, eşit performansta hızlı bir güncelleme opsiyonu sunulmaktadır.

Major sürüm güncellemesinden sonra tüm paketlerin sağlıklı bir şekilde yeni sürüme port edilebilmesi için portupgrade paketine ihtiyaç duyulmaktadır, eğer sistemde yüklü değilse portlardan kolaylıkla yükleyebilirsiniz:



