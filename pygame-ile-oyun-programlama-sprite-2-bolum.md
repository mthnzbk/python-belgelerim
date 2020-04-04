# Pygame ile Oyun Programlama - Sprite 2. Bölüm

İlk Sprite dersinde Sprite sınıfını nasıl kullanacağımızı öğrendik.

Sprite sınıfımızda tanımladığımız self.image ve self.rect niteliğini çağırarak blit işlemini gerçekleştirdik ve update\(\) methoduna; Sprite’ımızın kontrolünü sağlayan kodlarımızı yazdıp, bu methodu oyun döngümüzde çağırdık ve oyun döngümüzün kod bakımından fakirleşmesini sağladık. Ama yine bir problemimiz var…

Yazdığımız oyuna bağlı olarak bir çok Sprite oluşturabiliriz ve hepsinin tek tek update\(\) methodlarını oyun döngümüze yazarak ve tek tek blit ederek kod zenginliğinde kafamızı karıştırabiliriz. Bu gibi bir durumu engellemek için Sprite grupları oluşturmamız gerekir. Pygame bize yeteri kadar gruplama yapmamızı sağlayan sınıf sağlar.

```text
import pygame, sys
from asker import Asker
pygame.init()

pencere = pygame.display.set_mode((500,400))

asker = Asker((250,50))
asker2 = Asker((100,50))
asker3 = Asker((200,150))
asker4 = Asker((50,100))


saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    asker.update()
    asker2.update()
    asker3.update()
    asker4.update()   

    pencere.blit(asker.image, asker.rect)
    pencere.blit(asker2.image, asker.rect)
    pencere.blit(asker3.image, asker.rect)
    pencere.blit(asker4.image, asker.rect)
    pygame.display.flip()
    saat.tick(24)
```

Bir önceki derste kullandığımız asker modülünü çağırdık ve dört adet Asker nesnesi oluşturduk. Oluşan kod yığınını gördünüz. Bu yığın Sprite nesnelerinize göre değişiklik gösterecektir.

Group sınıfı en temel Sprite gruplama sınıfıdır.

```text
grup = pygame.sprite.Group(asker,asker2,asker3,asker4)
```

Tek satır kod ile Sprite grubumuzu oluşturduk istersek Group sınıfının add\(\) methodu ile de spritelarımızı ekleyebiliriz.

```text
grup = pygame.sprite.Group()
grup.add(asker)
grup.add(asker2,asker3,asker4)
```

Tek tek ekleyebileceğiniz gibi, birden fazla Sprite nesnesini de ekleyebilirsiniz.

Group sınıfının birde update\(\) methodu vardır. Bu method ile gruba eklediğiniz bütün Sprite nesnelerinin update\(\) methodları çalıştırılır. Eğer Sprite sınıfınızda update\(\) methodunuz parametre alıyorsa diğer Sprite sınıflarınızda aynı parametreyi almalıdır ve Group\(\) nesnesini oluşturup update\(\) methodunu kullanırkende bu parametreyi es geçmemeliyiz. Farklı argüman alan methodlarda sorun yaşamanız büyük bir ihtimaldir.

Gruptaki Sprite nesnelere ulaşmak için ise sprites\(\) methodu mevcuttur. Bu method Sprite nesnelerini liste olarak döndürür.

```text
import pygame, sys
from asker import Asker
pygame.init()

pencere = pygame.display.set_mode((500,400))

asker = Asker((250,50))
asker2 = Asker((250,150))

grup = pygame.sprite.Group(asker,asker2)

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    grup.update()

    for sprite in grup.sprites():
        pencere.blit(sprite.image, sprite.rect)

    pygame.display.flip()
    saat.tick(24)
```

Yukarıdaki örnekte Asker nesnemizden iki adet oluşturduk ve Group sınıfı ile Sprite grubu oluşturduk. Group sınıfının update\(\) methodu ile bütün spritelerın update\(\) methodlarını da çalıştırmış olduk. Group sınıfının sprites\(\) methodunu kullanarak bir for döngüsü yardımıyla spritelarımızı ekrana çizdirdik.

