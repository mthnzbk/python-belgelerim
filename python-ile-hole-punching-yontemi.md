# Python ile Hole Punching Yöntemi

Hole Punching denilen yöntem, nat arkasındaki iki cihazı birbirine doğrudan bağlamaya yarar. Skype, Google Hangouts gibi görüntü ve ses gibi verilerin aktarımını yapan programların kullandığı bir yöntemdir.

Görüntü ve ses verisi içeren veriler sunuculara çok büyük bir yük getireceği için bu tarz programlar bu verilerin karşılıklı iki istemci\(client\) arasında aktarılmasını sağlayarak sunucularını büyük bir yükten kurtarırlar.

Peki nat bu işi arka planda nasıl hallediyor? İç ve dış ip olmak üzere iki aşamalı ip kullandırıyor. Her makine kendi içerisinde 192.168.1.2 gibi iç ip kullanır ve dış dünya ile bu ip üzerinden iletişim kurduğunu sanır. İstemci ile bir sunucuya veri yolladığımızda nat, yerel ip ve portu dış ip ve port ile değiştirir. Nat bu bilgileri bir tabloda tutarak, 192.168.1.2 iç ip'li istemcinin 88.76.128.62 dış ip'si ile 1234 portu ile bağlandığını aklında tutuyor. Böylece dışarıdan bir veri bu dış ip ve porta geldiğinde nat bu verinin 192.168.1.2 iç ip'li istemciye geldiğini anlayarak veriyi bu istemciye yönlendiriyor.

Siz bu şekilde bir sunucuya, istemci ile bağlandığınızda nat'ın açtığı dış port sayesinde başka istemcilerin bu dış ip ve port ile sizinle doğrudan iletişim kurmasını sağlayabiliyorsunuz. İşte bu yönteme **Hole Punching** deniyor. Tabii bu yöntem en kolay UDP protokolü ile yapılabiliyor.

Çok basit olan bu yöntemi basit bir örnek ile açıkladıktan sonra Python ile basit bir uygulama yazarak anlatalım.

1. istemci Metehan.
2. istemci Konuray

ve sunucu...

Metehan ve Konuray sunucuya bağlanarak kendi dış ip ve portlarını sunucuya bildirmiş oluyorlar. Metehan ve Konuray birbirleriyle sesli görüşme yapmak istediklerinde sunucu bu istemcilere birbirlerinin ip ve portunu bildiriyor ve istemciler karşılıklı olarak birbirlerinin ip ve portunu öğreniyorlar.

Sıra geldi karşılıklı iletişimi başlatmaya...

1. Metehan, Konuray'a test mesajı yolluyor. Konuray'ın nat'ı sunucudan veri beklediği için kafası karışıyor ve bu gelen test verisini çöpe atıyor. Yani Metehan ilk veriyi kendisini Konuray'a tanıtmak için yolluyor.
2. Konuray'ın nat'ı bu gelen test verisini iletmese de Metehan'ın ip ve portunu tablosunda tutuyor ve Metehan'dan gelebilecek veriyi beklemeye koyuluyor.
3. Konuray' da Metehana test mesajı yolluyor, ama Metehan'ın nat'ı zaten Konuray'ı sunucu olarak bellediği için Konuray'dan gelen veriyi Metehan'a iletiyor.
4. Hem Metehan, hem de Konuray birbirlerini sunucu olarak kabul edip birbirlerinden gelecek veriyi beklemeye başlıyorlar. Gelen veriler nat'ları tarafından ilgili yerel ip'lere yollanacak ve karşılıklı veri alışverişi başlamış olacaktır.

Şimdi açıkladığımız bu yöntemi Python ile kodlayalım. Öncelikle sunucu uygulamamız ile başlayalım.

```text
from socket import *
import json

HOST = ""
PORT = 24531

BUFFER = 1024

sunucu = socket(AF_INET, SOCK_DGRAM)
sunucu.bind((HOST, PORT))

istemciler = []

while True:
    gelen_veri = sunucu.recvfrom(BUFFER)
    print("Gelen mesaj ->", gelen_veri[0], "Adres ->", gelen_veri[1])

    # gelen veriden ip-port bilgisini alıyoruz. Eğer istemciler listesinde yoksa listeye ekliyoruz.
    if not gelen_veri[1] in istemciler:
        istemciler.append(gelen_veri[1])

    # gelen veri byte cinsinden "ip ver" ise verinin geldiği ip bilgisi hariç istemciler listesindeki ip bilgilerini
    # istemciye yolluyoruz.
    if gelen_veri[0] == b"ip ver":
        ipler = [i for i in istemciler if i != gelen_veri[1]]
        sunucu.sendto(bytes(json.dumps(ipler), encoding="utf-8"), gelen_veri[1])
```

Yukarıdaki kodu sunucu.py adıyla bir dosyaya kaydedip çalıştıralım... Sunucu, istemciden gelen veriyi ip-port bilgisi ile birlikte konsola yazar. İstemci, veri olarak byte cinsinden "ip ver" verisini yollamışsa veriyi isteyen istemci dışında kayıtlı istemcilerin bir listesini json formatında istemciye yolluyor. Her gelen veriyle aynı ip-port bilgileri listeye eklenmesin diye de basit bir kontrol bloku yazdık. Bu örnek uygulama da ne kadar kullanıcı sunucudan ip-port verisi isterse kendisi hariç bütün istemcilerin ip-port bilgilerine ulaşacaktır. Şimdi ipython3 yorumlayıcı arayüzüyle istemci olarak sunucuya bağlanalım...

```text
In [1]: cl = socket(AF_INET, SOCK_DGRAM)

In [2]: cl.sendto(b"ip ver", ("", 24531))
Out[2]: 6

In [3]: cl.recvfrom(1024)
Out[3]: (b'[]', ('127.0.0.1', 24531))

In [4]: # aynı işlemi ayrı bir yorumlayıcı açarak da yapalım.

In [5]: cl.sendto(b"ip ver", ("", 24531))
Out[5]: 6

In [6]: cl.recvfrom(1024)
Out[6]: (b'[["127.0.0.1", 36286]]', ('127.0.0.1', 24531))
```

İki istemciden yola çıkarsak ilk istemcimiz, ikinci istemcinin ip-port bilgisini 6. satırda almış oldu. Şimdi bu ip-port bilgisi ile diğer istemciye test mesajı atalım ve sonucu görelim:

```text
In [7]: cl.sendto(b"Merhaba!", ("", 36286))
Out[7]: 8

# karşı istemci de:

In [13]: cl.recvfrom(1024)
Out[13]: (b'Merhaba!', ('127.0.0.1', 51819))
```

Başta anlatırken ilk mesajın nat tarafından çöpe atıldığını anlattık, ama bu örneği denediğinizde fark edeceksiniz ki ilk mesajı çöpe atmak yerine, yerine ulaştırdı. Çünkü bu socket uygulaması yerel ağ üzerinde çalışıyor ve aynı bilgisayar üzerinde deneniyor. Ip'si farklı iki istemci üzerinde denediğinizde ilk veri diğer istemciye ulaşmayacaktır. O yüzden karşı istemci recvfrom\(\) ile veri beklerken siz ikinci veriyi yollamadan ekrana bir şey basmayacaktır.

Bu basit yöntem ile yeterli seviyeye ulaştığınız da siz de skype gibi bir uygulama yapabilirsiniz. Tabii sabit hatlarla görüşmek istiyorsanız voip için gerekli standartları da öğrenmeniz gerekecektir ;\)

