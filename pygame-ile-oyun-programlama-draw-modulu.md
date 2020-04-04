# Pygame ile Oyun Programlama - draw Modülü

Pygame draw modülüyle geometrik şekiller çizebiliriz. Bu modülle; dikdörtgen, daire, elips, çokgen, çizgi, ve yay-kavis çizebiliriz. Çizgi çizimlerinde ise artı olarak antialias çizgiler çizebiliriz. Antialias, şekillerin kenarlarındaki kırıklığı yumuşatarak daha iyi görünüm elde edilmesini sağlar. Diğer geometrik şekillerde bu özelliği kullanmak istiyorsanız pygame’in gfxdraw modülünü kullanmanız gerekecektir. Yalnız gfxdraw modülünde bir geometrik şeklin kenar kalınlığını ayarlıyamıyoruz. Bu modülü kullanacaksanız bunuda göz önüne almalısınız.

**Dikdörtgen Çizdirme**

Pygame de bir kare veya dikdörtgen çizdirmek istiyorsak draw modülünün rect\(\) methodunu kullanabiliriz. Rect, rectangel sözcüğünün kısaltmasıdır. Türkçe de dikdörtgen anlamındadır. rect\(\) methodu dört parametre alır; dikdörtgenin çizileceği yüzey nesnesi, rengi, konum ve boyutu, kalınlığı. Aşağıda ki örnek kodumuzla da pratik yapalım.

```text
import pygame, sys

pygame.init()
pencere = pygame.display.set_mode((300,300))

beyaz = (255,255,255)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.rect(pencere, beyaz, (50,50,100,150), 1)

    pygame.display.flip()
```

Yazdığımız kodla 300×300 pixellik bir pencere oluşturduk. Bu pencerede gerçekleşebilecek hareketler için bir döngü oluşturduk. Döngümüzde rect\(\) methodunu kullanarak ana yüzeye beyaz renkte, bir pixel kalınlığında kenarı olan bir dikdörtgen çizdirdik. Bu dikdörtgenin boyutunu 100×150 olarak ayarladık ve pozisyonunu 50×50 yaptık.

**Çokgen Çizdirme**

Polygon ya da çokgen, çok köşeli geometrik şekillere verilen addır. polygon\(\) methodumuz la istediğimiz sayıda kenarı olan bir şekil çizdirebiliriz. Ben bu örnekte ikizkenar üçgen çizdireceğim…

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

yesil = (0,255,0)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.polygon(pencere, yesil, [(100,100),(100,200),(200,200)], 1)
    pygame.display.flip()
```

Bir pencere oluşturduk ve ana döngümüzde üçgen çizdireceğimiz polygon\(\) methodunu yazdık. İlk parametremizde çokgeni hangi yüzeye çizdirileceğini söyledik. Çokgenimizin yeşil renkte ve son parametre olarak verdiğimiz değerin de kenar kalınlığı olarak 1 pixel olmasını istedik. Liste içinde verdiğimiz konum bilgileriyle bir konumdan diğerine son konumdan ilk konuma çizgi çizdirilmesini sağladık. Verdiğimiz konum bilgileriyle ikizkenar üçgen çizimi yapmış olduk.

Liste olarak verdiğimiz konum bilgilerini artırarak daha fazla kenarı olan şekiller çizdirebiliriz. Son parametre olan kenar kalınlığını 0\(sıfır\) verirsek şeklin içini girdiğimiz renkle doldurur. Kalınlığı çok artırırsak da birleşme noktalarında kırıklar meydana gelecektir.

**Daire Çizdirme**

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))


kirmizi = (255,0,0)
pozisyon = (150,150)
yaricap = 100
kalinlik = 1

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.circle(pencere, kirmizi, pozisyon, yaricap, kalinlik)

    pygame.display.flip()
```

Daire çizdirmek için örnek kodda da görüldüğü gibi circle\(\) methodunu kullanıyoruz. Daha açıklayıcı olsun diye parametrelere girilen değerleri değişkenlere atadım. Bir daire çizdirmek için circle\(\) methodu, bizden yarıçap bilgisi ister; yariçap değerini biz 100 olarak verdik. Bu durumda bu dairenin çapı 200 pixel uzunluğunda olacaktır. Pozisyon olarak verdiğimiz değer dairenin sol-üst köşesini işaret eder. Bu konum dairenin sol ucuyla üst ucunu, sol-üst tarafta birleştiği noktadır. Daha iyi anlamak için örnek uygulamamızda değişiklikler yapıp denemeniz iyi olur.

Kalınlık değerini yine 1 pixel olarak verdik. Eğer 0 verseydik dairenin içi kirmizi renkle doldurulacaktı. Kalınlığı fazla arttırdığımızda ise fark edeceksiniz ki çizgi arasında boş pixeller görünecektir. Bunu istemiyorsanız kalınlığı düşük tutmanız yerinde olur.