Ekrana çizdirme işini daha basite indirgeyebiliriz. sprites\(\) methodunu kullanarak for döngüsüyle çizdirmek ve blit işleminde; her Sprite nesnesinin self.image ve self.rect niteliklerini yazmak fazladan kod satırıdır ve Sprite sınıfınızı genişlettiğinizde self.image niteliği yerine başka isim seçebilirsiniz. Bu da ayrı bir sorun olmaktadır.

Group sınıfının draw\(\) methodu gruptaki her Sprite nesnesini ekrana çizdirir. Biz sadece çizdireceğimiz yüzeyi parametre olarak veririz; gerisini Group nesnemiz hallediyor.

```text
import pygame, sys
from asker import Asker
pygame.init()

pencere = pygame.display.set_mode((500,400))

asker = Asker((250,50))
asker2 = Asker((250,150))

grup = pygame.sprite.Group(asker,asker2)

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    grup.update()

    grup.draw(pencere)
    pygame.display.flip()
    saat.tick(24)
```

Ana yüzeyimizi draw\(\) methoduna parametre olarak verdik ve Group nesnemiz, barındırdığı Sprite nesnelerini daha önceki örneğimizdekine benzer bir yolla ekrana çizdirdi.

Yazdığımız kodlar ilerledikçe kısalmaya başladı. Birazdan bir kaç satır uzayacak, ama bu şekilde daha fazla performans sağlayacağız.

Önceki yazının başında self.image ile self.rect niteliklerinin olması gerektiğini söylemiştim. Sebebi ise Sprite gruplarında, grupların bu nitelikleri çağırmasındandı…

Ana yüzeyimizle aynı boyutta ve renkte bir Surface nesnesi oluşturacağız. Bunun nedeni ise ekrana çizdirdiğimiz Sprite nesnelerinin hareketiyle oluşan kirliliği, Group sınıfının clear\(\) methodunu kullanarak temizleyeceğiz.

Oyun döngümüzde her daim mevcut olan pencere.fill\(\) in yaptığını yapan bu method, çizilen alanı temizlediğinden daha performanslıdır. Bu methodu kullanmak kişinin isteğine kalan bir durum, ama oyununuzu geliştirdikçe bir yavaşlama veya kasma hissettiğinizde aklınızda bulunsun.

```text
import pygame, sys
from asker import Asker
pygame.init()

pencere = pygame.display.set_mode((500,400))
temizle = pygame.Surface((500,400))

asker = Asker((250,50))
asker2 = Asker((250,150))

grup = pygame.sprite.Group(asker,asker2)

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    grup.clear(pencere, temizle)

    grup.update()

    grup.draw(pencere)
    pygame.display.flip()
    saat.tick(24)
```

temizle adında bir Surface nesnesi oluşturduk. Oyun döngümüzde pencere.fill\(\) in yerini alan grup.clear\(\) a ilk parametre olarak ana penceremizin örneğini yazdık. Bu sayede Group nesnemize hangi yüzeyi temizleyeceğini söyledik; ikinci parametre de ise temizle adındaki Surface nesnemizi yazdık. Group nesnemiz, temizle adındaki Surface nesnemizi kullanarak sprite animasyonumuzun önceki hareketindeki konumunu temizleyecektir ve döngü boyunca bu sürecektir.

Merak edenler temizle.fill\(\)'ı kullanarak farklı bir renge boyayarak Group nesnesinin temizlediği alanı daha net görebilirler.

Group sınıfının copy\(\) methodunu kullanarak da kendisinden bağımsız kopyasını oluşturabilirsiniz…

Group sınıfının dezavantajı olduğu gibi Spritelarında dezavantajı vardır. Pasif olması gerektiği halde bir Sprite’ın update\(\) methodunu çalıştırarak performans kaybedebilirsiniz. Group sınıfının dezavantajına ise bir sonraki derste konu içinde değineceğim.

