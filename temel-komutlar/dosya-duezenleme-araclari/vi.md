# vi

vi, terminalde çok satırlı düzenleme yapabilen metin editörlerinin atalarından birisi olarak görülür. 2009'da yapılan bir anket Linux üzerine kullanılan en yaygın metin editörünün %36 ile vi olduğunu göstermektedir. Kompleks işlemleri destekleyen ve hemen her sistemde önyüklü olarak gelen vi haricinde bir diğer popüler metin editörü ise emacs'tir.

## Tarihçe

vi editörü aslında oldukça eskiye dayanan bir takım editörlerin evrilmesiyle ortaya çıkmıştır. Editörün çıkış noktası _ed_ isimli editördür. _ed_ Ken Thompson tarafından UNIX işletim sisteminin üç temel yapı taşından biri olarak geliştirilmiştir: assembler, editör ve shell.

_ed_'in kullanımı zamanla pek "pratik" bulunmadığından, "ölümsüzler" \(immortals\) tarafından kullanılabileceği espirisiyle, "ölümlüler" \(mortals\) için _em_ geliştirildi: _editor for mortals_. _em_'nin görece yaygınlaşmasının ardından Bill Joy ve Chuck Haley kodu kendilerince şekillendirip _en_ isimli bir versiyonunu geliştirdiler. Daha sonra Bill Joy tek başına _en_'yi "extend" edip _ex_'i piyasaya sürdü. _ex_'e eklenen tam-ekran görsel modu, metin editörlerinin dönüm noktası oldu. Her ne kadar bu fikir piyasadaki başka yazılımlardan alınmış olsa da, en yaygın kullanıma ulaşanı kendisi olmuştu. Zamanla _ex_'in görsel modu _\(visual mode\)_ o kadar çok kullanıldı ki, doğrudan _vi_ komutu ile _ex_ programı tam-ekran görsel modunda çalıştırılır oldu.

Yıllar sonra Bram Moolenaar tarafından _vi_ kodu geliştirildi ve **vim** (_Vi IMproved_) ortaya çıktı. `vim`, `vi`'ın tüm özelliklerini içerirken, üzerine sözdizimi renklendirme (syntax highlighting), çoklu geri alma (undo), görsel mod (visual mode), eklenti desteği gibi birçok modern özellik eklemiştir. Günümüzde çoğu Linux dağıtımında `vi` komutu aslında `vim`'in daha kısıtlı bir modda çalışan halini veya doğrudan `vim`'i çalıştırır. Bu nedenle, genellikle `vim`'in sunduğu gelişmiş özelliklerden faydalanmak daha pratiktir. Örneklerimizde `vim`'i temel alacağız.

Çoğu sistemde `vim` önyüklü gelir. Eğer kurulu değilse, dağıtımınızın paket yöneticisi ile kolayca kurabilirsiniz:

```bash
# Debian/Ubuntu tabanlı sistemler:
sudo apt update && sudo apt install vim

# RHEL/CentOS/Fedora tabanlı sistemler:
sudo dnf install vim 
# veya eski sürümlerde:
# sudo yum install vim 
```

## Çalışma Modları

vi'ın bir takım çalışma modları vardır, bu aslında _ed_'den kalan bir alışkanlıktır. vi ilk çalıştırıldığında _komut_ modunda bulunur, _düzenleme_ moduna geçmek için ayrıca işlem yapmanız gerekir. _komut_ modundayken tuşlara basarsanız yazı yazamazsınız, ancak kopyalama, silme, dosya içinde gezinme benzeri işlemleri gerçekleştirebilirsiniz.

Dosyamızı `vi` (veya `vim`) ile aşağıdaki gibi açtığımızda, varsayılan olarak _Komut Modu_nda başlarız.

```bash
vim crc8.py 
```

_Komut Modu_'ndan _Düzenleme (Insert) Modu_'na geçmek için çeşitli tuşlar kullanılır:

*   **i**: İmlecin bulunduğu yerden itibaren eklemeye başlar (insert).
*   **a**: İmlecin bulunduğu karakterden *sonra* eklemeye başlar (append).
*   **I**: Satırın başına geçer ve eklemeye başlar.
*   **A**: Satırın sonuna geçer ve eklemeye başlar.
*   **o**: İmlecin bulunduğu satırın *altına* yeni bir boş satır açar ve ekleme moduna geçer.
*   **O**: İmlecin bulunduğu satırın *üstüne* yeni bir boş satır açar ve ekleme moduna geçer.

Düzenleme moduna geçtiğinizde, terminalin en alt satırında genellikle **-- INSERT --** veya **-- EKLE --** gibi bir ibare belirir. Bu modda klavyeden yazdıklarınız doğrudan dosyaya eklenir.

Düzenleme modundan çıkıp tekrar _Komut Modu_'na dönmek için klavyenizdeki **ESC** tuşuna basmanız gerekir. **-- INSERT --** yazısı kaybolacaktır. Tüm temel komutlar, silme, kopyalama, yapıştırma işlemleri _Komut Modu_'nda yapılır.

## Temel Komutlar (Komut Modu)

