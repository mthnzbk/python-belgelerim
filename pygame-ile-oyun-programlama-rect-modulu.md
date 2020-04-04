# Pygame ile Oyun Programlama - rect Modülü

Her Surface nesnesi boyutuyla ve çizildiği konumla bir dikdörtgen alanı kaplar. Bu dikdörtgen alanlar Rect\(\) modülüyle ifade edilir. Rect ile [Surface](pygame-ile-oyun-programlama-surface-modulu.md) modülünü anlatırken karşılaşmıştık. Surface modülünün get\_rect\(\) methoduyla bir Rect nesnesi döndürmüştük. İşte bu rect bilgisi ile ekrana çizdirdiğimiz Surfacelerin hareketlerini yönetebiliriz.

Rect modülünün bir çok methodu vardır. Yalnız hiç kullanmadığımdan ve bilmediğimden sadece bir kısmını göreceğiz ve zaten anlatacağım kısımda en çok kullanılan methodlardır. Bu yazıyı okuyup bitirdiğinizde ekranda bir Surface i nasıl hareket ettireceğimizi ve çarpışma kontrolünü öğrenmiş olacaksınız.

Şimdi ekrana iki adet Surface çizdirelim ve Rect modülünü öğrenmeye başlayalım…

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((500,500))

yesil_kare = pygame.Surface((100,100))
yesil_kare.fill((0,255,0))
yesil_kare_rect = pygame.Rect((100,100), yesil_kare.get_size())

mavi_kare = pygame.Surface((100,100))
mavi_kare.fill((0,0,255))
mavi_kare_rect = yesil_kare_rect.copy()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_w:
                yesil_kare_rect = yesil_kare_rect.move(0,-5)
            if event.key == pygame.K_s:
                yesil_kare_rect = yesil_kare_rect.move(0,5)
            if event.key == pygame.K_a:
                yesil_kare_rect = yesil_kare_rect.move(-5,0)
            if event.key == pygame.K_d:
                yesil_kare_rect = yesil_kare_rect.move(5,0)

    pencere.fill((0,0,0))

    pencere.blit(yesil_kare, yesil_kare_rect)
    pencere.blit(mavi_kare, mavi_kare_rect)
    pygame.display.flip()
```

yesil\_kare adında ve mavi\_kare adında iki tane Surface nesnesi oluşturduk. Boyutlarını 100×100 pixel olarak atadık. fill\(\) methodlarıyla da sırasıyla Surface leri yeşil ve maviye boyadık. yesil\_kare\_rect değişkenine pygame.Rect modülüyle dikdörtgen bir alan meydana getirdik. Bu dikdörtgen alan 100×100 konumunu ve yesil\_kare Surface inin boyutunu içeriyor. mavi\_kare\_rect değişkenine ise Rect modülünün copy\(\) methodunu kullanarak yesil\_kare\_rect in kopyasını atıyoruz.

Oyun döngümüzde olayları for döngüsüyle sıralıyoruz ve if bloklarıyla w, a, s, d tuşlarını yakalıyoruz. Bu tuşlara basıldığı an işlem yapılması için KEYDOWN tipinde olayları yakalayan bir kod yazdık. Bu if kontrolünde ise Rect modülünün move\(\) methoduyla karşılaşıyoruz. move\(\) methodu aldığı parametrelerle x ve y konumlarının değerini değiştirmemizi sağlıyor. Ancak, bu işlem tanımladığımız nesneye etki etmediğinden biz move\(\) methoduyla dönen rect nesnesini tekrar ana nesnemize atıyoruz.

Bu olay kontrolünde W tuşuna bastığımızda rect nesnesinin y niteliği -5 artacaktır. Yani y değeri 100 ise yeni değeri 95 olacaktır. S tuşuna basıldığında ise y niteliği +5 artacaktır. y niteliğinin değeri 100 ise yeni değeri 105 olacaktır. A tuşuna basıldığında bu sefer x niteliği -5 artacak ve x in değeri 100 ise 95 olacaktır. D tuşuna bastığımızda ise x niteliğinin değeri +5 artacak ve önceki değeri 100 ise 105 olacaktır.

Olay kontrolünden sonra yesil\_kare ve mavi\_kare surfacelerini pencere.blit\(\) ile ekrana çizdiriyoruz. Konumlarını girdiğimiz parametreye ise bu nesnelerin rect nesnelerini giriyoruz. Bu şekilde yesil\_kare\_rect ile mavi\_kare\_rect in değerleri değişirse ekrana yeni değerine göre çizim yapılacaktır. Bu kodu çalıştırdığımızda W, A, S, D tuşlarına basarak yeşil renkteki Surface i oynatabiliriz. Yalnız yesil\_kare\_rect in kopyasını mavi\_kare\_rect değişkenine atadığımızdan ve mavi\_kare değişkenini yesil\_kare den sonra yazdığımızdan dolayı yeşil renkli Surface ilk başta görünmeyecektir. Klavye tuşlarına basıp move\(\) methoduyla taşıma yaptığınız zaman görünecektir.

Döngüde kullandığımız pencere.fill\(\(0,0,0\)\) kodunu kullanmadan nasıl sonuç aldığınızı gözlemleyiniz.

Buraya kadar Rect modülünün copy\(\) ile move\(\) methodunu öğrendik. Bundan sonra oyunlarda vazgeçilmez olan nesnelerin çarpışma kontrolünü yapmamızı sağlayacak methodları öğrenmeye başlayabiliriz.

Nesnelerin çarpışması, bir nesnenin diğer nesneye temas etmesiyle oluşur. Bir nesnenin diğer nesneye temas edip etmediğini colliderect\(\) methoduyla öğreniriz. İlk örneğimizden ilerleyerek hareket ettirdiğimiz yeşil kutumuzu mavi kutuya temas ettirdiğimizde mavi kutuyu rasgele bir alana kaçırtalım.

```text
import pygame, sys, random

