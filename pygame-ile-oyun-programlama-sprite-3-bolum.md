# Pygame ile Oyun Programlama - Sprite 3. Bölüm

Sprite gruplamaya yarayan Group sınıfının bir dezavantajı var dedik, ancak dezavantaj yerine bir eksiklik diyebiliriz. Daha doğrusu, kullanım alanına göre farklı diyebiliriz.

Group sınıfının draw\(\) methoduyla Sprite nesnelerimizi parametre olarak verdiğimiz yüzeye çizdiriyorduk. Bu method bize None döndürür.

Bu sınıfın clear\(\) methodu ise Sprite ın çizildiği alanı temizliyordu. Biz bu yöntemle belli koordinatları temizleyerek performans sağlıyorduk. Aynı şekilde; tüm ekranı güncellemek yerine, çizdirdiğimiz dikdörtgen alanları güncelleyerek biraz daha performans artışı sağlayabiliriz.

Yeni Sprite gruplama sınıfımız RenderUpdates, çizim yaptığımız alanları güncellemekte yardımcı olacaktır. Bu sınıfın draw\(\) methodu geriye bir Rect listesi döndürür. Bu listedeki Rect nesneleri ekrana çizilen Spriteların çizildiği alanı göstermektedir. pygame.display modülünün update\(\) methoduyla da parametre olarak vereceğimiz bu Rect listesiyle update\(\) methodu verilen dikdörtgen alanları güncelleyecektir.

```text
import pygame, sys
from asker import *
pygame.init()

pencere = pygame.display.set_mode((500,500))
temizle = pygame.Surface((500,500))

grup = pygame.sprite.RenderUpdates()

asker1 = Asker((100,100))
asker2 = Asker2((200,100))

grup.add(asker1, asker2)

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    grup.clear(pencere, temizle)

    grup.update()
    rectlist = grup.draw(pencere)
    pygame.display.update(rectlist)
    saat.tick(24)
```

Yukarıdaki örnek açıkladığım şekilde çalışmaktadır. Tabii görüntü olarak bu performans artışını algılayamıyoruz, ama çok fazla iş yapan ve çizim yapan bir kodunuz varsa gözle görülür bir performans artışı olacaktır.

RenderUpdates, Group sınıfını genişleten\(miras alan\) bir sınıftır. Farkı ise draw\(\) methodunun geriye Sprite nesnelerinin, Rect bilgilerini liste olarak döndürmesidir.

RenderUpdates sınıfı Group sınıfını genişlettiği için Group sınıfında bulunan methodları da kullanabilmektedir…

Python da bilindiği üzere sözlükler sırasız veri tipidir. Örneğin;

```text
sozluk = {"Ali":18, "Mehmet":22, "Dilek":19}
```

gibi bir sözlük oluşturup print deyimi ile çıktısını aldığınızda yazdığınız sıra da olmadığını göreceksiniz. Sözlüklerin anahtarları 1,2,5,3 gibi sayılardan oluşuyorsa ne kadar karışık sırayla versenizde 1,2,3,5 gibi sıralanacaktır. Ancak buna güvenerek iş yapmayınız…

RenderUpdates ve Group sınıfları Sprite nesnelerini sözlükte tutar ve sırasız olarak ekrana çizer. Bu durum oyun ekranınızda sıralı bir çizim gerektiğinde sıkıntıya yol açabilir\(Harita çiziminde yer ile yerin üstünde durması gereken bir ağacın yerden önce çizilmesi gibi.\). Bunun önüne geçmek için sıralı çizim yapan OrderedUpdates sınıfını kullanmalıyız. Bu sınıfın RenderUpdates sınıfından tek farkı Sprite nesnelerini sözlük yerine listede tutmasıdır. Bunun dışında RenderUpdates sınıfında kullandığınız her methodu bu sınıfla da kullanabilirsiniz.

Bu sınıfın draw\(\) methodu da Rect listesi döndürdüğü için performans sağlamaya yardımcı olur.

Son Sprite gruplama sınıfımız ise LayeredUpdates…

Bu sınıf diğer Sprite gruplama sınıflarına göre daha çok özellik sunar. Bir resim düzenleme programında nasıl katmanlarla çalışıyorsanız bu sınıfla da Sprite nesnelerini katmanlara bölebiliyorsunuz. LayeredUpdates de varsayılan katman 0\(sıfır\)dır. add\(\) methodu ile eklediğiniz her Sprite 0 katmanına eklenir. Eğer yeni bir katmana Sprite eklemek isterseniz şu şekilde yapabilirsiniz.

```text
import pygame, sys
from asker import *
pygame.init()

pencere = pygame.display.set_mode((500,500))
temizle = pygame.Surface((500,500))

grup = pygame.sprite.LayeredUpdates()

asker1 = Asker((100,100))
asker2 = Asker2((200,100))

grup.add(asker1)
grup.add(asker2, layer=1)

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    grup.clear(pencere, temizle)
    grup.update()
    rectlist = grup.draw(pencere)
    pygame.display.update(rectlist)
    saat.tick(24)
```

