# pymunk 2B Fizik Kütüphanesi – Bölüm 2 Son

Bu dersimizde simülasyonumuzu geliştirmeye devam ediyoruz.

Düşen topların bir engelle karşılasmasını sağlayacağız.

```text
def sabit_cizgi_ekle(space):
    govde = pymunk.Body(pymunk.inf, pymunk.inf)
    govde.position = (300,200)
    l1 = pymunk.Segment(govde, (-200, 0), (200.0, 0.0), 5.0)
    l2 = pymunk.Segment(govde, (-200.0, 0), (-200.0, 50.0), 5.0)
    l3 = pymunk.Segment(govde, (200.0, 0), (200.0, 50.0), 5.0)

    space.add_static(l1, l2,l3)
    return l1,l2,l3

def to_pygame(p):
    """pymunk-pygame koordinat düzenlemesi yapar."""
    return int(p.x), int(-p.y+600)

def cizgileri_ciz(ekran, cizgiler):
    for cizgi in cizgiler:
        body = cizgi.body
        pv1 = body.position + cizgi.a.rotated(body.angle)
        pv2 = body.position + cizgi.b.rotated(body.angle)
        p1 = to_pygame(pv1)
        p2 = to_pygame(pv2)
        pygame.draw.line(ekran, THECOLORS["red"], p1, p2)
```

sabit\_cizgi\_ekle\(\) fonksiyonuna yine space adında bir parametre veriyoruz. Fonksyionumuzda bir gövde tanımladık ve kütlesiyle momentini pymunk.inf değişkeniyle sonsuz yaptık. Bir nevi duvara monteleme işlemide denebilir sanırım… Gövdemizin pozisyonunu vektörel olarak 300×200 olarak belirledik. l1, l2, l3 değişkenlerine Segment nesnesi tanımladık. İlk parametreyle gövdesini diğer iki parametresini pygame.draw.line\(\) da gördüğümüz gibi çizgi koordinatlarını belirledik. Son koordinat ise çizginin çapını belirliyor; daha doğrusu kalınlığı diyebiliriz…

Sabit olarak ekleyeceğimiz nesneleri Space sınıfımızın add\_static\(\) methodu ile ekliyoruz ve fonksiyonumuzda bu şekilde ekledikten sonra fonksiyonumuzun bu çizgileri döndürmesini sağladık.

cizgileri\_ciz\(\) fonksiyonuna, çizgileri çizeceği yüzeyi belirtmek için ekran parametresiyle; sabit\_cizgi\_ekle\(\) fonksiyonundan dönen Segment nesnelerini grafik olarak çizdirmek için de cizgiler parametresini ekledik. Çizgileri bir for döngüsü yardımıyla tek tek pygame.draw.line\(\) ile ekrana çizdirilmesini sağladık. pv1 ile pv2 de çizgilerin Space deki pozisyonlarını ayarladık. rotated\(body.angle\) da ise çizgilerin bir açısı varsa ona göre bir düzenleme yapılması sağlanıyor. Eğer bir çizgi 45 derece aksi yöne yatıksa Vektörde o kısımın değeri artış ya da azalış gösterecektir.

p1 ve p2 de ise önceki dersimizde pygame’e özgü koordinat çevirme işlemi yapılıyor. Bu çevrimi yapılmış koordinatlar çizginin simülasyondaki yerine göre çizilmesini sağlıyor.

```text
import sys
import pygame
from pygame.color import *
import pymunk

#def top_ekle(space):
#def top_cizdir(ekran, top):
# Yer kaplamaması için bu şekilde gösterildi. Siz kodunuzu olduğu gibi bırakın.

def sabit_cizgi_ekle(space):
    govde = pymunk.Body(pymunk.inf, pymunk.inf)
    govde.position = (300,200)
    l1 = pymunk.Segment(govde, (-200, 0), (200.0, 0.0), 5.0)
    l2 = pymunk.Segment(govde, (-200.0, 0), (-200.0, 50.0), 5.0)
    l3 = pymunk.Segment(govde, (200.0, 0), (200.0, 50.0), 5.0)
    space.add_static(l1, l2,l3)
    return l1,l2,l3

def to_pygame(p):
    """pymunk-pygame koordinat düzenlemesi yapar."""
    return int(p.x), int(-p.y+600)

def cizgileri_ciz(ekran, cizgiler):
    for cizgi in cizgiler:
        body = cizgi.body
        pv1 = body.position + cizgi.a.rotated(body.angle)
        pv2 = body.position + cizgi.b.rotated(body.angle)
        print cizgi.a.rotated(1.0), cizgi.b.rotated(0.0)
        p1 = to_pygame(pv1)
        p2 = to_pygame(pv2)
        pygame.draw.line(ekran, THECOLORS["red"], p1, p2)

pygame.init()
ekran = pygame.display.set_mode((600, 600))
saat = pygame.time.Clock()

space = pymunk.Space()
space.gravity = (0.0, -900.0)

cizgiler = sabit_cizgi_ekle(space)
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
            pass

    ekran.fill(THECOLORS["white"])
    for top in toplar:
        top_cizdir(ekran, top)
    cizgileri_ciz(ekran, cizgiler)
    space.step(1/60.0)
    pygame.display.flip()
    saat.tick(60)
```

