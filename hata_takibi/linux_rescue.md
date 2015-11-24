# Linux Rescue

Hata veren ve açılmayan Linux sistemleri kurtarmanın birden çok yolu vardır, eğer vaktiniz ve boş bir diskiniz varsa kurtarma işleminden önce diskin yedeğini almanızı tavsiye ederiz.

Yeni ve kurtarılması gereken diskleri başka bir sunucuya takın, Diyelim ki kurtarılması gereken diskiniz /dev/sdb ve yeni diskiniz /dev/sdc olsun. (disk isimlerini öğrenmek için fdisk -l komutunu kullanabilirsiniz)

Eğer diskleriniz aynı boyutta ise fdisk ile alacağınız bilgi yeterli olmayacaktır, bu durumda aşağıdaki komut ile disklerin üretici bilgisi ve seri numarasını alabilirsiniz:

^^^bash
hdparm -I /dev/sda | grep Number:
        Model Number:       WDC WD5003ABYX-18WERA0                  
        Serial Number:      WD-WMAYP1327681
^^^

