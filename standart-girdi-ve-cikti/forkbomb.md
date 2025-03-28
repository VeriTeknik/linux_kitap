# Fork Bomb

## Fork Bomb Nedir?

Fork bomb, kendini sürekli olarak kopyalayan (fork eden) ve bu yolla sistem kaynaklarını (özellikle işlem tablosunu ve işlemci zamanını) hızla tüketerek sistemi kullanılamaz hale getirmeyi amaçlayan basit ama etkili bir Denial of Service (DoS) saldırısıdır. Çalıştırıldığında, üstel bir hızla yeni işlemler oluşturur ve kısa sürede sistemin yeni işlem başlatmasını engeller.

## Klasik Bash Fork Bombası

Bash kabuğunda en bilinen fork bombası şu şekildedir:

```bash
:(){ :|:& };:
```

Bu kodun açıklaması şöyledir:

1.  **:(){ ... };** : `:` isminde bir fonksiyon tanımlar. Fonksiyon ismi olarak `:` seçilmesi, kodun daha kısa ve gizemli görünmesini sağlar, ancak herhangi bir geçerli fonksiyon ismi de kullanılabilir (örneğin `bomb(){ bomb|bomb& }; bomb`).
2.  **:** : Fonksiyonun gövdesi içinde, fonksiyon kendini tekrar çağırır.
3.  **|** : İlk çağrının standart çıktısı, ikinci bir çağrıya pipe (boru hattı) ile standart girdi olarak bağlanır. Bu pipe işlemi, her iki tarafın da çalışmasını sağlamak için gereklidir, ancak verinin kendisi önemli değildir.
4.  **:** : İkinci çağrı yapılır.
5.  **&** : İkinci çağrı (`:|:`) arka planda çalıştırılır. Bu, fonksiyonun hemen geri dönmesini ve döngünün hızla devam etmesini sağlar.
6.  **;** : Fonksiyon tanımı sonlandırılır.
7.  **:** : Tanımlanan `:` fonksiyonu ilk kez çağrılarak bomba tetiklenir.

Bu zincirleme reaksiyon sonucunda, her çalışan fonksiyon iki yeni fonksiyonu tetikler ve işlem sayısı katlanarak artar (`1 -> 2 -> 4 -> 8 -> 16 -> ...`).

## Fork Bombasına Karşı Korunma: `ulimit` ve `nproc`

Modern Linux sistemleri, fork bombalarının etkisini sınırlamak için kullanıcı başına kaynak limitleri uygular. Bu limitlerden en önemlisi, bir kullanıcının aynı anda sahip olabileceği maksimum işlem sayısıdır (`nproc`).

Bu limitler `ulimit` komutu ile görüntülenebilir ve (soft limitler için) ayarlanabilir:

```bash
# Mevcut kullanıcı için soft nproc limitini göster
ulimit -Su 

# Mevcut kullanıcı için hard nproc limitini göster
ulimit -Hu

# Tüm soft limitleri göster
ulimit -aS

# Tüm hard limitleri göster
ulimit -aH 
```

Örnek `ulimit -aS` çıktısı:
```
...
max user processes              (-u) 4096 
...
```
Bu örnekte, kullanıcının aynı anda en fazla 4096 işlem başlatabileceği görülmektedir (soft limit).

Bir fork bombası çalıştırıldığında, işlem sayısı hızla bu limite ulaşır. Limit aşıldığında, sistem yeni işlemler (fork) oluşturamaz ve aşağıdaki gibi hatalar verir:

```bash
-bash: fork: retry: No child processes
-bash: fork: Resource temporarily unavailable 
```

Bu hatalar, fork bombasının `nproc` limiti tarafından durdurulduğunu gösterir. Sistem yavaşlayabilir ancak tamamen kullanılamaz hale gelmez ve genellikle yönetici müdahalesi ile (örneğin, bombayı başlatan kullanıcının işlemlerini sonlandırarak) kurtarılabilir.

## Limitlerin Yapılandırılması

Kullanıcı bazlı işlem limitleri genellikle `/etc/security/limits.conf` dosyasında veya daha modern sistemlerde `/etc/security/limits.d/` dizini altındaki dosyalarda (örneğin, `20-nproc.conf`) ayarlanır.

Örnek bir `/etc/security/limits.d/20-nproc.conf` dosyası:
```
# Kullanıcıların yanlışlıkla fork bombası başlatmasını önlemek için
# varsayılan işlem limiti.

*          soft    nproc     4096
root       soft    nproc     unlimited
#emre       hard    nproc     8192 
```
*   `*`: Root dışındaki tüm kullanıcılar için geçerli kural.
*   `soft`: Uygulanan başlangıç limiti (soft limit). Kullanıcı bu limiti hard limite kadar artırabilir.
*   `hard`: Kullanıcının ulaşabileceği mutlak üst limit (hard limit). Sadece root tarafından artırılabilir.
*   `nproc`: Limitlenen kaynağın türü (number of processes).
*   `4096`: Limit değeri. `unlimited` ise limitsiz anlamına gelir.

Bu yapılandırma dosyaları, PAM (Pluggable Authentication Modules) aracılığıyla, genellikle `pam_limits.so` modülü kullanılarak kullanıcı oturumları başladığında uygulanır.

Fork bombaları, kaynak limitlerinin ve dikkatli sistem yönetiminin önemini gösteren klasik bir örnektir.