Yumuşatma olmadığından dairenin etrafını çeviren kenarında kırık pixeller görünür. Güzel görünüm elde etmek için pygame.gfxdraw modülünden yardım almalısınız…

**Elips Çizdirme**

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

mavi = (0,0,255)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.ellipse(pencere, mavi, (50,50,80,120), 1)

    pygame.display.flip()
```

Yukarıdaki örnekte 50×50 konumunda 80×120 boyutunda ve 1 pixel kalınlığında kenarı olan mavi bir elips şekli çizdirdik. ellipse\(\) methodu, rect\(\) methoduyla aynı parametreleri alır, ama ekrana çizilen elips bir şekildir.

Daire gibi elips şeklininde kenarında kırıklar oluşur. Hoş görünmeyen bu durumu pygame.gfxdraw modülü ile halledebiliriz

**Eğik-Kavis Çizdirme**

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

yesil = (0,255,0)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.arc(pencere, yesil, (10,10,100,100), 0, 4, 1)

    pygame.display.flip()
```

arc\(\) methodu elips şeklinde eğik-kavisli çizgiler çizmemizi sağlar. Tam bir elips yerine belirttiğimiz açılara göre eğik çizgi çizebiliriz. arc\(\) methodu altı parametre alır. İlk parametremiz kavisin çizileceği yüzeyi belirtir. İkinci parametre çizimin rengini, üçüncüsü ise rect yani dikdörtgen alanını belirtir. Methodumuz verilen konumdan dikdörtgen bir alana eğik çizgi çizer. Dördüncü parametremiz başlangıç açısını belirtir. Eğer 0 yazarsak matematik derslerinde öğrendiğimiz gibi elipsin sağ-orta noktasını başlangıç olarak alır. Beşinci parametre ise bitiş açısını belirtir. Buraya 6.30 değerini girersek tam bir elips çizer. Bitiş açısı en fazla 6.28-30 olmalıdır. Bu tam bir dönüşümün değeridir. Daha yüksek bir değer girilse de etkilemez. Son parametremizde çizimin kalınlığını belirtir.

**Düz Çizgi Çizdirme**

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

beyaz = (0,0,255)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.line(pencere, (0,255,0), (75,75), (150,150), 1)

    pygame.display.flip()
```

Düz çizgi çizmek için line\(\) methodunu kullanırız. Yukarıdaki örnek kodu çalıştırdığınızda; yeşil renkte, bir pixel kalınlığında, 75×75 konumundan 150×150 konumuna düz bir çizgi olduğunu görürsünüz.

**Çoklu Düz Çizgi Çizdirme**

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

beyaz = (0,0,255)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.lines(pencere, (66,99,200), True, [(100,100),(100,200),(200,100)], 1)

    pygame.display.flip()
```

lines\(\) methodu verdiğiniz konum noktaları listesini baz alarak çizgiler çizer. Eğer üçüncü parametreyi True verirseniz çizgiler en sona birleşmiyorsa ilk noktayla son nokta arasına bir çizgi çizerek kapatma yapar. False değeri verirseniz verdiğiniz listeye uyarak çizim işlemini yapar.

**Yumuşatılmış Düz Çizgi Çizdirme**

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

sari = (255,255,0)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.aaline(pencere, sari, (100,100), (120,150), 0)

    pygame.display.flip()
```

aaline\(\) methodu line\(\) methoduyla hemen hemen aynıdır. Farkı ise, anti aliasing yani yumuşatma yapar. Örneğin çapraz bir çizgi çizdirdiğinizde line\(\) fonksiyonu çizimi tırtıklı bir şekilde çizer ve göze hoş gelmez, ama aaline\(\) pürüzsüz bir çizgi çizer. Ayrıca en sonraki parametre çizginin kalınlığını belirtmez. Son parametre -blend- True ya da False değeri alır. Son parametre False ise çizgide oluşabilecek kırık pixeller; renk tonuna uygun, çizgiye göre şeffaflık içeren pixellerle kapatılır. True değeri verilirse şeffaf pixeller yerine aynı renk pixellerle kapatılır.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

mavi = (0,0,255)

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.draw.aalines(pencere, mavi, True, [(100,100),(100,250),(200,100)], 1)

    pygame.display.flip()
```

aalines\(\) methodu da lines\(\) methoduyla aynıdır. aaline\(\) methodun da olduğu gibi çizgilerde yumuşatma yapar. Son parametresi de -blend- çizginin kalınlığını değil çizimin güzelliğini belirtir. True değeri verilirse birbirleriyle birleşen çizgilerin kesiştiği noktalar düzgün görünür. False değeri verilirse kesişen noktalarda çıkıntılar oluşur. Deneyerek tam olarak nasıl olduğunu anlayabilirsiniz.

