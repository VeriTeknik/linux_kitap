# Forkbomb

Bu bölüm boyunca gördüğümüz file descriptor'lar, pipeline mekanizması ve bir kullanıcının limitlerinin kötüye kullanımı sayesinde, basit bir takım işlemlerle sistemin kaynaklarını tüketip, başka işlemlere cevap vermesinin engellenmes, kısacası Denial of Service (DoS) durumuna düşmesi sağlanabilir. Forkbomb adı verilen bu teknik, sistem üzerindeki işlemlerin fork'larının oluşturulmasına izin veren bütün platform ve programlama dillerinde gerçekleştirilebilir.

```
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: Resource temporarily unavailable
-bash: fork: retry: No child processes
-bash: fork: Resource temporarily unavailable
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: Resource temporarily unavailable
-bash: fork: Resource temporarily unavailable
-bash: fork: Resource temporarily unavailable
-bash: fork: Resource temporarily unavailable
-bash: fork: retry: No child processes
```

```
[root@thompson ~]# :(){ :|:& };:
[1] 21369
[root@thompson ~]#
```

```
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes
-bash: fork: retry: No child processes

[1]+  Done                    : | :
[root@thompson ~]#
```

![](../.gitbook/assets/Selection\_076.png)

```
[emre@thompson ~]$ ulimit -aS
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 3884
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) unlimited
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

```
[emre@thompson ~]$ cat /etc/security/limits.d/20-nproc.conf 
# Default limit for number of user's processes to prevent
# accidental fork bombs.
# See rhbz #432903 for reasoning.

#*          soft    nproc     4096
#root       soft    nproc     unlimited
emre        hard    nproc     unlimited
emre        soft    nproc     unlimited
```
