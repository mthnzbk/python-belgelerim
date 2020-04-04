# Pygame ile Oyun Programlama - time Modülü

Frame Per Second. Yani saniyede ekrana verilen kare sayısı…

Her oyun döngüden ibarettir; ekrana verilen görüntü kare kare çizilir ve döngü ile oyun akışı sağlanır. Önceki derslerde gördüğünüz üzere bir while döngüsüyle oyunumuzun döngüsünü sağlıyorduk. Burada while döngüsünün saniyede kaç kere işlediğini bilemiyoruz.

Oyun yapım firmaları her oyun için fps değeri belirler. Eğer işlemciniz yeterli güçte değilse oyun döngüsü yavaş olacağından beklenen fps yi sağlayamaz ve oyun kesik kesik veya yavaş görünür. Bu da oyunun oynanma olasılığını etkileyen bir durumdur. Ayrıca fps değerinin değişiklik göstermesi oyunun bazı yerlerinde akışın hızlı bazı yerlerinde daha yavaş olmasına yol açar. Bunun nedenlerinden biri de ekrana çizilen nesnelerin çokluğu ve azlığıdır.

Bu zamana kadar yaptığımız örneklerde lüzum olmadığı için fps ile ilgili bir işlem yapmadık. Ancak şöyle bir durumda kullanım ihtiyacı doğabilir.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((500,400))

kare = pygame.Surface((100,100))
kare.fill((255,0,0))
kare_rect = pygame.Rect((0,0), kare.get_size())

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    if pygame.key.get_pressed()[pygame.K_UP]:
        kare_rect.y -= 2
    if pygame.key.get_pressed()[pygame.K_DOWN]:
        kare_rect.y += 2
    if pygame.key.get_pressed()[pygame.K_LEFT]:
        kare_rect.x -= 2
    if pygame.key.get_pressed()[pygame.K_RIGHT]:
        kare_rect.x += 2

    pencere.fill((0,0,0))

    pencere.blit(kare, kare_rect)
    pygame.display.flip()
```

Bu örnekte şu kısma odaklanmanızı istiyorum:

```text
if pygame.key.get_pressed()[pygame.K_UP]:
    kare_rect.y -= 2
if pygame.key.get_pressed()[pygame.K_DOWN]:
    kare_rect.y += 2
if pygame.key.get_pressed()[pygame.K_LEFT]:
    kare_rect.x -= 2
if pygame.key.get_pressed()[pygame.K_RIGHT]:
    kare_rect.x += 2
```

Burada olayları kullanmadan pygame.key modülünün get\_pressed\(\) fonksiyonuyla hangi tuş\(lar\)a basıldığını liste olarak alıyoruz. K\_UP, K\_DOWN gibi değişkenler sayı tuttuğu için bu listeden o sayıya denk gelen liste elemanının 1 mi 0 mı olduğuna bakıp işlem yapıyoruz. Bu satırlarda kırmızı bir kare yi bastığınız tuşlara göre ikişer pixel yer değiştirtiyoruz.

Örnek kodu test ettiğinizde ne kadar hızlı yer değiştirdiğini görmüş olmalısınız. Bizim amacımız burada kareyi hareket ettirmek, ama bu kadar hızlı bir şekilde yer değiştirmesini istemiyor olabiliriz. pygame.time modülünün sağladıklarıyla bunu kontrol altına alabiliriz.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((500,400))

kare = pygame.Surface((100,100))
kare.fill((255,0,0))
kare_rect = pygame.Rect((0,0), kare.get_size())

saat = pygame.time.Clock()
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    if pygame.key.get_pressed()[pygame.K_UP]:
        kare_rect.y -= 2
    if pygame.key.get_pressed()[pygame.K_DOWN]:
        kare_rect.y += 2
    if pygame.key.get_pressed()[pygame.K_LEFT]:
        kare_rect.x -= 2
    if pygame.key.get_pressed()[pygame.K_RIGHT]:
        kare_rect.x += 2

    pencere.fill((0,0,0))

    pencere.blit(kare, kare_rect)
    pygame.display.flip()
    saat.tick(60)
```

saat adında bir Clock nesnesi tanımladık. Bu nesne yardımıyla oyun döngümüzün saniyedeki kaç kere dönmesi gerektiğini söyleyeceğiz.

Oyun döngümüzün sonunda Clock nesnemizin tick methoduyla saniyede kaç kere döngü oluşması gerektiğini belirtiyoruz. 60 parametresiyle while döngüsü saniyede otuz kere dönecektir. Bu sayede karemizi daha düzgün bir şekilde hareket ettirebileceğiz.

Peki, biz oyun döngümüzün saniyede otuz kere döndüğünü nasıl anlayacağız?

```text
pygame.display.set_caption("FPS: {}".format(saat.get_fps()))
```

get\_fps\(\) methoduyla float olarak saniyedeki kare sayısını öğreniyoruz ve yukarıda ki kod parçasını oyun döngümüze ekleyerek pygame penceremizin başlığını fps değeri olarak değiştiriyoruz. Tabii bu değer hiç bir zaman sabit olmayacaktır. Çünkü bilgisayarınızdaki her hangi bir işlem, ya da oyunda ki bir olay sonucu fps değeri ufakta olsa değişecektir.

Pygame ile yaptığınız bir oyuna bu kadar basit bir şekilde fps değeri verebilirsiniz.

Pygame kodunuzu çalıştırdığınızda pygame.init\(\) in bulunduğu satır işlendiğinde siz uygulamayı sonlandırana kadar sürecek bir zaman akışı olacaktır. Kod çalışırken işlediğiniz her pygame.time.get\_ticks\(\) fonksiyonu o anda pygame.init\(\) işlendikten beri kaç saniye geçtiğini milisaniye olarak döndürür. Eğer bu fonksiyonu oyun döngünüzde print ederseniz çıktı olarak sürekli geçen saniyeyi milisaniye olarak gösterir.

```text
pygame.time.set_timer(pygame.QUIT, 10000)
```

set\_timer\(\) ile olay tipini ve tetiklenecek süreyi milisaniye olarak girerek bir zamanlayıcı oluşturabiliriz. Yukarıda ki kodda on saniye de bir QUIT tipinde olay kuyruğa eklenecektir. Bu olayı yakaladığınız kod varsa her on saniyede bu kod işlenecektir.

Sadece QUIT değil KEYDOWN, MOUSEBUTTONDOWN, USEREVENT gibi olay tiplerinide kullanabilirsiniz. Peki bu set\_timer\(\) ile ne yapabiliriz? Yazdığınız bir oyunun demosunu yayınlamaya karar verdiniz ve belirtilen süre kadar oynanabilsin istiyorsunuz. Basitçe yukarıdaki kodu kullanarak ve oynanma süresini ayarlıyarak kullanabilirsiniz.

1000_60_60 gibi bir parametre girerek bir saatlik oynama süresi koyabilirsiniz. Tabii ki olay kontrolünde ise şu kod olmalı:

```text
if event.type == pygame.QUIT:
    sys.exit()
```

Bu fonksiyon ile hayal gücünüz ile sınırlı kodlar yazabilirsiniz. İleriki derslerde lazım olursa daha iyi kavrayacağınızdan emin olun.