Örnekteki kodda cizgiler değişkenine sabit çizgilerin listesini tutturduk ve oyun döngümüzde cizgileri\_ciz\(\) fonksiyonumuza bu değişkeni parametre olarak vererek çizgilerimizi ekrana çizilmesini sağladık. Şimdi bu kodu çalıştırdığınızda bir hata yapmadıysanız fare tıklamalarınızda tepsi gibi bir alana top doldurabilirsiniz.

Şimdi sabit duran bu çizgileri bir pervanenin kanadı gibi bir noktasından iğneleyelim. Bunun için sabit\_cizgi\_ekle\(\) fonksiyonunun adını cizgi\_ekle\(\) olarak değiştiriyoruz ve

```text
def cizgi_ekle(space):
    devir_govdesi = pymunk.Body()
    devir_govdesi.position = (300,200)
    govde = pymunk.Body(50, 10000)
    govde.position = (300,200)
    l1 = pymunk.Segment(govde, (-200, 0), (200.0, 0.0), 5.0)
    l2 = pymunk.Segment(govde, (-200.0, 0), (-200.0, 50.0), 5.0)
    devir_eklemi = pymunk.PinJoint(govde, devir_govdesi, (0,0), (0,0))
    space.add(l1, l2, govde, devir_eklemi)
    return l1,l2
```

bu şekilde bir kod ortaya çıkarıyoruz… İlk olarak devir\_govdesi adında yeni bir Body nesnesi oluşturuyoruz ve diğer Body nesnemizle aynı pozisyona getiriyoruz. Çizgileri tutan gövdemiz artık sabit olmayacağı için 50 kütleli ve 10000 momente sahip oluyor. Sağ taraftaki l3 çizgisinide çıkarıyoruz.

devir\_eklemi adında bir PinJoint nesnesi oluşturduk. Bunu bir çubuğun ortasına saplanmış iğne gibi düşünebilirsiniz. PinJoint sınıfının ilk iki parametresi a ve b noktaları olmak üzere koordinat verisi alır; diğer iki parametree farklı bir değer vermeyecekseniz yazmanıza gerek yoktur. Hakkında pek bilgim yok; onun için rasgele değerler vererek ne işe yaradığını çıkartabilirsiniz.

Space sınıfının add\_static\(\) methodu yerine de add\(\) methodunu kullandığımıza dikkat edin…

Son olarak şu satırı

```text
cizgiler = sabit_cizgi_ekle(space)
```

şu şekilde değiştiriyoruz:

```text
cizgiler = cizgi_ekle(space)
```

Kodumuzu çalıştırıp fare sol tuşuyla top oluşturarak topun çizgilere temas etmesiyle çizgilerin pervane gibi döndüğünü göreceksiniz. Tabii ki; bunu bu şekilde bırakmayacağız. Pervane gibi dönmesini engelleyip kısıtlı olarak sağ-sol aşağı doğru eğilmesini sağlayacağız.

```text
def cizgi_ekle(space):
    devir_govdesi = pymunk.Body()
    devir_govdesi.position = (300,200)
    sinirli_devir_govdesi = pymunk.Body()
    sinirli_devir_govdesi.position = (200,200)
    govde = pymunk.Body(50, 10000)
    govde.position = (300,200)
    l1 = pymunk.Segment(govde, (-200, 0), (200.0, 0.0), 5.0)
    l2 = pymunk.Segment(govde, (-200.0, 0), (-200.0, 50.0), 5.0)
    devir_eklemi = pymunk.PinJoint(govde, devir_govdesi, (0,0), (0,0))
    eklem_siniri = 25
    sinirli_devir_eklemi = pymunk.SlideJoint(govde, sinirli_devir_govdesi, (-100,0), (0,0), 0, eklem_siniri)
    space.add(l1, l2, govde, devir_eklemi, sinirli_devir_eklemi)
    return l1,l2
```

