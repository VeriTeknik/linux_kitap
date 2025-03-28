# Komut Satırından `ftp` Kullanımı

Linux sistemlerde, FTP sunucularına bağlanmak için genellikle `ftp` adında bir komut satırı istemcisi bulunur. Bu istemci, interaktif bir oturum başlatarak FTP sunucusu üzerinde dosya işlemleri yapmanızı sağlar.

**UYARI:** Standart `ftp` istemcisi, güvensiz FTP protokolünü kullanır. Kullanıcı adı, şifre ve aktarılan veriler **şifrelenmeden** gönderilir. Güvenilmeyen ağlarda kullanmaktan **kesinlikle kaçının**. Güvenli dosya transferi için `sftp` istemcisini veya `scp`/`rsync` komutlarını tercih edin.

## Temel `ftp` Komutları

1.  **Bağlantı Kurma:**
    ```bash
    ftp <sunucu_adresi_veya_ip>
    ```
    Komut, sunucuya bağlanmayı dener ve genellikle kullanıcı adı (`Name:`) ister. Anonim giriş için `ftp` veya `anonymous` yazılabilir, aksi takdirde kullanıcı adınızı girin. Ardından şifre istenir.

2.  **İnteraktif Oturum:**
    Başarıyla bağlandıktan sonra `ftp>` istemi görünür. Aşağıdaki komutlar kullanılabilir:
    *   `ls` veya `dir`: Uzak sunucudaki dosyaları listeler.
    *   `cd <dizin>`: Uzak sunucudaki dizini değiştirir.
    *   `lcd <dizin>`: Yerel makinedeki dizini değiştirir.
    *   `pwd`: Uzak sunucudaki mevcut çalışma dizinini gösterir.
    *   `get <uzak_dosya> [yerel_dosya]`: Uzak sunucudan yerel makineye dosya indirir.
    *   `put <yerel_dosya> [uzak_dosya]`: Yerel makineden uzak sunucuya dosya yükler.
    *   `mget <dosya_deseni>`: Birden fazla dosyayı indirir (joker karakterler kullanılabilir, örn. `mget *.txt`). Genellikle her dosya için onay ister.
    *   `mput <dosya_deseni>`: Birden fazla dosyayı yükler.
    *   `prompt`: `mget` ve `mput` için onay istemini açar/kapatır.
    *   `binary`: Dosya transfer modunu ikili (binary) olarak ayarlar (resimler, programlar vb. için gereklidir).
    *   `ascii`: Dosya transfer modunu metin (ASCII) olarak ayarlar (metin dosyaları için). Genellikle `binary` modunu kullanmak daha güvenlidir.
    *   `mkdir <dizin>`: Uzak sunucuda dizin oluşturur.
    *   `delete <dosya>`: Uzak sunucudaki dosyayı siler.
    *   `rmdir <dizin>`: Uzak sunucudaki boş dizini siler.
    *   `help` veya `?`: Kullanılabilir komutların listesini gösterir.
    *   `bye` veya `quit`: Bağlantıyı kapatır ve istemciden çıkar.

**Örnek Oturum:**
```bash
$ ftp ftp.example.com
Connected to ftp.example.com.
220 (vsFTPd 3.0.3)
Name (ftp.example.com:user): anonymous
331 Please specify the password.
Password: <e-posta_adresinizi_girin>
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
227 Entering Passive Mode (192,0,2,1,195,80).
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Oct 26  2023 pub
226 Directory send OK.
ftp> cd pub
250 Directory successfully changed.
ftp> get README.txt
227 Entering Passive Mode (192,0,2,1,195,81).
150 Opening BINARY mode data connection for README.txt (1024 bytes).
226 Transfer complete.
1024 bytes received in 0.01 secs (100.00 Kbytes/sec)
ftp> bye
221 Goodbye.
```

**Tekrar Hatırlatma:** Bu `ftp` istemcisi güvensizdir. Güvenli alternatifler için [SFTP](sftp.md) bölümüne bakın veya `scp`/`rsync` kullanın.
