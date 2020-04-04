# Pygame ile Oyun Programlama - event Modülü

Daha önceki derslerde anlatılmasa da örnek kodlarda Pygame'in event modülünü kullandık. Event, Türkçe olay anlamına gelir. Pygame penceresinde; bastığımız her klavye tuşu, fare hareketi ve tıklamaları, pencere boyutlandırma\(Pencere pygame.RESIZABLE bayrağıyla oluşturulmuşsa pencere boyutunu pencerenin kenarlarından değiştirebilirsiniz.\) vs. bir olaydır ve bir Event\(\) fonksiyonu oluşturur. pygame.event modülü bize bu olayları yönetmemiz için fonksiyonlar sunar.

Klavye ve/veya fare olaylarının birer Event\(\) fonksiyonu oluşturduğunu söylemiştik. Bu fonksiyonların kullanıma göre nitelikleri vardır. Her oluşturulan Event\(\) fonksiyonunun “type” adında bir niteliği mevcuttur. Bu nitelik olayın ne olduğunun bilgisini içerir. Bu type niteliği şu değerlerde olabilir:

QUIT – Pencerenin X butonuna basılmasıyla oluşur. ACTIVEEVENT – Pencereye odaklandığında ya da odaktan çıkıldığında oluşur. KEYDOWN – Klavyeden bir tuşa basıldığında oluşur. KEYUP – Basılan tuş bırakıldığı zaman oluşur. MOUSEMOTION – Fare imleci Pygame penceresinde hareket ettikçe oluşur. MOUSEBUTTONUP – Fare tuşlarından basılı olan bırakıldığı zaman oluşur. MOUSEBUTTONDOWN – Fare tuşlarından herhangi birine basıldığı zaman oluşur. VIDEORESIZE – Pencere boyutu değiştiği zaman oluşur. USEREVENT – Bu olay tipi kullanıcı tanımlı olaylarda kullanılır.

Tabii ki type niteliği bir string veri tutmaz. QUIT, KEYUP gibi değişkenler integer veri tutarlar. Bu değişkenlere pygame'i import ettikten sonra pygame.OLAYTIPI şeklinde erişebilirsiniz…

```text
from pygame.locals import *
```

Bu şekilde ki bir içe aktarma yöntemiyle pygamede ki bütün tanımlı değişkenlere direkt adıyla erişebilirsiniz.

Her Event\(\) fonksiyonunun birer “type” adında niteliği olduğunu söylemiştik. Her farklı olay tipinin kendine has nitelikleride vardır. Bunları da diğer fonksiyonlarla beraber öğreneceğiz.

Pygame de penceredeki güncellemeleri almak için while döngüsü kuruyoruz. Biz pencereye bakarken fark etmesekte ekran sürekli güncellenir. Pencereye odaklı iken bastığımız bir klave tuşu veya fare hareketi bir olay meydana getirecektir. Bu olay o anki döngü tamamlanıp yenisi başladığında listeye alınır ve o olayın kontrolünü yaptıysak olay kontrol noktasında yakalanır. Biz bu olay listesine get\(\) fonksiyonuyla ulaşırız. Bu method gerçekleşen olayların bir listesini döndürür.

```text
import pygame

pygame.init()

pencere = pygame.display.set_mode((300,300))

while True:
    print(pygame.event.get())
    pygame.display.flip()
```

Bu kodu çalıştırırsanız çok hızlı olarak konsol çıktısında olay listesi yazılır. Eğer gözleriniz yakalayabilirse konsola bakarken pygame penceresine fare ile tıklayın, klavyeden rasgele bir tuşa basın veya farenizi pencere üzerinde gezdirin. Akıp giden çıktılardan oluşan olayları görebilirsiniz. Yalnız bu durumda pencerenizi kapatmak biraz zorlayabilir sizi, uyarmadı demeyin…

Biz bu fonksiyonu genel olarak kullanıldığı şekliyle; for döngüsü ile kullanacağız ve olayları kontrol edeceğiz. Önceki derslerde mevcut olan pencere kapatma olayını kontrol eden kodumuza bir bakalım.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()

    pygame.display.flip()
```

Burada get\(\) fonksiyonu ile listesi alınan olayları for döngüsüyle tek tek sıralıyoruz. if blokuyla da diyoruz ki; “Eğer Event\(\) fonksiyonunun tipi, QUIT ile eşit ise sys.exit\(\) ile uygulamayı kapat.” Eğer biz pencerenin üstündeki X butonuna basarsak oluşacak olayın tipi QUIT ile eşit olacaktır ve kodumuz bu şekilde ise pencere kapanacaktır. Aksi halde X butonuna sürekli bassanız da pencere kapanmaz.

Bazı uygulamalarda dialog pencerelerine odaklıyken escape\(ESC\) tuşuna bastığımızda pencere kapanır. Böyle bir şeyi pygame de uygulamak istersek şöyle yaparız.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

while True:
    for i in pygame.event.get():
        if i.type == pygame.KEYDOWN:
            if i.key == pygame.K_ESCAPE:
                sys.exit()

    pygame.display.flip()
```

Bu kodda ise tipi KEYDOWN ile eşit olan olay varsa alttaki işlemleri gerçekleştir dedik. Gelen olayın tipi KEYDOWN ile eşit ise bu Event\(\) fonksiyonla “key” niteliğide gelir. key niteliğinin escape tuşunun yerini tutan K\_ESCAPE değişkenine eşit olması durumunda uygulamayı kapatmasını söyledik.

