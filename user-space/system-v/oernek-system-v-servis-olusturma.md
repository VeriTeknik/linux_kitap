# Örnek System V Servis Betiği Oluşturma

System V init (SysVinit) sisteminde, servisler `/etc/init.d/` (veya `/etc/rc.d/init.d/`) dizininde bulunan kabuk betikleri (shell scripts) ile yönetilir. Bu betikler, servisi başlatmak (`start`), durdurmak (`stop`), yeniden başlatmak (`restart`) ve durumunu sorgulamak (`status`) gibi eylemleri gerçekleştiren mantığı içerir.

Aşağıda, basit bir servisi yönetmek için temel bir SysVinit betiği iskeleti verilmiştir. Bu betik, `/usr/local/bin/benimservisim` adlı bir programı `benimkullanici` kullanıcısı olarak çalıştırdığını varsayar ve PID dosyasını `/var/run/benimservisim.pid` olarak kullanır.

```bash
#!/bin/sh
### BEGIN INIT INFO
# Provides:          benimservisim
# Required-Start:    $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Benim örnek servisimi başlatır/durdurur
# Description:       Bu betik, /usr/local/bin/benimservisim daemon'ını yönetir.
### END INIT INFO

# Betik değişkenleri
DAEMON=/usr/local/bin/benimservisim
DAEMON_ARGS="" # Daemon'a geçilecek argümanlar (varsa)
NAME=benimservisim
DESC="Benim Örnek Servisim"
PIDFILE=/var/run/$NAME.pid
SCRIPTNAME=/etc/init.d/$NAME
RUNASUSER=benimkullanici # Servisi çalıştıracak kullanıcı

# Dağıtıma özgü fonksiyon kütüphanesini yükle
# Debian/Ubuntu:
. /lib/lsb/init-functions
# RHEL/CentOS:
# . /etc/rc.d/init.d/functions 

# Yardımcı fonksiyonlar (start-stop-daemon Debian/Ubuntu'da yaygındır)
do_start() {
    # start-stop-daemon --start --quiet --pidfile $PIDFILE --chuid $RUNASUSER --exec $DAEMON --test > /dev/null || return 1
    # start-stop-daemon --start --quiet --pidfile $PIDFILE --chuid $RUNASUSER --exec $DAEMON -- $DAEMON_ARGS || return 2
    # Alternatif (daha basit, start-stop-daemon yoksa):
    if [ -f $PIDFILE ]; then
        log_daemon_msg "$DESC zaten çalışıyor" "$NAME"
        log_end_msg 0
        return 1
    fi
    log_daemon_msg "$DESC başlatılıyor" "$NAME"
    su -s /bin/sh -c "$DAEMON $DAEMON_ARGS & echo \$! > $PIDFILE" $RUNASUSER
    log_end_msg $?
}

do_stop() {
    # start-stop-daemon --stop --quiet --retry=TERM/30/KILL/5 --pidfile $PIDFILE --name $NAME
    # RETVAL="$?"
    # [ "$RETVAL" = 2 ] && return 2
    # start-stop-daemon --stop --quiet --oknodo --retry=0/30/KILL/5 --exec $DAEMON
    # [ "$?" = 2 ] && return 2
    # rm -f $PIDFILE
    # return "$RETVAL"
    # Alternatif (daha basit):
    if [ ! -f $PIDFILE ]; then
        log_daemon_msg "$DESC zaten durdurulmuş" "$NAME"
        log_end_msg 0
        return 1
    fi
    log_daemon_msg "$DESC durduruluyor" "$NAME"
    PID=$(cat $PIDFILE)
    kill $PID
    # kill komutunun başarılı olup olmadığını kontrol et (daha gelişmiş kontrol eklenebilir)
    sleep 1 # Durması için biraz bekle
    rm -f $PIDFILE
    log_end_msg $?
}

case "$1" in
  start)
    do_start
    ;;
  stop)
    do_stop
    ;;
  status)
    # status_of_proc -p $PIDFILE "$DAEMON" "$NAME" && exit 0 || exit $?
    # Alternatif (daha basit):
    if [ -f $PIDFILE ]; then
        PID=$(cat $PIDFILE)
        if ps -p $PID > /dev/null; then
            log_daemon_msg "$DESC çalışıyor (PID $PID)" "$NAME"
            log_end_msg 0
            exit 0
        else
            log_daemon_msg "$DESC çalışmıyor ama PID dosyası ($PIDFILE) mevcut" "$NAME"
            log_end_msg 1
            exit 1
        fi
    else
        log_daemon_msg "$DESC çalışmıyor" "$NAME"
        log_end_msg 3
        exit 3
    fi
    ;;
  restart|force-reload)
    log_daemon_msg "$DESC yeniden başlatılıyor" "$NAME"
    do_stop
    # Durması için biraz bekle
    sleep 1 
    do_start
    ;;
  *)
    echo "Kullanım: $SCRIPTNAME {start|stop|status|restart|force-reload}" >&2
    exit 3
    ;;
esac

exit 0
```

