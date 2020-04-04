# pymunk 2B Fizik Kütüphanesi – Bölüm 1

[pymunk](http://www.pymunk.org/en/latest/) kullanımı basit 2 boyutlu oyunlar için hazırlanmış 2B fizik motorudur. Bu modül [chipmunk](http://chipmunk-physics.net/) üzerine inşa edilmiştir. pymunk’u [pygame](http://www.pygame.org/), [pyglet](http://www.pyglet.org/), [PySFML](http://www.sfml-dev.org/) gibi oyun kütüphaneleriyle kullanabileceğiniz gibi [PyQt](http://www.riverbankcomputing.co.uk/news) gibi grafik arayüz kütüphaneleriyle de kullanabilirsiniz.

pymunk modülünü öğrenirken pygame modülünden faydalanacağız…

pymunk ile daire, dikdörtgen ve çokgen şekilleri fizik kurallarına uydurabileceğimiz sınıflar ile eklem oluşturmak, engel oluşturmak gibi işlemler için de sınıflar mevcuttur.

pymunk modülünü “pip install pymunk” komutunu vererek kurabilirsiniz.

Kurulum tamamlandıktan sonra Python yorumlayıcısına

```text
import pymunk
```

yazarak test edebilirsiniz. Eğer ImportError hatası almıyorsanız sorun yok demektir. O zaman motoru çalıştıralım.

pymunk’ta fizik kurallarını uygulayacağımız bir alan oluşturmamız gereklidir. Bu işi Space\(\) sınıfı ile yapıyoruz. Daha sonra gravity özelliği ile yer çekiminin uygulanacağı yönü belirliyoruz…

```text
import sys
import pygame
from pygame.color import *
import pymunk

pygame.init()
ekran = pygame.display.set_mode((600, 600))
saat = pygame.time.Clock()

space = pymunk.Space()
space.gravity = (0.0, -900.0)

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
            sys.exit()

    ekran.fill(THECOLORS["white"])
    space.step(1/60.0)
    pygame.display.flip()
    saat.tick(60)
```

Örnekte görüldüğü gibi space değişkenine Space sınıfımızı atadık ve gravity özelliğine \(0.0, -900.0\) olarak atadık. İster bu şekilde yazalım, ister pymunk.Vec2d\(0.0, -900.0\) yazalım fark etmez. Sınıfımız doğru şekilde girilen veriyi kendisi Vec2d vektör nesnesine dönüştürecektir.

gravity özelliğine girilen ilk değer X koordinatını, ikincisi ise Y koordinatını temsil eder. Artı değer verilirse X için sağa, Y için yukarı doğru yer çekimi oluşur. Eksi değer girilirse tersi yönlere yer çekimi oluşur. Tabii yer çekimine maruz kalan nesnelerin vektörel değer girdiğimizden pygame koordinat tarzını ona göre ayarlamak gerekir.

Oyun döngümüzde 60 fps olarak ayarladığımız döngü sayısını fizik hesaplama süresiyle eşit tutuyoruz. step\(\) methoduna girdiğimiz değerde oyun döngüsüyle eşit hesaplama yapmasını sağlıyor. Burada amaç simülasyonun uyumlu olmasını sağlamaktır.

Alanımızı oluşturduğumuza göre şimdi geometrik şekilleri çizdirmeye çalışabiliriz…

İlk olarak fare imleciyle ekrana tıkladığımızda bir daire çizdirelim ve fizik kurallarına göre yer çekimiyle aşağı doğru hareket etmesini sağlayalım…

```text
def top_ekle(space):
    kutle = 1
    yaricap = 24
    eylemsizlik = pymunk.moment_for_circle(kutle, 0, yaricap)
    govde = pymunk.Body(kutle, eylemsizlik)
    pos = pygame.mouse.get_pos()
    govde.position = pos[0], 600-pos[1]
    sekil = pymunk.Circle(govde, yaricap)
    space.add(govde, sekil)
    return sekil

def top_cizdir(ekran, top):
    p = int(top.body.position.x), 600-int(top.body.position.y)
    pygame.draw.circle(ekran, THECOLORS["blue"], p, int(top.radius), 0)
```

top\_ekle ve top\_cizdir adında iki fonksiyon yazdık. top\_ekle fonksiyonundan başlayalım:

Çizdireceğimiz şekle göre bir moment hesabı yapılması gerekiyor. Daire çizdireceğimiz zaman moment\_for\_circle fonksiyonunu dairenin moment hesabını yapmak için kullanırız. Değişken adlarından anlayacağınız gibi ilk parametresine kütle değerini, üçüncü parametresine de dairenin yarıçapını giriyoruz. 0 olan ikinci parametre ise dairenin iç yarıçapını temsil ediyor. Yani delikli para gibi bir şey düşünüyorsanız ikinci parametreye yarıçap verebilirsiniz.

Oluşturacağımız şekiller Body adında bir gövdeye ait olmalıdırlar. Body nesnemizi oluştururken şeklimizin momentinden ve kütlesinden haberdar olmasını sağlıyoruz. Sonra da çizileceği an gelince fare imleci pozisyonuna göre, ama y ekseni ekran yüksekliğinden çıkartılarak çizilmesi sağlanıyor. Çünkü matematikten hatırlarsanız y ekseni aşağı doğru eksilere inerken pygame de aşağı doğru artar. Bunu tersine çevirmek için böyle bir şey yapıyoruz.

Body nesnemiz ve yarıçap bilgisiyle bir Circle nesnesi oluşturuyoruz ve space parametresinden yararlanarak Space nesnemize gövdemizi ve şeklimizi ekliyoruz. Fonksiyonumuz bize Circle nesnemizi döndürüyor…

top\_cizdir fonksiyonumuza ise ekran ve top parametrelerini almasını sağlıyoruz. p değişkeninde pygame’in anlayacağı koordinat verisini ayarlıyoruz ve draw.circle\(\) fonksiyonumuz ile ekrana mavi renkli ve çapı 48 pixel olan bir daire çizilmesini sağlıyoruz.

Şimdi de ana kodumuza ufak eklemeler yaparak tam kodu yazalım.

```text
import sys
import pygame
from pygame.color import *
import pymunk

def top_ekle(space):
    kutle = 1
    yaricap = 24
    eylemsizlik = pymunk.moment_for_circle(kutle, 0, yaricap)
    govde = pymunk.Body(kutle, eylemsizlik)
    pos = pygame.mouse.get_pos()
    govde.position = pos[0], 600-pos[1]
    sekil = pymunk.Circle(govde, yaricap)
    space.add(govde, sekil)
    return sekil

def top_cizdir(ekran, top):
    p = int(top.body.position.x), 600-int(top.body.position.y)
    pygame.draw.circle(ekran, THECOLORS["blue"], p, int(top.radius), 0)

pygame.init()
ekran = pygame.display.set_mode((600, 600))
saat = pygame.time.Clock()

space = pymunk.Space()
space.gravity = (0.0, -900.0)

toplar = []

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
            sys.exit()
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            top_sekli = top_ekle(space)
            toplar.append(top_sekli)

    ekran.fill(THECOLORS["white"])
    for top in toplar:
        top_cizdir(ekran, top)
    space.step(1/60.0)
    pygame.display.flip()
    saat.tick(60)
```

Evet toplar adında boş bir liste oluşturduk ve ekranda fare sol tuşuyla bastığımızda şeklimizin oluşmasını ve toplar listesine eklenmesini sağladık. Oyun döngümüzde ise listeye eklenen her topun sırayla çizilmesini sağladık. Kodu çalıştırıp topları sol fare tuşuyla oluşturmaya başladıktan sonra subliminal mesaj gibi mavi topların belirip kaybolduğunu görebilirsiniz. Bu durum ya modüllerden kaynaklanan bir durum ya da listeye eklenen ama ekrandan çıkmış toplarında çizilmeye çalışmasından kaynaklı olabilir. Bir sonraki devam dersimizde bu konuyuda halletmeye çalışacağız…

