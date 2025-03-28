# egrep ve Regular Expressions (Düzenli İfadeler)

Regular Expressions (Regex veya Düzenli İfadeler), metin içinde belirli kalıpları (pattern) aramak, eşleştirmek ve işlemek için kullanılan güçlü bir araçtır. Birçok programlama dili ve komut satırı aracı (örneğin `grep`, `sed`, `awk`, `perl`, `python`) regex desteği sunar.

`grep` (Global Regular Expression Print) komutu, dosyalarda veya standart girdide belirli bir kalıbı aramak için kullanılır. `grep`'in farklı sürümleri ve modları vardır:
*   `grep`: Temel regex sözdizimini kullanır. Bazı özel karakterler (`+`, `?`, `|`, `()`, `{}`) anlamlarını yitirir ve kullanılacaksa `\` ile kaçış (escape) yapılması gerekir (örn. `\+`, `\(...\)`).
*   `egrep` veya `grep -E`: Genişletilmiş regex (Extended Regular Expressions - ERE) sözdizimini kullanır. `+`, `?`, `|`, `()`, `{}` gibi karakterler özel anlamlarını korur ve kaçış yapmaya gerek kalmaz. Bu genellikle daha kullanışlıdır.
*   `fgrep` veya `grep -F`: Sabit dizgeler (Fixed strings) arar, regex metakarakterlerini yorumlamaz. Basit metin aramaları için daha hızlı olabilir.

Bu bölümde `grep -E` (veya eşdeğeri `egrep`) ve genişletilmiş regex sözdizimine odaklanacağız.

## Temel Regex Metakarakterleri

Regex'te bazı karakterlerin özel anlamları vardır (metakarakterler):

*   **.** (Nokta): Herhangi *tek bir* karakterle eşleşir (yeni satır karakteri hariç).
    *   Örnek: `gr.p` ifadesi "grep", "grip", "grap" gibi dizgelerle eşleşir.
*   **\*** (Yıldız): Kendinden *önceki* karakterin veya grubun **sıfır veya daha fazla** kez tekrarlanmasıyla eşleşir.
    *   Örnek: `a*b` ifadesi "b", "ab", "aab", "aaab" ile eşleşir. `.*` ise herhangi bir karakter dizisiyle (boş dizgi dahil) eşleşir.
*   **+** (Artı): Kendinden *önceki* karakterin veya grubun **bir veya daha fazla** kez tekrarlanmasıyla eşleşir. (`grep -E` gerektirir).
    *   Örnek: `a+b` ifadesi "ab", "aab", "aaab" ile eşleşir, ancak sadece "b" ile eşleşmez.
*   **?** (Soru İşareti): Kendinden *önceki* karakterin veya grubun **sıfır veya bir** kez tekrarlanmasıyla eşleşir. (`grep -E` gerektirir).
    *   Örnek: `colou?r` ifadesi hem "color" hem de "colour" ile eşleşir.
*   **^** (Şapka): Satırın *başlangıcıyla* eşleşir.
    *   Örnek: `^Merhaba` ifadesi sadece satırın başında "Merhaba" ile başlayanlarla eşleşir.
*   **$** (Dolar): Satırın *sonuyla* eşleşir.
    *   Örnek: `dünya$` ifadesi sadece satırın sonunda "dünya" ile bitenlerle eşleşir. `^Merhaba dünya$` ise sadece "Merhaba dünya" içeren satırla eşleşir.

## Karakter Sınıfları ve Köşeli Parantezler `[...]`

*   **`[abc]`**: Köşeli parantez içindeki karakterlerden *herhangi biriyle* eşleşir (bu örnekte 'a', 'b' veya 'c').
    *   Örnek: `gr[ae]y` ifadesi "gray" ve "grey" ile eşleşir.
*   **`[a-z]`**: Belirtilen aralıktaki karakterlerden *herhangi biriyle* eşleşir (bu örnekte herhangi bir küçük harf). `[0-9]` rakamlarla, `[a-zA-Z0-9]` alfanümerik karakterlerle eşleşir.
*   **`[^abc]`**: Köşeli parantez içindeki `^` karakteri, parantez içindeki karakterler *dışındaki* herhangi bir karakterle eşleşir (bu örnekte 'a', 'b' ve 'c' olmayan herhangi bir karakter).

**POSIX Karakter Sınıfları:** Taşınabilirlik ve farklı yerel ayarlar (locale) için önceden tanımlanmış karakter sınıfları kullanılır (genellikle çift köşeli parantez içinde):
*   `[[:digit:]]`: Rakamlar (`[0-9]`)
*   `[[:lower:]]`: Küçük harfler (`[a-z]`)
*   `[[:upper:]]`: Büyük harfler (`[A-Z]`)
*   `[[:alpha:]]`: Harfler (`[a-zA-Z]`)
*   `[[:alnum:]]`: Alfanümerik karakterler (`[a-zA-Z0-9]`)
*   `[[:space:]]`: Boşluk karakterleri (boşluk, tab, yeni satır vb.)
*   `[[:punct:]]`: Noktalama işaretleri
*   `[[:xdigit:]]`: Hexadecimal rakamlar (`[0-9a-fA-F]`)

## Gruplama `(...)` ve Alternatif `|`

*   **`(...)`**: Parantezler, bir grup karakteri veya alt ifadeyi bir arada tutmak için kullanılır. Quantifier'lar (`*`, `+`, `?`) veya alternatif (`|`) bu gruplara uygulanabilir. (`grep -E` gerektirir).
    *   Örnek: `(ab)+` ifadesi "ab", "abab", "ababab" ile eşleşir.
*   **`|`**: "Veya" anlamına gelir. İki veya daha fazla alternatiften biriyle eşleşir. (`grep -E` gerektirir).
    *   Örnek: `kedi|köpek` ifadesi "kedi" veya "köpek" ile eşleşir. `^(kedi|köpek)` satır başında "kedi" veya "köpek" ile eşleşir.

## Quantifier'lar `{...}`

Belirli sayıda tekrarı belirtmek için kullanılır. (`grep -E` gerektirir).
*   **`{n}`**: Önceki öğenin tam olarak `n` kez tekrarlanmasıyla eşleşir.
    *   Örnek: `[0-9]{3}` tam olarak 3 rakamla eşleşir.
*   **`{n,}`**: Önceki öğenin en az `n` kez tekrarlanmasıyla eşleşir.
    *   Örnek: `[0-9]{2,}` en az 2 rakamla eşleşir.
*   **`{n,m}`**: Önceki öğenin en az `n`, en fazla `m` kez tekrarlanmasıyla eşleşir.
    *   Örnek: `[a-z]{3,5}` 3 ila 5 küçük harfle eşleşir.

## Kaçış Karakteri `\`

Bir metakarakterin özel anlamını kaldırıp normal bir karakter gibi eşleşmesini sağlamak için kullanılır.
*   Örnek: `\.` ifadesi `.` metakarakteri yerine gerçekten nokta karakteriyle eşleşir. `\*` yıldız karakteriyle eşleşir. `\\` ters eğik çizgi karakteriyle eşleşir.

## `grep -E` (egrep) Kullanım Örnekleri

```bash
# dosya.txt içinde "hata" veya "uyarı" kelimelerini içeren satırları bul (büyük/küçük harf duyarsız)
grep -Ei 'hata|uyarı' dosya.txt

