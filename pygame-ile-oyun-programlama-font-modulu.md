# Pygame ile Oyun Programlama - font Modülü

Pygame font modülü, bir Surface nesnesinin içine TrueType yazı tiplerini işlemek için izin verir. Ekrana yazdıracağımız yazıları Font nesnesiyle oluştururuz ve Font nesnesiyle yazılarımızı işleriz. Örneğin, ekrana “Yeni Oyun” yazdıracağız ve bunun altı çizili ve eğik olmasını istiyoruz. Font nesnesinin methodları vasıtasıyla bu isteğimizi gerçekleştiririz. Bir Font nesnesi şöyle oluşturulur:

```text
yazi = pygame.font.Font("yazıtipi.ttf", 14)
```

Font nesnesinin ilk parametresi string olarak yazı tipi dosyasının adını ister. Eğer yazdığımız kodla yazı tipi aynı dizinde ise sadece adını yazmamız yeterli. Farklı bir dizindeyse tam yolunu yazmamız gerekir.

Bu halde yukarıdaki kod bize Font nesnesi döndürür. Biz ekrana Surface çizdirebildiğimizden bize lazım olan şey bir Surface nesnesi. Yukarıdaki koda bakarsak biz sadece yazı tipiyle yazının pixel olarak büyüklüğünü girdik, ama yazdıracağımız yazıyı henüz girmedik. Şuna bir bakalım:

```text
yazi = pygame.font.Font("yazıtipi.ttf", 14)
surface = yazi.render("Yeni Oyun", True, (255,255,255), None)
```

Font nesnesinin render\(\) methodu, bizden gerekli bilgileri alıp bir surface nesnesi döndürür. Biz bu kodda “Yeni Oyun” kelimelerini yazdıracağımızı, True ile antialias olacağını, \(255,255,255\) ile yazı renginin beyaz olacağını söyledik. None parametresi ise yazının arkaplan renginin olmayacağını belirtir. Son parametre varsayılan olarak None dir. Yani yazmanıza gerek yoktur. Yani arkaplan rengi girmek istemiyorsanız bu kısmı atlayın.

Bizim ihtiyacımız olan Surface nesnesiydi ve render\(\) methoduyla istediğimiz şekilde bir yazıyı Surface nesnesine çevirdik. Şimdi örnek bir kodla bunu ekrana yazdıralım.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

yazi = pygame.font.Font("akaDora.ttf", 20)
surface = yazi.render("Yeni Oyun", True, (255,255,255))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()


    pencere.fill((0,0,0))

    pencere.blit(surface, (100,100))
    pygame.display.flip()
```

Örneğimizde “akaDora.ttf” adlı yazı tipini kullandım ve karakterlerin boyutunu 20 pixel büyüklüğünde olacak şekilde ayarladım. render\(\) methodu ile de yumuşatmalı, beyaz renkte “Yeni Oyun” kelimeleriyle bir Surface nesnesi meydana getirdim ve surface değişkenine atadım. Daha önceki derslerde gördüğümüz gibi pencere yüzeyimizin blit\(\) methoduyla 100×100 konumuza bu yazıyı yazdırdık. Ondan önce gelen fill\(\) methodu da ekranımızı temizleyerek güncellenme olması durumunda oluşacak kirlenmeleri önlüyor.

Ekrana yazdırdığımız bir yazıyı eğik, kalın ve altı çizili yapabiliriz. Her biri için bir ayrı methodlar mevcuttur. Kavraması çok kolay olduğundan tek bir örnekte bu methodları öğrenelim.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

yazi = pygame.font.Font("GentiumPlus.ttf", 20)
yazi.set_bold(True)
yazi.set_underline(True)
yazi.set_italic(True)

surface = yazi.render("Yeni Oyun", True, (255,255,255))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pencere.blit(surface, (100,100))
    pygame.display.flip()
```

Bu örneğimizde bir Font nesnesi oluşturduk; set\_bold\(\) methoduyla yazdıracağımız yazıyı kalın, set\_underline\(\) methodu ile altı çizili, set\_italic\(\) methodu ile de eğik hale getirdik. render\(\) methodu ile Surface nesnesine dönüştürdük. Dönüştürülen Surface nesnesini blit\(\) ile ekrana 100×100 konumuna yazdırdık.

Yazdırdığımız yazının eğik mi, kalın mı, altı çizili mi olduğunu ise get methodlarıyla öğreniriz:

```text
print(yazi.get_bold())
print(yazi.get_underline())
print(yazi.get_italic())
```

Font nesnesinin render\(\) methoduyla yazdıracağımız yazıyı Surface nesnesine dönüştürüldüğünü öğrendik. Her Surface nesnesinin bir uzunluğu ve yüksekliği vardır. Font nesnesinin get\_height\(\) methodu bize Surface nesnesinin yüksekliğini verir. get\_linesize\(\) methodu da genelde get\_height\(\) methodu ile aynı sayıyı döndürür.

