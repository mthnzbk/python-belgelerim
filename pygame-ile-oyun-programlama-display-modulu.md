# Pygame ile Oyun Programlama - display Modülü

Display modülü pencere oluşturmayı ve onu kontrol etmemizi sağlayan methodlar sunar. Bu yazı da display modülünün işimize yaran methodlarını öğreneceğiz.

**Pencere Oluşturma**

Pygame de bir pencere oluşturmak için display modülünün set\_mode\(\) methodunu kullanırız. Bu set\_mode\(\) methodu üç adet parametre kabul eder, ama ilk iki parametresi işimizi görmemize yeterlidir.

```text
import pygame
import sys

pygame.init()

pencere = pygame.display.set_mode((200,200), pygame.SWSURFACE)

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    pygame.display.flip()
```

Bu örneğimizde 200×200 pixellik bir pencere oluşturmuş oluyoruz. Bunu sağlayan set\_mode\(\) methodunun ilk parametresidir. Bu parametre, pencere boyutunu belirten iki elemanlı liste kabul eder. İkinci parametremiz penceremiz için bayrak atamamıza izin verir. Örnek kodumuzdaki SWSURFACE değişkeni pygame in hafıza olarak video hafızasını kullanmasını söyler.

Pygame de bazı modülleri kullanabilmek için o modülleri başlatmamız gerekir. Bu işi pygame.init\(\) kodu gerçekleştirir. Bu kodu yazmaz isek yukarıdaki kodu örnek alarak display modülünü başlatmamız gerekirdi. Bu durumda pygame.init\(\) yerine pygame.display.init\(\) kodunu yazmamız gerekir. init\(\) fonksiyonu kullanılmaz ise kod hata verecektir.

pygame.display.quit\(\) ise display modülü kapatır.

Bayrak olarak kullanabileceğiniz diğer değişkenler şunlardır:

```text
pygame.FULLSCREEN #ekranı tamamen kaplar.
pygame.HWSURFACE #Donanım hızlandırması yapar. FULLSCREEN ile kullanılması gerekiyor.
pygame.RESIZABLE #pencere boyutunu değiştirebilmenizi sağlıyor.
pygame.NOFRAME #pencere kenarlıklarını kaldırır.
pygame.DOUBLEBUF #Ne işe yaradığından emin değilim, ama HWSURFACE veya OPENGL ile kullanmanız öneriliyormuş.
pygame.OPENGL #OpenGL renderli görüntü oluşturmanızı sağlar.
pygame.SWSURFACE #Sistem belleği yerine video hafızasını kullanır.
```

**Pencere Yüzeyi**

set\_mode\(\) methodu pencere oluşturmakla beraber bir Surface\(yüzey\) nesnesi döndürür. Bu dönen nesnenin örneğini alabileceğimiz gibi get\_surface\(\) methodunu da kullanabiliriz.

```text
pencere = pygame.display.set_mode((200,200), pygame.SWSURFACE)
print(pygame.display.get_surface(), pencere)
```

pencere adlı örneğimizin çıktısıyla pygame.display.get\_surface\(\) in çıktısı aynı olacaktır. Bu Surface nesnesi ekrana çizdireceğimiz şekillerin çizileceği yüzeyi temsil eder. İleri ki yazılarımda Surface modülünüde öğrendiğimiz zaman daha iyi kavrayacaksınız. Bu modül pygame in yapı taşıdır desem yalan söylemiş olmam herhalde.

**Ekran Modları**

Oyun oynayanlardansanız mutlaka çözünürlük ayarı yapmışsınızdır. Ör: 800×600. Bu ayarı yaparken çeşitli çözünürlük oranlarıyla karşılaşırız. Eğer sizde çözünürlük seçeneğine göre ekranınızı çizdirmek istiyorsanız list\_modes\(\) methodunu kullanabilirsiniz. Bu methodu print ile yazdırdığımızda şöyle bir çıktı alırız:

```text
[(1440, 900),
(1280, 800),
(1152, 864),
(1024, 768),
(832, 624),
(800, 600),
(700, 525),
(640, 480),
(512, 384),
(400, 300),
(320, 240)]
```

Bende ki çıktı böyle. Bu çıktıya bakarak ben yazdığım oyunda en düşük ekran boyutunu 320×240 en yüksek ekran boyutunu 1440×900 olarak sunabilirim. Tabii bunu kullanacaksak ona göre kodlarımızı yazmalıyız. Eğer 200×200 pixellik bir oyun ekranında örnek bir haritayı 200×200 olarak çizdiriyorsak pencere boyutunu yükselttiğimizde sağ ve alt taraflar boşlukta kalacaktır.

**Simge Durumuna Küçültmek**

Pygame penceresini full ekran yaptığınızda ve her hangi bir klavye tuşuna pencereyi kapatma olayını atamadığınızda can sıkıcı bir durumla karşılaşabilirsiniz. Windowstaki deneyimime göre pencere full ekrandayken Alt+Tab tuşuyla başka pencereye geçebilirken, GNU/Linuxta hiç bir şekilde full ekrandan çıkamıyorsunuz. Tek çareniz ctrl+alt+f1-f7 kısayolarından birine basıp konsola düşmek ve bilginiz yeterliyse çalışan pencereyi öldürmek. Aksi halde blgisayar kasanızdaki küçük düğmeye basarak bilgisayarı yeniden başlatmanız gerekir. Eğer bunu tecrübe ettiğinizde kaydedilmemiş verileriniz varsa vay halinize.

