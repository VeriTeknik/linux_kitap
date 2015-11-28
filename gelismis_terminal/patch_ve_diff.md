# patch ve diff

İki dosya arasındaki farkı görmenin en kolay yolu, **diff** komutunu kullanmaktır. Örneğin

```bash
eaydin@dixon ~/calisma $ cat test.txt
birinci satir
ikinci satir
ucuncu satir
eaydin@dixon ~/calisma $ cat test2.txt
birinci satir
ikinci satir
eaydin@dixon ~/calisma $ diff test.txt test2.txt 
3d2
< ucuncu satir
```

