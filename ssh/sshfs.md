# sshfs

sshfs (SSH File System) ile uzak bir sunucuyu kendi bilgisayarınızda bir klasör gibi "mount" edebilirsiniz. Üstelik SSHFS için hem Linux, hem Mac OSX hem de MS Windows client programları mevcuttur.

## Uzak Dosya Sistemini Mount Etmek

Bunun için önce sistemimizde bir dizin oluşturmamız gerekir.

```bash
mkdir /media/centos-server
```

Ardından uzak dosya sistemini mount ederiz.

```bash
sshfs root@sunucu-ip:/ /media/centos-server
```

Eğer mount işlemi için anahtar kullanmak isterseniz

```bash
sshfs -o IdentityFile=~/.ssh/id_rsa root@sunucu-ip:/ /media/centos-server
```

Artık ```/media/centos-server``` klasöründe yapacağınız bütün işlemler sunucunuzda gerçekleşecektir.

Boot sırasında bu işlemin otomatik olarak gerçekleşmesi için ```/etc/fstab``` dosyasına satır ekleyebilirsiniz.

```bash
sshfs#root@sunucu-ip:/ /media/centos-server
```

Sunucularınızı birbirine SSHFS ile bağlamanız dosya transferleri veya kod geliştirme aşamalarında çok ciddi kolaylıklar sağlasa da, sistemlerinizden birinin açığı yakalandığında diğerlerinin de tehdit altında olmasını sağlar. Bu açıdan dikkatli kullanılmalıdır.

## Uzak Dosya Sistemini Unmount Etmek

Standart dosya sistemi unmount etme işlemi ile aynıdır.

```bash
umount /media/centos-server
```