Ayrıca ekranı kapatmak için gerekli klavye olayını atamış olsanızda oyun esnasında mola verip başka bir pencereye geçme durumunu göze almalısınız. iconify\(\) methodu bu gibi durumlarda imdadımıza yetişiyor.

```text
import pygame
import sys

pygame.init()

pencere = pygame.display.set_mode((200,200), pygame.FULLSCREEN)

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LALT:
                pygame.display.iconify()

    pygame.display.flip()
```

Olay modülü olan event’ı henüz görmedik, ama kısaca anlatacak olursak sol ALT tuşuna bastığımızda iconfy\(\) methodumuzu tetiklemiş oluyoruz. Pygame penceresine atadığımız FULLSCREEN bayrağı penceremizi full ekran yapar. Biz, sol ALT tuşuna bastığımızda pygame penceresi simge durumuna küçülecektir.

**Pencere Başlığı**

Verdiğim örnek kodları çalıştırdığınızda pencere başlığının “pygame window” olduğunu göreceksiniz. Eğer istersek pencere başlığımızı değiştirebiliriz.

```text
import pygame
import sys

pygame.init()

pencere = pygame.display.set_mode((200,200), pygame.SWSURFACE)
pygame.display.set_caption("Oyunumuzun Adı")

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    pygame.display.flip()
```

Çok basit bir şekilde set\_caption\(\) methoduyla Pencere başlığımızı değiştirmiş oluyoruz. Bu methodu döngü içinde kullanarak sürekli değişen bir başlıkta yapabiliriz… Bu sizin hayal gücünüze kalıyor.

pygame.display.get\_caption\(\) ile de pencere başlığını alabilirsiniz. Başlık atarken kullandığımız “u” string değerini unicode yapar. Eğer Türkçe karakter kullanacaksanız bu gerekli. Python’un 3x versiyonlarında unicode desteği sağlandığı için u”" gibi bir yazım biçimi ortadan kalktı.

**Full Ekran Yapma ve Full Ekrandan Çıkma**

Pygame penceremizi bir klavye tuşuna basarak full ekran yapmak ve tekrar eski haline getirmek isteyebiliriz. toggle\_fullscreen\(\) methodu bu isteğimizi gerçekleştirecektir.

```text
import pygame
import sys

pygame.init()

pencere = pygame.display.set_mode((200,200), pygame.SWSURFACE)

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LALT and event.key == pygame.K_RETURN:
                pygame.display.toggle_fullscreen()

    pygame.display.flip()
```

Burada ALT ve ENTER tuşlarına birlikte basıldığında penceremizi full ekran yapmasını sağlıyoruz. K\_RETURN klavyemizdeki büyük enter tuşunu ifade etmektedir. Numpad de mevcut olan enter ise K\_KP\_ENTER olarak geçmektedir.

Full ekran yaptığımız da aynı tuşlara tekrar bastığımızda ise toggle\_fullscreen\(\) methodu pencereyi eski durumuna döndürecektir. Eğer penceremizi FULLSCREEN bayrağıyla başlatırsak tuşlara ilk basışımızda full ekrandan çıkar ve belirttiğimiz boyutta bir pencere halini alır.

**Pencere İkonu**

```text
pygame.display.set_icon(pygame.image.load(“logo.png”))
```

Sıradan bir pygame penceresinde sol üst kısımda bulunan ikon ağzında joystick olan sarı bir yılan resmidir. Bu resmi de başlık gibi değiştirebiliriz. Aşağıdaki koda bir bakalım.

```text
import pygame
import sys

pygame.init()

pencere = pygame.display.set_mode((200,200), pygame.SWSURFACE)
pygame.display.set_icon(pygame.image.load("resim.png"))

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pygame.display.flip()
```

pygame.image.load\(\) resim dosyasını yükler ve onu Surface nesnesine çevirir. set\_icon\(\) methodu da parametre olarak Surface nesnesi bekler. Bu şekildeki bir kod ile resim.png resmini pygame penceresinin sol-üst tarafında ikon olarak görürüz.

**Ekranı Güncellemek**

İlk verdiğimiz örnekte döngü içerisinde şu kodu yazmıştık:

```text
pygame.display.flip()
```

Pygame de ekrana bir Surface nesnesi çizdirdiğimizde bu çizimi görebilmek için ekranın güncellenmesi gerekir. Bu method bu görevi üstlenir. Ekrandaki her değişikliği görebilmek için while döngüsüyle sonsuz bir döngü oluşturmalıyız.

Görsel programlama da çoğu zaman biz fark etmesekte bir döngü mevcuttur. Pygame bu döngünün kontrolünü bize bırakır. Bu durum bizi sıkıntıya sokabilir…

flip\(\) methodu ile aynı işi yapan bir de update\(\) methodu vardır. Bu methodu flip\(\) methodundan ayıran şey ise parametre almasıdır. update\(\) methoduna parametre vermezseniz flip\(\) ile aynı işi görür, ama dikdörtgenlerden oluşan bir listeyi parametre olarak verirseniz bu dökdörtgenler güncellenir.

```text
pygame.display.update([(10,10,100,100),
                       (200,140,200,200)])
```

Bu şekilde verilen bir parametrede update\(\) methodu 10×10 konumundan başlayan 100×100 pixel boyutlarındaki dikdörtgen alanı günceller. Bu örnekte verdiğimiz dikdörtgen alanında Surface nesnesi mevcut olsaydı o nesnedeki değişiklikler güncellenmiş olacaktı.