asker2 adındaki Sprite nesnemiz 1\(bir\) katmanına eklenecektir. LayeredUpdates sınıfının layers\(\) methodunu print ile yazdırarak katmanları görebilirsiniz.

Üstteki duruma göre çıktımız böyle olur:

```text
print(grup.layers)

[0, 1]
```

Rect sınıfını anlattığım konuda, fare imleci konumuyla nasıl nesnelerle çarpışma kontrolünü denetlediğimizi öğrendik. LayeredUpdates sınıfı da get\_sprites\_at\(\) methodu ile parametre olarak verdiğimiz koordinatta hangi Spritelar çizili ise bir listesini döndürür. Bu özellik sayesinde hayal gücünüzü kullanarak bir çok şey yapabilirsiniz. Örneğin\(1. bölümün sonunda verdiğim dosyayı indirerek asker.py dosyasını elde edebilirsiniz\):

```text
import pygame, sys
from asker import *
pygame.init()

pencere = pygame.display.set_mode((500,500))
temizle = pygame.Surface((500,500))

grup = pygame.sprite.LayeredUpdates()

asker1 = Asker((100,100))
asker2 = Asker2((200,100))

grup.add(asker1)
grup.add(asker2, layer=1)

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    for i in grup.get_sprites_at(pygame.mouse.get_pos()):
        i.rect.y += 5

    grup.clear(pencere, temizle)
    grup.update()
    rectlist = grup.draw(pencere)
    pygame.display.update(rectlist)
    saat.tick(24)
```

Oyun döngümüzde bu methodu kullandık ve pozisyon parametresine fare imlecinin konumunu verdik. Bir for döngüsü ile fare imlecinin temas ettiği nesnelerin 5px aşağı doğru inmesini sağladık. Örnek kodu çalıştırdığınızda fare imlecinin konumuna göre askerlerin yüksekliği değişecektir.

Bir Sprite ın bulunduğu katmanı değiştirmek isteyebilirsiniz. Bunun sebebi fare imlecinin temas ettiği Sprite nesnesini öne almak olabilir. Bunu change\_layer\(\) methodu ile gerçekleştirebiliriz. Askerlerimizi bir biri üzerine gelecek şekilde çizdirelim ve bu methodu kullanarak fare imlecinin temas ettiği Sprite'ı öne getirelim.

```text
import pygame, sys
from asker import *
pygame.init()

pencere = pygame.display.set_mode((500,500))
temizle = pygame.Surface((500,500))

grup = pygame.sprite.LayeredUpdates()

asker1 = Asker((180,100))
asker2 = Asker2((200,100))
asker3 = Asker2((290,100))

grup.add(asker1)
grup.add(asker2, layer=1)

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    for i in grup.get_sprites_at(pygame.mouse.get_pos()):
        grup.change_layer(i, 0)

    grup.clear(pencere, temizle)
    grup.update()
    rectlist = grup.draw(pencere)
    pygame.display.update(rectlist)
    saat.tick(24)
```

Örnekte Asker nesnelerini birbirine temas edecek şekilde çizdirdik. Bundaki amaç fare imlecini üzerlerine getirince hangi Sprite ın ilk çizildiğini fark edebilmektir. Bu sefer for döngümüzde change\_layer\(\) methodunu kullandık ve temas halindeki Sprite nesnesini 0 katmanına aldırdık. Doğal olarak 1 katmanında bulunan Sprite nesnemiz 0 katmanına aktarılınca 1 katmanı silinmiş olacak. Ayrıca fare imleci her iki Sprite nesnesine de temas ediyorsa sıralamaya göre çizileceği için istediğiniz sonucu alamama ihtimali de olacaktır.

Burada nasıl bir kullanımı olabileceğine değindik. Halbuki bu işi yapan bir method vardır. move\_to\_front\(\) methodu parametre olarak verdiğiniz Sprite nesnesini en önce alacaktır.

```text
for i in grup.get_sprites_at(pygame.mouse.get_pos()):
    grup.move_to_front(i)
```

Eğer Sprite nesnesini en arkaya almak isterseniz de move\_to\_back\(\) methodunu kullanabilirsiniz.

```text
for i in grup.get_sprites_at(pygame.mouse.get_pos()):
    grup.move_to_back(i)
```

Bu kodu denerken fark ettim ki; fare imleci her iki Sprite a temas ediyorsa çok hızlı olarak her bir nesneyi ard arda arkaya atıyor ve bize sorun olabilecek bir görüntü ortaya çıkıyor. Ayrıca her arkaya atılan Sprite nesnesi en düşük katmandan bir eksi katmana atılıyor\(0 ise -1\). Bu da sürekli bir işlemde sürekli değişen katman adının -500 lere dayanmasına neden oluyor.

LayeredUpdates sınıfının başka methodları da mevcuttur, ama bunları uygulamalı derslerde gerekli olursa kullanacağız ve bu arada öğrenmiş olacağız. Sprite gruplama sınıfları bu kadar\(LayeredDirty var, ama kullanmaya gerek yoktur.\). Bir sonraki derste pygame.sprite modülünde bulunan fonksiyonları öğreneceğiz.