Fonksiyonumuz biraz daha gelişti… sinirli\_devir\_govdesi adında yeni bir Body nesnesi tanımladık ve 200×200 pozisyonuna ayarladık. sinirli\_devir\_eklemi adında ise SlideJoint nesnesi tanımladık. İlk iki parametre PinJoint sınıfındaki gibidir. Üç ve dördüncü parametrelerde PinJoint sınıfının son iki parametresiyle aynı işleve sahiptir. parametre adı anchor olduğundan en uygun anlamı çapa oluyor. Buna göre x koordinatı -100 olan yere çapa atılmış oluyor. Belgelendirme yeterli düzeyde olmadığından ancak bu kadarı çıktı malesef… Son parametre eklem\_siniri ise çizginin ne kadar kaydırılılabileceğini\(?\) söylüyor. Buna da farklı değerler vererek pekiştirme yapabilirsiniz. Son olarakta Space nesnemizin add\(\) methoduna SlideJoint nesnemizi ekliyoruz. Fonksiyonla işimiz burada bitiyor…

Önceki derste listeye eklenen topların ekranda olmamasına rağmen listede yer işgal ettiğini ve bunlarında programın ekrana çizmeye çalıştığını söylemiştik. Ekleyeceğimiz tek satır kod ile y koordinatı sıfırın altına düşen topları listeden çıkartacağız ve olası şişme ve performans sorununun önüne geçeceğiz.

```text
for top in toplar:
    top_cizdir(ekran, top)
```

Oyun döngümüzdeki bu for döngüsüne

```text
if top.body.position.y < 0: toplar.remove(top)
```

kodunu ekleyerek

```text
for top in toplar:
    if top.body.position.y < 0: toplar.remove(top)
    top_cizdir(ekran, top)
```

Bu şekle getiriyoruz. Artık kodumuz sağlıklı bir şekilde çalışmalı. Kodumuzun son hali şu şekilde:

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

def cizgi_ekle(space):
    devir_govdesi = pymunk.Body()
    devir_govdesi.position = (300,200)
    sinirli_devir_govdesi = pymunk.Body()
    sinirli_devir_govdesi.position = (200,200)
    govde = pymunk.Body(50, 10000)
    govde.position = (300,200)
    l1 = pymunk.Segment(govde, (-200, 0), (200.0, 0.0), 5.0)
    l2 = pymunk.Segment(govde, (-200.0, 0), (-200.0, 50.0), 5.0)
    devir_eklemi = pymunk.PinJoint(govde, devir_govdesi, (0,0), (0,0))
    eklem_siniri = 25
    sinirli_devir_eklemi = pymunk.SlideJoint(govde, sinirli_devir_govdesi, (-100,0), (0,0), 0, eklem_siniri)
    space.add(l1, l2, govde, devir_eklemi, sinirli_devir_eklemi)
    return l1,l2

def to_pygame(p):
    return int(p.x), int(-p.y+600)

def cizgileri_ciz(ekran, cizgiler):
    for cizgi in cizgiler:
        body = cizgi.body
        pv1 = body.position + cizgi.a.rotated(body.angle)
        pv2 = body.position + cizgi.b.rotated(body.angle)
        p1 = to_pygame(pv1)
        p2 = to_pygame(pv2)
        pygame.draw.line(ekran, THECOLORS["red"], p1, p2)

pygame.init()
ekran = pygame.display.set_mode((600, 600))
saat = pygame.time.Clock()

space = pymunk.Space()
space.gravity = (0.0, -900.0)

cizgiler = cizgi_ekle(space)
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
            pass

    ekran.fill(THECOLORS["white"])
    for top in toplar:
        if top.body.position.y < 0: toplar.remove(top)
        top_cizdir(ekran, top)
    cizgileri_ciz(ekran, cizgiler)

    space.step(1/60.0)
    pygame.display.flip()
    saat.tick(60)
```

Ne yazık ki pymunk hakkında pek fazla belge yok ve Kaynak altında verdiğim linkler ne kadar yeterli olur bilemiyorum, ama İngilizcesi yeterli olanlar sınıf isimlerinden vs. öğrenmekte zorluk çekmeye bilirler. Bende bu yazıyı yazarken bu modülü öğrendiğim için sorularınıza cevap veremezsem kusuruma bakmayın...

Kaynak: [http://www.pymunk.org/en/latest/pymunk.html](http://www.pymunk.org/en/latest/pymunk.html)

