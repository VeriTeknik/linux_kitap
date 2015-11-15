# Tarih ve Saat Ayarlama

Linux üzerinde tarih ve saat ayarlama işlemleri **date** komutu ile gerçekleştirilir. Komutu tek başına yazdığınızda tarih ve saati çıktı olarak verir.

```bash
date
Sun Nov 15 19:22:23 EET 2015
```

date komutu ile istediğimiz biçimde çıktı alabiliriz.

```bash
date +%d_%m_%Y
15_11_2015
```

Daha kompleks işlemler için boşluklu parametreleri çift tırnak içinde birleştirip tek parametre olarak programa sunmak gerekir.

```
date +"Ay:%m / Yıl:%y"
Ay:11 / Yıl:15
```

