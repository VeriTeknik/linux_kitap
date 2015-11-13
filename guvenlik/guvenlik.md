# Güvenlik

### Öneriler

FTP servisini sürekli kapalı tutun, bunun için bir cron oluşturun
```bash
11 0 * * * /etc/init.d/vsftpd stop
```