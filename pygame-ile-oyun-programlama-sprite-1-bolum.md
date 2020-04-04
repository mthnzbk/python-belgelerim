# Pygame ile Oyun Programlama - Sprite 1. Bölüm

Sprite terimi anlam olarak peri, hayelet vb. anlamına gelse de konu itibariyle alakasız bir anlamı var.

Sprite 2B oyunlarda kullanılan bağımsız görüntülerdir. Bir sprite tekil ya da birden fazla resimden oluşabilir. Bu resimlerin arka arkaya oynamasıyla animasyon oluşur. Pygame de Sprite kullanmak için pygame.sprite modülünün Sprite sınıfı vardır.

Sprite modülü en az bir Surface ve bir Rect nesnesi içermelidir. Varsayılan olarak self.image niteliği bir Surface, self.rect niteliği de bir Rect nesnesini barındırmalıdır.

Sprite modülü bir sınıftır ve bu sınıfı kullanmak için yeni bir sınıf oluşturup miras almalıyız.

```text
from pygame.sprite import Sprite
import pygame

class Kare(Sprite):
    def __init__(self):
        Sprite.__init__(self)
        self.image = pygame.Surface((100,100))
        self.rect = self.image.get_rect()
```

En basit Sprite sınıfı bu şekildedir. Bu sınıfı şu şekilde kullanabiliriz.

```text
import pygame, sys
from kare import Kare
pygame.init()

pencere = pygame.display.set_mode((500,400))

kare = Kare()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((255,255,255))

    pencere.blit(kare.image, kare.rect)
    pygame.display.flip()
```

Kare sınıfımızı kare.py adıyla kaydettiğimizi varsayarsak yukarıda from kare import Kare ile sınıfımızı çağırmış oluyoruz. kare örneğine Kare sınıfımızı atıyoruz ve ana döngümüzde sınıfımızın self.image niteliğini ve self.rect niteliğini kullanarak ekrana yüzeyimizi çizdiriyoruz.

Sprite sınıfının kullanımı basit ve hızlıdır. Bu hızın nedeni en iyi şekilde optimize edilmesindendir. Çoğu zaman kendi sınıfınızı yazmak yerine Sprite sınıfını miras alarak genişletmeniz yeterlidir.

Sprite ın bir çok avantajı vardır. Genişlettiğiniz sınıfı argüman alacak şekilde ayarlayıp sınıfınızın çeşitli özellikte yüzeyler oluşturmasını sağlayabilirsiniz. Ayrıca Sprite sınıfının methodlarında update\(\) methodu ile oyun döngüsüne yazacağınız onlarca satır kodu bu methoda yazarak döngüde tek satır yer kaplamasını sağlayabilirsiniz. Ve tabii ki update\(\) methoduyla da istediğiniz gibi argüman alabilirsiniz. Sprite sınıfının başka methodları da mevcuttur, ama update\(\) methodu haricindekilerin kullanımına rastlamanız düşük bir ihtimal, ki gerekte yoktur.

Şimdi Kare sınıfımızı geliştirelim:

```text
# kare.py
from pygame.sprite import Sprite

class Kare(Sprite):
    def __init__(self, renk, konum):
        Sprite.__init__(self)
        self.image = pygame.Surface((100,100))
        self.image.fill(renk)
        self.rect = self.image.get_rect()
        self.rect.x, self.rect.y = konum
```

Kare sınıfımıza renk ve konum adında iki tane parametre ekledik ve renk parametresinin değerini self.image.fill\(\) e atadık. konum adlı parametrenin değerini ise rect nesnemizin x ve y niteliğine atadık. Biz renk parametresinden üç elemanlı bir tuple, konum parametresinden ise iki elamanlı bir tuple bekliyoruz. Bu parametrelere verilecek değerler çizilecek olan karemizin rengini ve konumunu belirleyecek.

Şimdi de yeni Kare sınıfımızı nasıl kullanabileceğimize bakalım:

```text
import pygame, sys
from kare import Kare
pygame.init()

pencere = pygame.display.set_mode((500,400))

renkler_konumlar = [[(255,0,0),(50,50)],
                    [(0,255,0),(150,100)],
                    [(0,0,255),(75,150)]]

kareler = []

for renk, konum in renkler_konumlar:
    kareler.append(Kare(renk,konum))

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((255,255,255))

    for kare in kareler:
        pencere.blit(kare.image, kare.rect)

    pygame.display.flip()
```

renkler\_konumlar adında bir liste oluşturduk ve içine 3 liste daha oluşturduk. Bu üç listeye de Kare sınıfımıza gireceğimiz parametreleri girdik. Hemen altında kareler adında boş bir liste oluşturduk. Bu listeye for döngüsü yardımıyla, renkler\_konumlar listesinden gelen verilerle oluşturulan Kare sınıflarımızı ekledik. Oyun döngümüzde ise yine bir for döngüsüyle kareler listesindeki Kare nesnelerini ana yüzeyimize çizdirdik. Sonuç olarak birbiriyle bitişik halde; kırmızı, yeşil ve mavi kareler çizdirmiş olduk.

Sprite sınıfının kullanımı bu kadarla bitmiyor; update\(\) methoduyla Kare sınıfımızda yapılan değişiklikleri ekrana yansıtabiliriz. Bu sayede oyun döngümüzde kalabalık yapan onlarca satır kodu update\(\) methodlarında yazabiliriz.

