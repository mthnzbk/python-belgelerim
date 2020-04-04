# Pygame ile Oyun Programlama - surface Modülü

Surface modülü pygame de en çok kullanılan modüldür. pygame.image.load\(\) ile yüklediğimiz resimler, draw ve gfxdraw ile çizdirdiğimiz şekiller, font modülü ile yazdığımız yazılar… Hepsi Surface nesnesine dönüştürülür ve bu şekilde üzerinde işlem yapılır. Bu modülün en çok kullanılan methodlarını öğreneceğiz.

Örnek bir Surface nesnesi şöyle oluşurulur:

```text
import pygame

pygame.init()

kare = pygame.Surface((100,100))
```

100×100 pixellik bir Surface nesnesi oluşturduk… Surface modülünü pygame.Surface şelinde çağırabileceğimiz gibi pygame.surface.Surface şeklinde de çağırabilirsiniz.

Bir Surface nesnesini ana yüzeye çizdirmek için blit\(\) methodunu kullanırız. Bu method ilk parametresinde çizeceği yüzeyin örneğini ister. İkinci parametresinde ise yüzeyinin hangi konumuna çizeceğini söyleyecek iki elemanlı bir tuple veya liste bekler.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))
kare = pygame.Surface((100,100))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.fill((255,255,255))
    pencere.blit(kare,(100,100))
    pygame.display.flip()
```

Örnek kodumuzda ana Surface nesnemiz “pencere” oluyor. pencere.blit\(kare, \(100,100\)\) ile “kare” örneğine atadığımız 100×100 pixellik bir Surface nesnesini 100×100 koordinatına çizdiriyoruz. Örnek kodda değişiklikler yaparak iyice kavrayabilirsiniz.

İleride göreceğimiz pygame.image modülünde resim dosyalarını yüklemeyi ve onları yönetmeyi öğreneceğiz. O zaman da yüklediğimiz her resim bir Surface nesnesi olacak. Bu yükleyeceğimiz resimleri daha hızlı ekrana çizilmesini sağlamak için convert\(\) ve convert\_alpha\(\) methodlarını kullanıyoruz. Eğer resim dosyası alpha değeri içeriyor ise convert\_alpha\(\) methodunu kullanmalıyız. Yoksa convert\(\) methodu yeterlidir.

```text
import pygame, sys
pygame.init()

pencere = pygame.display.set_mode((300,300))

resim1 = pygame.image.load("seffaf_resim.png")
resim1.convert_alpha()
resim2 = pygame.image.load("normal_resim.png")
resim2.convert()

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.blit(resim1, (10,10))
    pencere.blit(resim2, (100,100))
    pygame.display.flip()
```

Örneğimizde şeffaflık içeren resim için convert\_alpha\(\) methodunu, şeffaflık içermeyen resim için convert\(\) methodunu kullandık ve pencere.blit\(\) ile ayrı ayrı çizdirdik. Bu örnekte resim dosyalarını kendiniz seçerek sonuçları inceleyebilirsiniz.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

kare = pygame.Surface((100,100))
kopya_kare = kare.copy()

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.fill((255,255,255))

    pencere.blit(kare,(100,100))
    pencere.blit(kopya_kare,(0,100))
    pygame.display.flip()
```

Bazı durumlarda bir Surface nesnesini kopyalama ihtiyacı duyabiliriz. copy\(\) methodu Surface nesnesinin bir kopyasını döndürür. Biz yukarıdaki kodda “kopya\_kare” örneğimize “kare.copy\(\)” ile “kare” örneğinin bir kopyasını “kopya\_kare” örneğine aktarmış olduk. Kopyalanan bir Surface nesnesinde yapılan değişiklikler kopya Surface nesnesini etkilemeyeceği gibi tam tersi durumda da etkilemez. Eğer biz yukarıdaki;

```text
kopya_kare = kare.copy()
```

…satırını,

```text
kopya_kare = kare
```

…yapsaydık; herhangi bir örneğimizde yapacağımız değişiklik her iki örneğide etkileyecekti. Bunu test ederek daha iyi kavrayabilirsiniz.

Üstteki kodumuzda da kullandığımız fill\(\) methodu Surface nesnemizi belirttiğimiz renkle boyar. Mesela biz yukarıdaki kodda pygame penceresini beyaz renkte boyamasını sağlıyoruz. Bu methodu sadece rengini belirtmekte kullanmıyoruz. Ana Surface nesnemiz olan “pencere” ye çizdirdiğimiz bir çok nesnenin pozisyon değişikliklerinde oluşacak kirlenmeleri temizlemeye yarar. İleriki derslerde daha açıklayıcı anlatılacaktır.

