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

```bash
date +"Ay:%m / Yıl:%y"
Ay:11 / Yıl:15
```

Saati ve tarihi ayarlamak içinse aşağıdaki gibi bir yol izlenebilir.

```bash
date -s "2 NOV 2014 17:45:12"
Sun Nov 2 17:45:12 EET 2014
```