**Açıklamalar:**

*   **LSB Headers (`### BEGIN INIT INFO ... ### END INIT INFO`):** Bu başlıklar, `update-rc.d` ve `chkconfig` gibi araçların betik hakkında bilgi (bağımlılıklar, varsayılan runlevel'lar vb.) edinmesini sağlar. İsteğe bağlıdır ancak iyi bir pratiktir.
*   **Fonksiyon Kütüphanesi:** Dağıtımlar genellikle servis betiklerinde kullanılmak üzere standart fonksiyonlar (`log_daemon_msg`, `log_end_msg`, `status_of_proc` vb.) içeren bir kütüphane sağlar. Bu, betiklerin daha tutarlı ve okunabilir olmasını sağlar. Örnekte hem Debian/Ubuntu (`/lib/lsb/init-functions`) hem de RHEL/CentOS (`/etc/rc.d/init.d/functions`) için kaynak satırı yorum olarak gösterilmiştir. Ayrıca `start-stop-daemon` (Debian/Ubuntu) gibi yardımcı programlar da kullanılabilir. Örnekte daha basit, genel komutlarla (`su`, `kill`, `ps`) alternatifler de gösterilmiştir.
*   **`case "$1" in ... esac`:** Betiğe verilen ilk argümana (`start`, `stop` vb.) göre ilgili fonksiyonu (`do_start`, `do_stop`) çağırır.
*   **PID Dosyası:** Servisin işlem ID'sini (PID) saklamak için genellikle `/var/run/` altında bir `.pid` dosyası kullanılır. Bu, servisin durumunu kontrol etmek ve durdurmak için kullanılır.
*   **Kullanıcı Değiştirme:** Servisler genellikle root olarak başlatılmaz. `su` veya `start-stop-daemon --chuid` gibi komutlarla belirtilen kullanıcıya (`RUNASUSER`) geçiş yapılır.

**Kullanım:**

1.  Betiği `/etc/init.d/benimservisim` olarak kaydedin.
2.  Çalıştırma izni verin: `sudo chmod +x /etc/init.d/benimservisim`.
3.  Servisi yönetin:
    ```bash
    sudo service benimservisim start
    sudo service benimservisim status
    sudo service benimservisim stop
    ```
4.  Başlangıçta otomatik çalışması için etkinleştirin:
    ```bash
    # Debian/Ubuntu
    sudo update-rc.d benimservisim defaults 
    # veya
    # sudo update-rc.d benimservisim enable

    # RHEL/CentOS
    sudo chkconfig --add benimservisim
    sudo chkconfig benimservisim on 
    ```

Bu, SysVinit için temel bir servis betiği örneğidir. Gerçek dünya betikleri genellikle daha karmaşık hata kontrolü ve yapılandırma seçenekleri içerir. Modern systemd sistemlerinde servis yönetimi için `.service` unit dosyaları kullanılır ve bu genellikle daha basit ve güçlü bir yöntemdir.
