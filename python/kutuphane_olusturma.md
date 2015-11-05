# Kütüphane Oluşturma

Python programları doğru teknikle yazıldığında, hem kütüphane olarak kullanılabilir, hem de tek başlarına program olarak kullanılabilir. Örneğin önceki bölümde yazdığımız ornek3.py programını Python terminalinden çağırmak için aşağıdaki komutları uygulayabiliriz. ornek3.py dosyasının bulunduğu dizinde terminalinizden python programını çalıştırın, ve ardından:

```python
Python 2.7.6 (default, Jun 22 2015, 17:58:13) 
[GCC 4.8.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import ornek3
Python'un ismi Monty Python'dan gelir.
Python'un ismi Rossum'dan gelmez mi?
```

Gördüğünüz gibi kütüphanemizi çağırır çağırmaz ekrana sonuçlar yazdırdı, oysa ki biz sadece yazdir fonksiyonuna erişebilmek istiyorduk.

Bu problemin önüne geçebilmek için, Python kodlarında programınızın "main" kısmını tanımlayabiliyorsunuz. Bu kısımdan sonra yazılan kod, sadece programınız tek başına çağırıldığında çalıştırılır, başka bir Python kütüphanesi veya ortamı tarafından çağırıldığında çalıştırılmaz.

```python
# ornek3.py
def yazdir(isim):
    cumle = "Python'un ismi %s'dan gelir." % isim
    if len(cumle) > 37:
        return cumle
    else:
        return cumle[:-7] + " gelmez mi?"

if __name__ == '__main__':
    print yazdir("Monty Python")
    print yazdir("Rossum") 
```

Yukarıdaki programı tek başına çalıştırdığımızda, hala ekrana ilgili sonuçları yazdırır.

```
python ornek3.py
Python'un ismi Monty Python'dan gelir.
Python'un ismi Rossum'dan gelmez mi?
```

Ama kodumuzu bir kütüphane gibi çağırdığımızda, ekrana çıktıları doğrudan yazmaz, böylece fonksiyonumuza erişebiliriz.

```python
Python 2.7.6 (default, Jun 22 2015, 17:58:13) 
[GCC 4.8.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import ornek3
>>> sonuc = ornek3.yazdir("deneme")
>>> print sonuc
Python'un ismi deneme'dan gelmez mi?
```

Dikkatinizi çekmişse, kütüphanemizdeki fonksiyona ornek3.yazdir şeklinde eriştik. Bu her ne kadar tavsiye edilen yöntem olsa da, kütüphaneleri yüklerken fonksiyonları dilediğimiz gibi yükletebiliriz.

```python
from ornek3 import yazdir
yazdir("deneme")
Python'un ismi deneme'dan gelmez mi?
```

```python
from ornek3 import yazdir
yazdir("deneme")
Python'un ismi deneme'dan gelmez mi?
```

```python
import ornek3 as calisma
calisma.yazdir("deneme")
Python'un ismi deneme'dan gelmez mi?
```

```python
from ornek3 import yazdir as dizgi
dizgi("deneme")
Python'un ismi deneme'dan gelmez mi?
```