# dosya.txt içinde en az 5 rakamdan oluşan sayıları içeren satırları bul
grep -E '[0-9]{5,}' dosya.txt

# dosya.txt içinde IP adresine benzeyen kalıpları bul (basit örnek)
# (Not: Bu regex tüm geçerli IP adreslerini tam olarak kapsamaz)
grep -E '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' dosya.txt

# /etc/passwd dosyasında bash veya sh kabuğunu kullanan kullanıcıları bul
grep -E '(/bin/bash|/bin/sh)$' /etc/passwd

# Sadece eşleşen IP adreslerini göster (-o)
grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' access.log

# "TODO" içermeyen (-v) satırları göster
grep -Ev 'TODO' kod.py 
```

## Diğer `grep` Seçenekleri

*   `-i`: Büyük/küçük harf ayrımını yok say (ignore case).
*   `-v`: Eşleşmeyen satırları göster (invert match).
*   `-c`: Eşleşen satır sayısını göster (count).
*   `-l`: Eşleşme içeren dosyaların sadece isimlerini listele.
*   `-L`: Eşleşme içermeyen dosyaların sadece isimlerini listele.
*   `-n`: Eşleşen satırların satır numaralarını göster.
*   `-r` veya `-R`: Dizinin içindeki alt dizinlerde de arama yap (recursive).
*   `-o`: Sadece eşleşen metin kısmını göster, tüm satırı değil.
*   `-A num`: Eşleşen satırdan sonraki `num` satırı da göster (After).
*   `-B num`: Eşleşen satırdan önceki `num` satırı da göster (Before).
*   `-C num`: Eşleşen satırın etrafındaki `num` satırı da göster (Context).

Regular expressions, pratik yaparak ve farklı araçlarda deneyerek öğrenilen güçlü bir yetenektir. İnternette birçok regex test aracı ve öğretici kaynak bulunmaktadır.
