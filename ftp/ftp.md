# FTP

```bash
[root@test ~]# systemctl enable vsftpd.service
Created symlink from /etc/systemd/system/multi-user.target.wants/vsftpd.service to /usr/lib/systemd/system/vsftpd.service.
[root@test ~]# systemctl start vsftpd.service 
[root@test ~]# 
```
Web kullanıcısı için daha önceden şifre oluşturmamıştık, aşağıdaki komut ile şifre oluşturalım

```bash
passwd web
```