```text
# kare.py
from pygame.sprite import Sprite

class Kare(Sprite):
    def __init__(self, renk, konum):
        Sprite.__init__(self)
        self.image = pygame.Surface((100,100))
        self.image.fill(renk)
        self.rect = self.image.get_rect()
        self.rect.x, self.rect.y = konum

    def update(self):
        if pygame.key.get_pressed()[pygame.K_LEFT]:
            self.rect.x -= 1
        if pygame.key.get_pressed()[pygame.K_RIGHT]:
            self.rect.x += 1
        if pygame.key.get_pressed()[pygame.K_UP]:
            self.rect.y -= 1
        if pygame.key.get_pressed()[pygame.K_DOWN]:
            self.rect.y += 1
```

update methodumuzda klavye ok tuşlarıyla karemizin yer değiştirmesini sağlıyoruz. Bu kodu Sprite kullanmadan önce oyun döngümüze yazıyorduk. Bu durumda oyun döngümüze update\(\) methodumuzu yazmamız yeterli olacaktır:

```text
import pygame, sys
from kare import Kare
pygame.init()

pencere = pygame.display.set_mode((500,400))

kare = Kare((255,0,0),(50,50))

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((255,255,255))

    kare.update()

    pencere.blit(kare.image, kare.rect)
    pygame.display.flip()
```

Bu kadar basit… Sekiz satırlık kodu update\(\) methodumuza yazdık ve oyun döngümüzde çağırarak tek satır yer kaplamasını sağladık.

Spritelar genellikle animasyon için kullanılır. Sprite animasyon dediğimiz bu terim, ard arda oynatılan resimlerle meydana gelen hareketli yüzeyleri belirtir. İnternette “Sprite sheet” diye aratırsanız 2B oyunlarda kullanılmak üzere çizilmiş yüzlerce; kare kare çizilip, tek yerde toplanmış resim dosyaları bulacaksınız. Bu resimleri kullanarak örneğin; oyunumuzdaki karakterin hareketlerini oluşturabilir ve klavye kontrolleriyle animasyonları gösterebiliriz. Tabii bunu tek resim dosyasından, grafiklerin tek tek dikdörtgen alanını bulup kullanacağınız gibi her kareyi tek bir resim dosyası haline getiripte Sprite animasyonunu oluşturabilirsiniz.

Çok basit bir Sprite animasyonu yapalım ve gelecek derslere motive edici bir etkisi olmasını dileyelim.

```text
# asker.py
from pygame.sprite import Sprite
import pygame

class Asker(Sprite):
    def __init__(self, konum):
        Sprite.__init__(self)
        self.resimler = [pygame.image.load("00.png"), pygame.image.load("01.png")]
        self.image = self.resimler[0]
        self.rect =  self.image.get_rect()
        self.rect.x, self.rect.y = konum
        self.say = 0

    def update(self):
        if pygame.key.get_pressed()[pygame.K_LEFT]:
            if self.say >= 2:
                self.say = 0
            self.rect.x -= 4
            self.image = self.resimler[self.say]
            self.say += 1

        if pygame.key.get_pressed()[pygame.K_RIGHT]:
            if self.say >= 2:
                self.say = 0
            self.rect.x += 4
            self.image = pygame.transform.flip(self.resimler[self.say],1,0)
            self.say += 1
```

self.resimler adında bir liste oluşturduk ve 00.png ve 01.png resimlerinin Surface halini listeye ekledik. Her Sprite nesnesinde olması gereken self.image niteliğine self.resimler listesinin ilk elemanını atadık. Bu resim karakterin durduğu anı temsil ediyor.

self.say adında da bir sayaç oluşturduk; bunun amacı da self.resimler listesindeki Surface nesnelerini sırayla göstermek içindir.

update\(\) methodumuz da sağ ve sol yönler için sağ ve sol ok tuşlarına basılması durumu için iki adet if bloku açtık. Bu if bloklarında self.rect adındaki rect nesnemizin x niteliğinin değerlerini artırıp, azaltarak sağa ve sola hareketini sağladık. self.image niteliğini; self.say ın tuttuğu sayıyla, self.resimler listesinde o sıraya tekabul eden Surface nesnemizle değiştirdik ve ardından self.say ın değerini bir artırdık. Bu şekilde sağ ve sol ok tuşlarına basılınca self.resimler listesindeki Surface nesneleri akıcı şekilde ekrana çizilecektir. Sprite karelerinin az olması çok hızlı akmasına neden oluyor, ama siz elinizdeki sprite grafiklerinizle çoğaltıp test edebilirsiniz.

Sağ ok tuşuna basıldığında çalışacak kodlara baktığımızda daha önce görmediğimiz pygame.transform.flip\(\) ile karşılaşıyoruz. transform modülünün flip fonksiyonu ile ilk parametresine verdiğimiz Surface nesnemizin ölçeğini x ve y konumunda değiştirebiliyoruz. İkinci parametre ile x ölçeğini, üçüncü parametre ile y ölçeğini değiştiriyoruz. Ben burda x ölçeğine 1 değerini vererek resmin sağ ok tuşuna basılınca x yönünde dönmesini sağladım ve askerimiz de sağa bakmış oldu.

```text
import pygame, sys
from asker import Asker
pygame.init()

pencere = pygame.display.set_mode((500,400))

asker = Asker((250,50))

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((255,255,255))

    asker.update()

    pencere.blit(asker.image, asker.rect)
    pygame.display.flip()
    saat.tick(24)
```

asker.py den Asker sınıfını import ettik ve konumunu 250×50 ye ayarladık. Bu sefer Clock nesnesi oluşturduk ve 24 fps olacak şekilde ayarladık. Zaten sprite resimlerin sayısı az olduğu için hızlı akıyordu; bu sayede biraz da olsa hem kısmış olduk, hemde hareket ediş hızını azalttık. Oyun döngümüzde update\(\) methodunu yazdık bu sayede klavye ok tuşlarına bastığımızda kodlarımız çalışacaktır…

