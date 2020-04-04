# Pygame ile Oyun Programlama - image Modülü

[Surface](pygame-ile-oyun-programlama-surface-modulu.md) modülünü anlatırken pygame.image modülünü de kullanmıştık. Pygame içinde mevcut olan image modülü ile var olan bir resim dosyasını pygame penceresinde göstermek için yükleyip Surface nesnesi yapabilir ya da bir Surface yi resim dosyası olarak kayıt edebiliriz.

Surface modülünü anlatırken bir resim dosyasının load\(\) methodu ile yüklendiğini görmüştük. Bu method; jpg, png, gif\(animasyonsuz\), bmp, pcx, tga, tif, lbm, pbm, xpm uzantılı resim dosyalarını yükleyebilir. Bir Surface yi resim olarak kaydeden save\(\) methodu ise; bmp, tga, png, jpeg uzantılı olarak kaydedebilir.

Peki bu save\(\) methodunu ne amaçla kullanabiliriz? Oynadığımız bazı oyunlarda ekran görüntüsü almak için bir kısayol tuşu vardır. Bu tuşa bastığımızda oyun, o anda ki görüntüyü alır ve resim dosyası olarak kaydeder. Bu methodda bize bu imkanı sağlar.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

resim = pygame.image.load("sprite.png")
resim.set_colorkey((14,248,0))

resim_cek = False
while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()
        if i.type == pygame.KEYDOWN:
            if i.key == pygame.K_F10:
                resim_cek = True
        if i.type == pygame.KEYUP:
            if i.key == pygame.K_F10:
                resim_cek = False

    if resim_cek:
        pygame.image.save(pygame.display.get_surface(), "ekran_görüntüsü.png")

    pencere.fill((0,0,0))

    pencere.blit(resim, (100,100))
    pygame.display.flip()
```

Yukarıdaki örnekte bir resim dosyasını image modülünün load\(\) methoduyla yükledik ve yüklenen resim bir Surface nesnesine dönüştüğü için set\_colorkey\(\) methoduyla resimdeki yeşil renkte olan arkaplanı şeffaf yaptık. Oyun döngüsünde klavye olaylarını kontrol eden bir kod yazdık.

```text
if i.type == pygame.KEYDOWN:
    if i.key == pygame.K_F10:
        resim_cek = True
if i.type == pygame.KEYUP:
    if i.key == pygame.K_F10:
        resim_cek = False
```

Bu kodda F10 tuşuna basıldığı anda resim\_cek değişkeni True değeri alıyor. Tuşa basmayı bıraktığımızda ise False değerini alıyor. Ana döngüde ki;

```text
if resim_cek:
    pygame.image.save(pygame.display.get_surface(), "ekran_görüntüsü.png")
```

kodumuz da resim\_cek değişkeni True ise image modülünün save\(\) methoduyla ana yüzeyin o anda ki görüntüsünü “ekran\_görüntüsü.png” adıyla kodumuzun bulunduğu dizine kayıt ettiriyoruz.

Yalnız bu örnek kod mükemmel sayılmaz. Örneğin; biz her basışta tarih-saat.png formatında kayıt ediyorsak her F10 tuşuna basınca farklı isimlerle ekran görüntüsü oluşacaktır. Bu olağan bir durum, ama burdaki sorun klavye olayında ortaya çıkıyor. Kodumuzu inceleyecek olursak tuşa basıldığı an resim\_cek değişkeni True değerini alacaktır ve ne kadar hızlı basıp çeksek de en kötü ihtimalle 3-4 kere döngü olacak ve bir basışta birden fazla ekran görüntüsü kayıt edecektir. Bu durumda çöp resim dosyalarına yol açacaktır. Bunu engellemek için daha uygun bir yol şöyledir:

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

resim = pygame.image.load("sprite.png")
resim.set_colorkey((14,248,0))

resim_cek = False
while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()
        if i.type == pygame.KEYDOWN:
            if i.key == pygame.K_F10:
                resim_cek = True

    if resim_cek:
        pygame.image.save(pygame.display.get_surface(), "ekran_görüntüsü.png")
        resim_cek = False

    pencere.fill((0,0,0))

    pencere.blit(resim, (100,100))
    pygame.display.flip()
```

Dikkat ettiyseniz F10 tuşunun bırakılma olayını kaldırdık ve o olaydaki “resim\_cek = False” kodunu if bloku altına aldık. Bu sayede siz istediğiniz kadar F10 tuşuna basılı tutsanız dahi resimi kaydettikten sonra resim\_cek değişkeni False olacağından if bloku işlenmeyecektir.

Eventlarda her olay bir kere algılanır ve tekrar o olayı yakalamak için o olayı bırakıp tekrar olay yaratmalısınız. Daha anlaşılır bir ifadeyle F10 tuşuna basılı tuttuğunuz süre boyunca sadece ilk bastığınız anda olay oluşacaktır ve tekrar etmeyecektir. Tabii biz bu durumu tek satır bir kod ilavesiyle değiştirebiliriz, ama bunu event modülünü işleyeceğim derste anlatacağım…