Peki, bu get\_linesize\(\) methodu bizim ne işimize yarayabilir? Birden fazla yazıyı alt alta yazdırıyorsak, bu methodun döndürdüğü değeri, yazdırdığımız blit\(\) methodunda verdiğimiz yükseklik değerine artı olarak eklersek orantılı bir görünüm elde ederiz.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

yazi = pygame.font.Font("akaDora.ttf", 20)
satir = yazi.get_linesize()

surface = yazi.render("Yeni Oyun", True, (255,255,255))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pencere.blit(surface, (100,100))
    pencere.blit(surface, (100,100+satir))
    pencere.blit(surface, (100,100+satir+satir))
    pygame.display.flip()
```

Örneğimizde “satir” değişkenine get\_linesize\(\) methodunun döndürdüğü değeri atadık ve oyun döngümüzde bunu kullandık. Aynı Surface nesnesini alt alta olacak şekilde yüksekliğine “satir” değişkeninide ekleyip orantılı olarak yazdırdık.

Font sınıfıyla hep aynı dizinde bulunan yazı tipi dosyalarını kullandık. Sistemde yüklü yazı tiplerini kullanmak istiyorsak sistemde yüklü bulunan yazı tipinin tam yolunu belirtmeniz gerekir. SysFont\(\) fonksiyonu bizi uzun uzun yazı tipinin yolunu yazdırmak yerine sadece yazı tipinin adını yazmamızı istiyor. SysFont\(\) girdiğimiz yazı tipi adını kontrol ediyor ve varsa bu yazı tipini kullanıyor. Ayrıca pratiklik amacıyla kalınlık ve eğiklik durumunu parametre olarak alıyor ve bize bir Font nesnesi döndürüyor. Bize kalan da bu Font nesnesini render\(\) methoduyla Surface nesnesine dönüştürmek kalıyor.

Bir örnekle açıklayalım:

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

yazi = pygame.font.SysFont("ubuntu", 30, 0, 1)
surface = yazi.render("Ubuntu yazı tipi.", True, (0,255,0))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pencere.blit(surface, (30,130))
    pygame.display.flip()
```

Örnekte SysFont\(\) fonksiyonunu kullandık ve ubuntu.ttf yazı tipini kullanmasını, 40px büyüklüğünde olmasını ve kalın değil, ama eğik olmasını söyledik. SysFont\(\) fonksiyonu bize Font nesnesi döndürdüğü için fazladan bir ekleme yapmadan render\(\) methodumuzu aynen kullandık ve ekranın 30×130 konumuna yeşil renkte “Ubuntu yazı tipi.” yazısını yazdırdık.

Hızlı bir yazı yazdırma yöntemi olduğundan bu fonksiyonu kullanabilirsiniz…

Pygame, beraberinde kendi varsayılan yazı tipiyle gelir. Bu yazı tipinin adı “freesansbold.ttf”dir. Pygame’in kurulu olduğu dizine bakarsanız bu yazı tipini görebiliriniz. Varsayılan yazı tipini şöylede öğrenebiliriz:

```text
print(pygame.font.get_default_font())
```

Bu kod parçası size pygame in varsayılan yazı tipini döndürecektir.

Sistemde mevcut bütün yazı tiplerini görmek isterseniz de get\_fonts\(\) fonksiyonunu kullanabilirsiniz.

```text
print(pygame.font.get_fonts())
# ya da
for font in pygame.font.get_fonts():
    print(font),
# Bu daha güzel bir çıktı verecektir.
```

Sistemdeki mevcut yazı tiplerini kullanmak istiyor, ama SysFont\(\) fonksiyonunu kullanmak istemiyorsanız, SysFont\(\) gibi yazı tipinin adını yazınca yolunu döndürecek bir fonksiyon gerekecek…

```text
print(pygame.font.match_font("ubuntu"))
/usr/share/fonts/truetype/ubuntu-font-family/Ubuntu-L.ttf
```

Örnekte de görüldüğü gibi match\_font\(\) fonksiyonu ile “ubuntu” yazı tipini arattık ve bize dosya yolunun çıktısını verdi.

```text
import pygame, sys

pygame.init()
pencere = pygame.display.set_mode((300,300))

yazi_tipi = pygame.font.match_font("ubuntu")
yazi = pygame.font.Font(yazi_tipi, 20)
surface = yazi.render("Yeni Oyun", True, (255,255,255))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pencere.blit(surface, (100,100))
    pygame.display.flip()
```

Bu koddaki gibi bir kullanımla yazı tipimizi kullanabiliriz. Eğer kullanacağımız yazı tipinin kalın ve/veya eğik bir yazı tipi olmasını istiyorsak match\_font\(\) fonksiyonunun ikinci ve üçüncü parametrelerine True veya False verebilirsiniz.

```text
print(pygame.font.match_font("ubuntu", bold=True, italic=True))
/usr/share/fonts/truetype/ubuntu-font-family/Ubuntu-BI.ttf
```

