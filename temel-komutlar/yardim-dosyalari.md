# Yardım Dosyaları

Geleneksel UNIX sistemlerde yardım dosyaları **man** komutu kullanılarak erişilen "manual" dosyalarıdır. Bu dosyalar sistemin tamamı için yazılmış ve bir hiyerarşi içerisindedir. man dosyaları **troff** yapısını kullanırlar, bu yapı sayesinde dosyaların çıktı alınca da rahat okunacak şekilde yazılması sağlanmıştır. GNU Projesi dahilinde **man**ual'ların hiyerarşisi çağdışı bulunmuş, ayrıca troff yapısının çıktı kaygısının yersiz olduğu düşünülmüştür. Bu yüzden alternatifi olarak **info** komutu ve yapısı oluşturulmuştur. Kullandığınız programa ve sisteme göre edineceğiniz dökümantasyon farklı olacaktır ancak bugün hemen her komutun **man** ve **info** sayfası mevcuttur. Eski olmasına rağmen **man** kullanımı, sistem yöneticilerinin alışkanlıkları arasındadır.

![](https://imgs.xkcd.com/comics/manual\_override.png)

_Kaynak:_ [https://xkcd.com/912/](https://xkcd.com/912/)

Her ne kadar **man** sayfaları UNIX'in bütün araçları için geliştirilmiş olsa da, her programcı gibi Ken Thompson ve Dennis Ritchie de dökümantasyon yazmaktan pek hoşlanmazlardı. UNIX'in ilk iki yılı boyunca hiç dökümantasyonu olmamıştır. En sonunda Thompson ve Ritchie yöneticileri Doug McIlroy'un ısrarları sonucu ilk **man** dosyalarını 1971'de yazmıştır.

Ayrıca, birçok GNU komutu, temel kullanım bilgilerini ve parametrelerini hızlıca görmek için `--help` seçeneğini destekler (örneğin, `ls --help`). Bu, genellikle `man` veya `info` sayfalarına göre daha kısa ve özet bir bilgi sunar.

## man Bölümleri

man sayfaları çeşitli bölümlerden oluşur. Bunun temel nedeni, aynı isimde birden fazla sayfa olabilmesidir. Örneğin **mkdir** için iki sayfa mevcuttur, birisi shell komutu olanı için, diğeri de System call olanı içindir. Bu yüzden iki farklı bölümde iki farklı mkdir man sayfası bulunur. Birisi birinci bölümde bulunur ve **mkdir(1)** şeklinde gösterilir, diğeriyse ikinci bölümde bulunur ve **mkdir(2)** şeklinde gösterilir.

man sayfaları aşağıdaki gibi bölümlendirilmiştir:

1. Programlar veya shell komutları - rm(1)
2. Unix ve C Sistem çağrıları - ioctl(2)
3. C Programlar için kütüphane çağrıları - printf(3)
4. Özel Dosyalar - null(4)
5. Dosya Biçimleri - ext4(5)
6. Oyunlar - fortune(6)
7. Diğerleri - regex(7)
8. Bakım - 1. bölümde yer alan programların bakımları ile ilgili

Herhangi bir komutun **man** sayfasına erişmek için, man komutuna kendisini parametre olarak göndermek yeterlidir.

```bash
man mkdir
```

Yukarıdaki örnekte man komutu, mkdir'in 1. bölümdeki sayfasını getirecektir, çünkü en düşük bu seviyede dosyası bulunur. Örneğin **null** için bu komut 4. bölümdeki sayfayı getirir, çünkü 1. 2. ve 3. bölümlerde sayfası yok.

Oysa `mkdir` komutunun 2. bölümdeki sayfasına erişmek isteseydik, yazmamız gereken komut şöyle olurdu:

```bash
man 2 mkdir
```

## apropos ve whatis

Herhangi bir konu hakkında yardım sayfalarını taramak isterseniz, `apropos` komutunu kullanabilirsiniz. `man -k` ile aynı işi yapmaktadır, dolayısıyla aşağıdaki komutlar size aynı çıktıyı verecektir:

```bash
man -k malloc
apropos malloc
__after_morecore_hook (3) - malloc debugging variables
__free_hook (3)      - malloc debugging variables
__malloc_hook (3)    - malloc debugging variables
__malloc_initialize_hook (3) - malloc debugging variables
__memalign_hook (3)  - malloc debugging variables
__realloc_hook (3)   - malloc debugging variables
malloc (3)           - allocate and free dynamic memory
malloc_get_state (3) - record and restore state of malloc implementation
malloc_hook (3)      - malloc debugging variables
malloc_info (3)      - export malloc state to a stream
malloc_set_state (3) - record and restore state of malloc implementation
malloc_stats (3)     - print memory allocation statistics
malloc_trim (3)      - release free memory from the top of the heap
malloc_usable_size (3) - obtain size of block of memory allocated from heap
mtrace (1)           - Interpret output from MALLOC_TRACE
mtrace (3)           - malloc tracing
muntrace (3)         - malloc tracing
```

## info Kullanımı

GNU projesinin `man` sayfalarına alternatifi olan `info` sistemi, belgeleri "node" adı verilen hiyerarşik bir yapıda sunar. `info` sayfalarında gezinmek için genellikle şu tuşlar kullanılır:

*   `n`: Sonraki node'a gitmek.
*   `p`: Önceki node'a gitmek.
*   `u`: Bir üst node'a gitmek.
*   `Enter`: İmlecin üzerindeki bağlantıyı (genellikle `*` ile başlar) takip etmek.
*   `q`: `info`'dan çıkmak.
*   `h`: Yardım ekranını görmek.

Örneğin, `ls` komutu için `info` sayfasını açmak isterseniz `info ls` komutunu kullanabilirsiniz.

**whatis** komutu ise bir komut hakkında size kısa bilgi vermeyi amaçlamaktadır.

```bash
whatis malloc
malloc (3)           - allocate and free dynamic memory
```

## man Dosyalarının Yolu

Her ne kadar Dosya Sistemi Hiyerarşi Standardında man dosyalarının yolu için `/usr/share/man` belirlenmişse de, her zaman buna uyulmaz, ve sistem birden fazla noktayı tarar.

Sisteminizde man dosyalarının nerelerde olduğunu öğrenmek için `manpath` komutunu veya `$MANPATH` çevre değişkenini kontrol edebilirsiniz. Ancak `manpath` komutu her zaman kurulu olmayabilir.

```bash
# manpath komutu varsa:
manpath
/usr/local/man:/usr/local/share/man:/usr/share/man

# Alternatif olarak çevre değişkeni:
echo $MANPATH
```

Yukarıdaki örnekte **:** ile ayrılmış değerler, farklı dizinlere işaret eder. Belirli bir komutun man sayfasının tam yolunu bulmanın daha güvenilir bir yolu `man -w` veya `man --where` komutunu kullanmaktır:

```bash
man -w mkdir
/usr/share/man/man1/mkdir.1.gz
```

Eğer komutun birden fazla bölümde man sayfası varsa, `man -wa` veya `man --where --all` komutu tüm yolları listeler:

```bash
man -wa mkdir
/usr/share/man/man1/mkdir.1.gz
/usr/share/man/man2/mkdir.2.gz
```
