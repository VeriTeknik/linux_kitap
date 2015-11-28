# xargs

xargs komutu ile bir komuttan gelen standart çıktıyı başka bir komuta argüman olarak yönlendirebilirsiniz.

Örneğin bir dizindeki (ve alt dizinlerindeki) Python dosyalarının kaçar satırdan oluştuğunu görmek istersek

```bash
eaydin@dixon ~/devel/gkmv1 $ find . -name "*.py" | xargs wc -l
   697 ./cardread.py
    83 ./checkcronjobs.py
   637 ./GKM/commands.py
    91 ./GKM/communication.py
    47 ./GKM/crc8dallas.py
    33 ./GKM/database.py
   165 ./GKM/ethernet.py
    47 ./GKM/log.py
    62 ./GKM/messaging.py
    43 ./GKM/queue.py
    61 ./GKM/readconfig.py
    86 ./GKM/serialupdate.py
   132 ./GKM/wiegand.py
     0 ./GKM/__init__.py
   152 ./listener.py
    23 ./resetfoodgkm.py
    53 ./tools/gkm-watchdog.py
   867 ./tools/kontrol.py
  3279 total
  ```
  
  Gördüğünüz gibi burada sondaki satır **total** değerini veriyor, Bu aslında ```wc``` programının bir özelliği, en sonda kendisine argüman olarak verilen bütün dosyaların satır sayılarının toplamını da verir. Örneğin şöyle bir kullanım yapsaydık da çalışacaktı, ama alt dizinleri kapsamayacaktı.
  
  ```bash
  eaydin@dixon ~/devel/gkmv1 $ wc -l *.py
  697 cardread.py
   83 checkcronjobs.py
  152 listener.py
   23 resetfoodgkm.py
  955 total
  ```
  
  