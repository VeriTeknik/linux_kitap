# MySQL / MariaDB Servisini Çalıştırmak ve Yönetmek

MySQL veya MariaDB sunucusu kurulduktan sonra, bir sistem servisi olarak çalışır. Modern Linux dağıtımlarında bu servis `systemd` tarafından yönetilir. Servis adı genellikle `mysqld` (MySQL için) veya `mariadb` (MariaDB için)'dir.

Servisleri yönetmek için `systemctl` komutu kullanılır (`sudo` yetkisi gereklidir):

**Servis Durumunu Kontrol Etme:**
```bash
sudo systemctl status mariadb 
# veya
sudo systemctl status mysqld 
```
Bu komut, servisin aktif (`active (running)`) olup olmadığını, ne zamandır çalıştığını ve son log mesajlarını gösterir.

Örnek Çıktı (MariaDB):
```
● mariadb.service - MariaDB 10.5.15 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2023-10-27 10:30:00 +03; 5min ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
   Main PID: 1234 (mariadbd)
     Status: "Taking your SQL requests now..."
      Tasks: 15 (limit: 4617)
     Memory: 100.0M
        CPU: 500ms
     CGroup: /system.slice/mariadb.service
             └─1234 /usr/sbin/mariadbd
...
```

**Servisi Başlatma:**
Eğer servis çalışmıyorsa, başlatmak için:
```bash
sudo systemctl start mariadb 
# veya
sudo systemctl start mysqld
```

**Servisi Durdurma:**
```bash
sudo systemctl stop mariadb 
# veya
sudo systemctl stop mysqld
```

**Servisi Yeniden Başlatma:**
Yapılandırma değişikliklerinden sonra veya sorun giderme amacıyla:
```bash
sudo systemctl restart mariadb 
# veya
sudo systemctl restart mysqld
```

**Sistem Başlangıcında Etkinleştirme/Devre Dışı Bırakma:**
Servisin sistem açıldığında otomatik olarak başlamasını sağlamak için:
```bash
sudo systemctl enable mariadb 
# veya
sudo systemctl enable mysqld
```
Otomatik başlamasını engellemek için:
```bash
sudo systemctl disable mariadb 
# veya
sudo systemctl disable mysqld
```

## `mysqladmin` ile Durum Kontrolü

`mysqladmin` komutu, çalışan bir MySQL/MariaDB sunucusu hakkında bilgi almak ve bazı temel yönetim görevlerini yapmak için kullanılan bir istemci aracıdır. Bağlantı için kullanıcı adı ve şifre gerektirir.

Sunucu sürümünü ve durumunu kontrol etmek için:
```bash
mysqladmin -u root -p version status
```
Komut sizden root kullanıcısının şifresini isteyecektir.

Örnek Çıktı:
```
mysqladmin  Ver 9.1 Distrib 10.5.15-MariaDB, for debian-linux-gnu on x86_64
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Server version          10.5.15-MariaDB-0+deb11u1
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /run/mysqld/mysqld.sock
Uptime:                 10 min 5 sec

Threads: 7  Questions: 123  Slow queries: 0  Opens: 34  Flush tables: 1  Open tables: 27  Queries per second avg: 0.203
Uptime: 605  Threads: 7  Questions: 123  Slow queries: 0  Opens: 34  Flush tables: 1  Open tables: 27  Queries per second avg: 0.203
```

## Güvenlik Ayarları

Veritabanı sunucusu kurulduktan sonra çalışır durumda olsa bile, canlı ortamlarda kullanmadan önce temel güvenlik ayarlarının yapılması **çok önemlidir**. Bu işlem genellikle kurulum bölümünde bahsedilen `mariadb-secure-installation` veya `mysql_secure_installation` betikleri aracılığıyla yapılır. Bu betikler root şifresini ayarlamanıza, anonim kullanıcıları kaldırmanıza, uzaktan root girişini engellemenize ve test veritabanını kaldırmanıza yardımcı olur. Bu adımları atlamadığınızdan emin olun.