Event\(\) fonksiyonunun tipi KEYDOWN ise barındırdığı “key” niteliğiyle beraber “mod” ve “unicode” niteliklerini de barındırır. “mod” niteliğinin ne işe yaradığını hiç gerek duymadığımdan bilmediğim için değinmeyeceğim, ama “unicode” niteliğine ileriki derslerde değineceğim. Bu konuda önemli olan, olayları kavrayabilmemizdir.

Değinmek istediğim başka bir nokta ise şudur. Her Event\(\) fonksiyonu bir type niteliği barındırır ve biz bu type niteliğinin değerine göre olay kontrolünü yaparız. Mesela MOUSEBUTTONDOWN tipinde olan bir Event\(\) fonksiyonu “pos” ve “button” niteliklerinide barındırır, ama KEYDOWN tipinde olan bir Event\(\) fonksiyonu “key”, “unicode” ve “mod” niteliğini barındırır. Bu yüzden kodlarken önce olayın tipini kontrol ediyoruz ki diğer nitelikleri kullanırken emin olabilelim. Yoksa olmayan bir niteliği if ile kullandığımızda AttributeError hatası alırız.

Pencere modunda bir oyun yaptınız ve fare oku bu oyunu oynarken sürekli ekrandan dışarı çıkıyor. set\_grap\(\) fonksiyonuyla giriş ve çıkış aygıtlarını pencereye kitleyebiliriz. Bu fonksiyona True argümanını verirseniz fare oku ekrandan dışarı çıkamayacaktır.

```text
import pygame, sys

pygame.init()
pencere = pygame.display.set_mode((300,300))

pygame.event.set_grab(True)
while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()
        if i.type == pygame.KEYDOWN:
            if i.key == pygame.K_ESCAPE:
                sys.exit()

    pygame.display.flip()
```

Bu kod fare imlecini pygame penceresinden çıkmamasını sağlar. Bu yüzden fare imlecini X butonuna tıklatamayacağınız ya da başka bir şey yapamayacağınız için ESC tuşuna uygulamayı kapatma görevi verdik. Bu sayede uygulamayı kapatma zorluğu çekmeyeceğiz.

Olayları kontrol edebildiğimiz gibi kullanıcı tanımlı olaylar da oluşturabiliriz. Yukarıda USEREVENT değişkenini tanıtırken kullanıcı tanımlı olaylarda kullanıldığını söylemiştik. pygame.event modülünün Event\(\) fonksiyonunu kullanarak kendi olay fonksiyonumuzu oluşturabiliriz.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

can = 100
can_event = pygame.USEREVENT+1

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()
        if i.type == pygame.KEYDOWN:
            if i.key == pygame.K_UP:
                pygame.event.Event(can_event, can=-10)

    pygame.display.flip()
```

Bu örnekte can değişkeni oluşturup değerini 100 verdik. can\_event değişkenine ise kullanıcı tanımlı olay olan USEREVENT ın değerine bir artırıp yeni değeri atadık. USEREVENT 24 sayısını tutar ve 24 sayısından sonraki değerler USEREVENT olarak değerlendirilir.

Olay kontrolünde ise klavyeden yukarı ok tuşuna basıldığı zaman tanımladığımız Event\(\) fonksiyonu olayımız olacaktır. Bu olayın tipi can\_event a eşit olacaktır ve type niteliğiyle beraber can adında bir nitelikte gelecektir. Bu nitelik bizim kodumuzda -10 değerini taşıyacaktır.

Bu kod bu halde işe yaramaz. Biz altına bir if bloku ekleyip bu olayı yakalamaya çalışsak dahi yakalayamayız. Çünkü bu halde gelecek döngüdeki olay listesinde yer almayacaktır. Yer alması için post\(\) fonksiyonuyla olay listesine aktarmamız gereklidir. Kodumuzun çalışır hali şöyle olacaktır.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,300))

can = 100
can_event = pygame.USEREVENT+1

while True:
    for i in pygame.event.get():
        if i.type == pygame.QUIT:
            sys.exit()
        if i.type == pygame.KEYDOWN:
            if i.key == pygame.K_UP:
                event = pygame.event.Event(can_event, can=-10)
                pygame.event.post(event)
        if i.type == can_event:
            can = can + i.can
            print can

    pygame.display.flip()
```

Bu örnekte Event fonksiyonumuzu oluşturduktan sonra post\(\) fonksiyonuyla listeye aktarma yaptık. Eğer bu kodu çalıştırırsak; yukarı ok tuşuna basılınca can\_event olayı oluşacak ve bir sonraki dögü sırasında if bloku bu olayı yakalıyacaktır ve can değişkeniyle Event\(\) fonksiyonunun barındırdığı can niteliğindeki değeri toplayıp print ile yazdıracaktır. Örneğimizde can niteliği -10 değerinde olduğu için ilk yukarı ok tuşuna bastığımızda çıktımız 90 olacaktır. Ne kadar basarsak her seferinde can değişkeninin değeri -10 artacaktır.

pygame.event modülü bunlardan başka da fonksiyonlara sahiptir, ama biz sık kullanacağımız fonksiyonları öğrendik ve ilerideki derslerde ihtiyaç duyarsak bu fonksiyonlarıda o derslerde öğrenebiliriz. Bu ders belki çok açıklayıcı yada öğretici olmamış olabilir, ama bu derste event modülünün temelini kavramış olmalıyız. Gerekli modülleri öğrenipte uygulamalı derslere başladığımızda kavrayamadığımız noktalarıda kavramış olacağımıza inanıyorum. Bu derslerde gösterilenleri deneyerek ve üzerlerinde değişikler yaparak neyin nasıl olması gerektiğini öğrenmeye çalışırsanız bu bilgiler aklınıza kazınacaktır…