pygame.init()

pencere = pygame.display.set_mode((500,500))

yesil_kare = pygame.Surface((100,100))
yesil_kare.fill((0,255,0))
yesil_kare_rect = pygame.Rect((100,100), yesil_kare.get_size())

mavi_kare = pygame.Surface((100,100))
mavi_kare.fill((0,0,255))
mavi_kare_rect = yesil_kare_rect.copy()

pygame.key.set_repeat(100,10)
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_w:
                yesil_kare_rect = yesil_kare_rect.move(0,-2)
            if event.key == pygame.K_s:
                yesil_kare_rect = yesil_kare_rect.move(0,2)
            if event.key == pygame.K_a:
                yesil_kare_rect = yesil_kare_rect.move(-2,0)
            if event.key == pygame.K_d:
                yesil_kare_rect = yesil_kare_rect.move(2,0)

    if mavi_kare_rect.colliderect(yesil_kare_rect):
        mavi_kare_rect.x, mavi_kare_rect.y = (random.randrange(400), random.randrange(400))

    pencere.fill((0,0,0))

    pencere.blit(yesil_kare, yesil_kare_rect)
    pencere.blit(mavi_kare, mavi_kare_rect)
    pygame.display.flip()
```

Bir önceki kodumuza 3 satır kod ekleyerek çarpışma olması durumunda mavi renkli karenin konumunu değiştirmesini sağladık. while döngüsünden hemen önce kullandığımız pygame.key.set\_repeat\(\) fonksiyonu ile klavye tuşuna basılı tuttukça girdiğimiz parametrelere göre saniye de kaç kere tetikleneceğini belirttik. İlk örneği incelediyseniz basılı tutsanızda sadece bir kere yeşil karenin oynadığını görmüşsünüzdür. Bu fonksiyon bizi bundan kurtarıyor. Lakin W ve A tuşlarına basmak gibi çapraz hareket ettiremiyoruz. Çünkü KEYDOWN olayı bir defa da bir tuşa işlem yapıyor. Bir tuşa basılı tutarken diğerine tıklayınca daha iyi anlarsınız. Bu gibi bazı can sıkıcı durumlarda klavye kontrolünü pygame.key modülü ile çözmek gerekebilir.

Olay kontrolünden sonraki ana döngümüzün başına bir if bloku açtık ve mavi\_kare\_rect adlı Rect nesnemizin colliderect\(\) methoduyla yesil\_kare\_rect adındaki Rect nesnesinin kontrolünü her döngüde kontrol edilmesini sağladık. Eğer bu iki nesne bir birine temas ediyorsa colliderect\(\) methodu True döndüreceği için altındaki kod işlenecektir. Yani mavi\_kare\_rect in x ve y koordinatları 0-400 arası bir değere atanacak ve sonraki satırlarda ekrana yeni konumuyla çizilmesini sağlayacaktır. Bu satırda bize rasgele sayı üretme işini yapan random modülü kullandık. Bu modülün randrange\(\) methodu girdiğimiz değeri aşmayacak şekilde rasgele sayı döndürür. Tabii sadece bu şekilde bir kullanımı yok, ama biz burada çarpışma olayını öğrendiğimiz için diğer kullanımları başka bahara kalıyor…

Dikdörtgen alanların çarpışma olayını colliderect\(\) methoduyla kontrol ettik. Örnek kodumuzda iki nesnenin çarpışmasıyla mavi renkli karenin yerini oynattık. Peki, bunu fare imleciyle yapalım mı? collidepoint\(\) methodu ise girdiğimiz konum bilgisine göre temas var mı onu bildirir. Büyük ihtimal bu method ile amaç fare imlecinin nesne ile temasının kontrolü için eklenmiş olmalı.

Şimdi yeşil renkli kutumuzdan geçici olarak kurtulup mavi kutuyu fare imleciyle temas olduğunda konumunu değiştirelim.

```text
import pygame, sys, random

pygame.init()

pencere = pygame.display.set_mode((500,500))

mavi_kare = pygame.Surface((100,100))
mavi_kare.fill((0,0,255))
mavi_kare_rect = pygame.Rect((100,100), mavi_kare.get_size())

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    if mavi_kare_rect.collidepoint(pygame.mouse.get_pos()):
        mavi_kare_rect.x, mavi_kare_rect.y = (random.randrange(400), random.randrange(400))

    pencere.fill((0,0,0))

    pencere.blit(mavi_kare, mavi_kare_rect)
    pygame.display.flip()
```

Çok daha kısa bir kodla amacımıza ulaştık. Dikkat edeceğim nokta şurasıdır.

```text
if mavi_kare_rect.collidepoint(pygame.mouse.get_pos()):
```

Bura da mavi\_kare\_rect in collidepoint\(\) methodunu kullandık ve parametre olarak pygame.mouse modülünün get\_pos\(\) fonksiyonunu kullandık. get\_pos\(\) fonksiyonu bize o an ki fare imlecinin konumunu verir. Bu kontrolü de döngü ile tekrarladığımız için fare imleci ekrandaki mavi renkli kutuya temas ettiği andan rasgele bir konumda belirecektir. Pygame in resmi [dökümanlarını](http://www.pygame.org/docs/) incelediğinizde fark edeceksinizki Rect modülünün başka methodlarıda var. Ben burada en çok kullanılan methodları anlatacağımı söylemiştim. O yüzden ileriki derslerde lazım olursa diğer methodlara da değinebilirim…