Sprite animasyonlu bir oyun yapmayı planlıyorsunuz ve araştırmanız sonucu istediğin Sprite resmini buldunuz, ama baktınız ki resim dosyasındaki çizimlerin arkaplanı şeffaf değil ve bunu ekrana çizdirdiğinizde arkaplan rengide ekrana basılacaktır. Bu durumda çok kötü bir görünüm elde etmiş olacaksınız. Bu durumdan kurtulmak için Surface modülü set\_colorkey\(\) methodunu içerir.

```text
import pygame, sys

pygame.init()
pencere = pygame.display.set_mode((300,300))

resim1 = pygame.image.load("sprite.png")
resim2 = resim1.copy()
resim2.set_colorkey((14,248,0))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.blit(resim1, (10,10))
    pencere.blit(resim2, (100,10))
    pygame.display.flip()
```

Örneğimizde “sprite.png” adında bir resim dosyasını yükledim ve “resim2″ değişkenine kopyasını aldım. Bu kopya Surface nesnemizin \(14,255,0\) renk koduna denk gelen pixelleri şeffaf yapmasını set\_colorkey\(\) methoduyla sağladım ve her iki resmi de öncesi ve sonrası gibi ekrana çizdirdim.

```text
print(resim2.get_colorkey())
```

Bu kodla da set\_colorkey\(\) methoduyla şeffaflaştırılan renk kodunu tuple olarak alabilirsiniz.

Surface nesnesine şeffaflık vermek isteyebilirsiniz. Bunu yapmak için Surface modülünün set\_alpha\(\) methodunu kullanmalısınız. Bu method integer olacak şekilde tek bir parametre alır. Bu parametreyi 0-255 sayıları arası bir değer verebilirsiniz. Sıfıra yakın olan değerlerde şefaflık üst düzeyde olacaktır. Yüksek değer girişlerde ise matlık oranı yükselecektir.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

resim1 = pygame.image.load("sprite.png")
resim2 = resim1.copy()
resim1.set_alpha(120)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pencere.blit(resim1, (10,10))
    pencere.blit(resim2, (100,10))
    pygame.display.flip()
```

Şeffaflık olsun ya da başka bir şey, ana yüzeyi temsil eden Surface nesnesi fill\(\) methoduyla temizlenmelidir. Aksi halde yukarıda ki koddan bu kısmı çıkarırsanız resmin şeffaflığı görülmeyecektir. Ekranı temizleyen ve boyayan fill\(\) methodu olmadığı için yapılan her şey üst üste çizilecek ve kat kat çizilen resim mat görünecektir.

Eğer set\_alpha\(\) methoduyla şeffaflık verdiysek ya da Surface nesnemizi convert\(\) veya convert\_alpha\(\) methodlarıyla dönüştürmemişsek get\_alpha\(\) methodu şeffaflık varsa değerini döndürecektir.

Bir Surface nesnesinin uzunlukxyükseklik bilgisini öğrenmek istiyorsak get\_size\(\) methodunu kullanırız. Bu method \(uzunluk, yükseklik\) formatında değer döndürür. Eğer uzunluğu ayrı, yüksekliği ayrı almak istersek uzunluk için get\_width\(\) methodu, yükseklik için ise get\_height\(\) methodunu kullanırız.

Her Surface nesnesi görünüşte olmasa da temelde dikdörtgen bir alanı kaplar. get\_rect\(\) methodu Surface nesnesinin ekrandaki yerini ve boyutunu belirten bir Rect nesnesi döndürür. Eğer,

```text
print(pencere.get_rect())
```

kodunu yukarıdaki örneklerden birine yazarsanız ana yüzeyin Rect bilgisinin çıktısını alırsınız:

Bir Surface yi hangi konuma çizdirseniz çizdirin, konum bilgisini veren ilk iki parametre sıfır olacaktır. Biz bu Surface yi blit\(\) methodu ile çizdirdiğimizde blit\(\) methodu bize get\_rect\(\) methodunda olduğu gibi Rect nesnesi döndürecektir ve…

```text
pencere.blit(resim1, (10,10))
```

kodunun çıktısı  olacaktır. Bu çıktıdan anlayacağınız blit\(\) methoduyla hangi konuma çizdirirsek dönen rect nesnesinde konum bilgiside o olur.

Uygulamalı dersler başladığı zaman Rect nesnesini daha iyi anlayacağız ve yazacağımız oyunlarda nesne hareketlerinde çokça kullanacağız…

