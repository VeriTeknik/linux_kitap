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

