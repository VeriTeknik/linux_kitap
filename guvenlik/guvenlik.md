# Güvenlik

### Öneriler

FTP servisini sürekli kapalı tutun, bunun için bir cron oluşturun, açmış olsanız bile kendiliğinden kapanmasını sağlayabilirsiniz.
```bash
11 0 * * * /etc/init.d/vsftpd stop
```

http://www.veriportal.com/linux-guvenlik