Aşağıdaki komutlar _Komut Modu_'nda iken çalışır. Bazıları doğrudan harf tuşları ile, bazıları ise `:` karakteri ile başlar.

**Çıkış ve Kaydetme:**

*   **:q** : Değişiklik yapılmadıysa çıkar (quit).
*   **:q!** : Değişiklikleri kaydetmeden çıkmaya zorlar.
*   **:w** : Dosyayı kaydeder (write).
*   **:wq** veya **:x** veya **ZZ** (Shift+Z iki kere): Dosyayı kaydedip çıkar.
*   **:w yeni_dosya_adi** : Dosyayı farklı bir isimle kaydeder.

**Navigasyon (Gezinme):**

*   **h, j, k, l**: Sol, aşağı, yukarı, sağ yön tuşları gibi çalışır.
*   **w**: Sonraki kelimenin başına gider.
*   **b**: Önceki kelimenin başına gider.
*   **0** (sıfır): Satırın başına gider.
*   **$**: Satırın sonuna gider.
*   **^**: Satırın başındaki ilk karaktere (boşluk olmayan) gider.
*   **gg**: Dosyanın başına gider.
*   **G** (Shift+g): Dosyanın sonuna gider.
*   **:N** (N bir sayı): N numaralı satıra gider (örneğin `:17`).
*   **Ctrl+f**: Bir sayfa aşağı gider (forward).
*   **Ctrl+b**: Bir sayfa yukarı gider (backward).

**Silme:**

*   **x**: İmlecin üzerindeki karakteri siler.
*   **dw**: İmleçten kelimenin sonuna kadar siler (delete word).
*   **dd**: İmlecin bulunduğu satırı tamamen siler.
*   **D** (Shift+d) veya **d$**: İmleçten satır sonuna kadar siler.

**Kopyalama ve Yapıştırma:**

*   **yw**: İmleçten kelimenin sonuna kadar kopyalar (yank word).
*   **yy** veya **Y** (Shift+y): İmlecin bulunduğu satırı tamamen kopyalar.
*   **p**: Kopyalanan veya silinen metni imleçten *sonra* yapıştırır (paste).
*   **P** (Shift+p): Kopyalanan veya silinen metni imleçten *önce* yapıştırır.

**Geri Alma / Yineleme:**

*   **u**: Son yapılan değişikliği geri alır (undo).
*   **Ctrl+r**: Geri alınan değişikliği yineler (redo).

**Arama:**

*   **/aranacak_metin**: İmleçten itibaren ileriye doğru metin arar. `n` tuşu sonraki eşleşmeye, `N` tuşu önceki eşleşmeye gider.
*   **?aranacak_metin**: İmleçten itibaren geriye doğru metin arar. `n` ve `N` tuşları aynı şekilde çalışır.

**Diğer Modlar:**

*   **v**: Görsel Moda (Visual Mode) geçer. Ok tuşları veya navigasyon komutları ile metin seçilebilir. Seçilen metin üzerinde silme (`d`), kopyalama (`y`) gibi işlemler yapılabilir.
*   **V** (Shift+v): Satır bazında Görsel Moda geçer.
*   **Ctrl+v**: Blok bazında Görsel Moda geçer (dikdörtgen seçim).

**Ayarlar (:** ile başlayan komutlar):

*   **:set number** veya **:set nu**: Satır numaralarını gösterir.
*   **:set nonumber** veya **:set nonu**: Satır numaralarını gizler.
*   **:set hlsearch**: Arama sonuçlarını vurgular.
*   **:set nohlsearch**: Arama vurgusunu kaldırır.
*   **:syntax on**: Sözdizimi renklendirmeyi açar (genellikle varsayılan olarak açıktır).
*   **:syntax off**: Sözdizimi renklendirmeyi kapatır.

**Pencere Yönetimi (:** ile başlayan komutlar):

*   **:split** veya **:sp**: Ekranı yatay olarak ikiye böler, aynı dosyayı gösterir.
*   **:vsplit** veya **:vsp**: Ekranı dikey olarak ikiye böler, aynı dosyayı gösterir.
*   **:sp dosya_adi**: Ekranı yatay böler ve yeni pencerede belirtilen dosyayı açar.
*   **:vsp dosya_adi**: Ekranı dikey böler ve yeni pencerede belirtilen dosyayı açar.
*   **:new**: Yeni boş bir pencere açar.
*   **Ctrl+w Ctrl+w** veya **Ctrl+w w**: Pencereler arasında geçiş yapar.
*   **Ctrl+w h/j/k/l**: Yön tuşlarına göre pencereler arasında geçiş yapar.
*   **:q** (pencere içindeyken): Aktif pencereyi kapatır.
*   **:only**: Aktif pencere dışındaki tüm pencereleri kapatır.

Bu komutlar `vim`'in temel kullanımını kapsar. `vim` çok daha fazla özelliğe ve komuta sahiptir. Daha fazla bilgi için komut modundayken `:help` yazarak `vim`'in kendi yardım sistemine başvurabilirsiniz veya `vimtutor` komutunu çalıştırarak interaktif bir eğitim alabilirsiniz.
