# Pygame ile Oyun Programlama - mixer Modülü

Bir önceki derste pygame.mixer modülü altında bulunan music modülünü öğrendik. music modülü ile oyun içi müzik oynatmamızı sağlayacak bilgiler edindik. Özellikle dedik ki, music modülü ile efektleri istediğimiz gibi oynatamayız, bunun için mixer modülünün sağladığı özellikleri kullanmalıyız…

mixer modülünde bu efektlerle çalışmak için iki adet sınıfımız var. Bunları ve diğer fonksiyonları öğrenmek için basit bir oyun yapalım…

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

ses = pygame.mixer.Sound("shotgun3.wav")
ses.play()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pygame.display.flip()
```

Örneğimizde ses adındaki değişkene Sound nesnesi atadık ve bu ses nesnesi shotgun3.wav dosyasını barındırsın dedik. Hemen ardından Sound sınıfının play\(\) methoduyla ses dosyasını oynattık. Örneği çalıştırdığınızda silah sesi bir defa duyulacaktır.

play\(\) methodu ile ses dosyasını oynatmakla beraber bu method bir Channel nesnesi döndürür. Channel nesnesi oynatılacak ses için bir kanal oluşturur. pygame de en fazla 9 kanal oluşturabilirsiniz bu kanallar 0-8 arası sıralanır. music modülünde ki play\(\) fonksiyonu gibi bu methodda ilk parametresine gireceğiniz rakama göre kaç kez oynatılacağına karar verebilirsiniz.

Oynatılan ses dosyası uzunsa ve durdurmak isterseniz stop\(\) methodu ile bu işi halledebilirsiniz. music modülünde anlattığım set\_volume\(\) ve get\_volume\(\) fonksiyonları da Sound sınıfında aynı şekilde çalışmaktadır. music modülünde olmayan şey ise get\_length\(\) methodudur. Bu method ile ses dosyanızın uzunluğunu öğrenebilirsiniz.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

ses = pygame.mixer.Sound("shotgun3.wav")
ses.play()
print ses.get_length()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pygame.display.flip()
```

Benim oynattığım ses dosyası 2 saniyeydi.

Sound sınıfında değinmek istediğim son method ise get\_num\_channels\(\) methodu. Bu method bağlı olduğu Sound nesnesinin kaç kanalı kullandığını sayı olarak döndürür. Normal olarak yukarıdaki örneğe print ile bu methodun döndürdüğü değeri yazdırırsak 1 sonucunu alırız. Ses oynatıldıktan sonra işletilen her play\(\) methodu bu methodun çıktısını sabit kılacaktır. Eğer siz bir ses dosyasının oynatılması bitmeden üç kere daha play\(\) methodunu çalıştırırsanız aynı sesten dört kez oynatılacaktır ve bu koddan sonra print ile get\_num\_channels\(\) methodunun döndürdüğü değeri yazdırırsanız 4 rakamını çıktıda görürsünüz.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

ses = pygame.mixer.Sound("shotgun3.wav")
ses.play()
ses.play()
ses.play()
ses.play()
print ses.get_num_channels()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pygame.display.flip()
```

Dört adet kanal kullanılacak ve sesler örneğe göre aynı anda oynatılacaktır. Bu yüzden tek bir ses oynatılmış gibi gelecek, ama ses seviyesi artmış sanacaksınız.

Yukarıda dediğim gibi en fazla dokuz kanal kullanılabiliyor. Eğer dokuz kanaldan fazlasını kullanmaya kalkarsanız fazla sesler oynatılmayacaktır.

play\(\) methodunun Channel nesnesi döndürdüğünü söylemiştik. İstersek Channel nesnelerini kendimizde tanımlayabiliriz.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

ses = pygame.mixer.Sound("shotgun3.wav")
kanal = pygame.mixer.Channel(0)
kanal.play(ses)

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pygame.display.flip()
```

Örnekte Sound nesnesini muhafaza ettik ve kanal adında bir Channel nesnesi oluşturduk. Bu kanalın id sini 0 olarak belirttik ve Channel nesnesinin play\(\) methoduna Sound nesnesini parametre olarak verdik. Kodu çalıştırdığımızda bir kez shotgun3.wav ses dosyası oynatılacaktır. Her kanal tek ses dosyası oynatabildiği için birden fazla oynatmalarda önceki sesler kesilecektir.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

ses = pygame.mixer.Sound("shotgun3.wav")
kanal = pygame.mixer.Channel(0)
kanal.play(ses)

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                kanal.play(ses)

    pencere.fill((0,0,0))

    pygame.display.flip()
```

Bu kodu çalıştırıp boşluk tuşuna sürekli bastığınızda önceki seslerin kesildiğini en son bastığınızdaki sesin çaldığını göreceksiniz.

Channel sınıfının music modülünde de olan; stop\(\), set\_volume\(\), get\_volume\(\), pause\(\), unpause\(\) methodları mevcuttur. Tekrar anlatmama lüzum görmüyorum. Çünkü aynı şekilde çalışıyorlar. Yalnız set\_volume\(\) methoduna isterseniz iki ayrı ses değeri vererek sağ ve sol hoparlörün ayrı ayrı ses seviyesinde olmasını sağlayabilirsiniz.

Şimdi öğrendiklerimizle basit bir hedef vurma oyunu yapmaya başlayalım. Nişangah ve hedef resimlerini seçelim. Malesef bu oyunda en büyük musluk tamircisi Mario karakterini vuracağız :\)

Yapacağımız oyun da ekranda rasgele belirecek bir hedef olacak ve onu vurduğumuzda hedef rasgele bir yerde tekrar belirecek. Her ateş ettiğimizde silah sesi duyacağız ve ard arda ateş ettiğimizde önceki seslerde duyulacak. Hedefi ise nişangah resmini fare imleciyle paralel hareket ettireceğiz ve imleci görünmez yapacağız. Yani nişangahın merkez noktası aslında fare imlecinin olduğu yer olacak. Bizde fare imleci konumuyla hedefin çarpışma kontrolünü yapıp çarpışma varsa ateş ettiğimizde vurmuş olacağız. 20 kere başarılı atıştan sonra oyunumuz bitecek.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((640,480))

nisan = pygame.image.load("nisan.png")
nisan_rect = pygame.Rect((0, 0), nisan.get_size())

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    nisan_rect.center = pygame.mouse.get_pos()
    pencere.blit(nisan, nisan_rect)
    pygame.display.flip()
```

Öncelikle 640×480 boyutlarında pencere oluşturduk. nişan.png resmimizi yükledik ve Rect nesnesini oluşturduk. Ana döngümüzde nisan\_rect adındaki Rect nesnemizin center-merkez koordinatını pygame.mouse.get\_pos\(\) ile fare imlecinin koordinatı olarak ayarladık. pencere.blit\(\) ile de güncellenen Rect nesnemizin içeriğine göre ekrana fare imlecinin hareketiyle hareket eden bir nişangah çizdirdik. Uygulamayı çalıştırdığımızda fare imlecinin nişangahın merkezinde olduğu görürüz. Daha güzel olması için o fare imlecini görünmez yapalım ve fare sol tuşuyla her basışımızda ateş sesi gelsin.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((640,480))

ses = pygame.mixer.Sound("shotgun3.wav")

nisan = pygame.image.load("nisan.png")
nisan_rect = pygame.Rect((0, 0), nisan.get_size())

pygame.mouse.set_visible(False)
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            if event.button == 1:
                ses.play()

    pencere.fill((0,0,0))

    nisan_rect.center = pygame.mouse.get_pos()
    pencere.blit(nisan, nisan_rect)
    pygame.display.flip()
```

Sound nesnemizi oluşturduk ve shotgun3.wav ses dosyasını kullanmasını söyledik. Fare imlecini görünmez yapmak için pygame.mouse.set\_visible\(\) fonksiyonuna False parametresini girdik. Olay kontrolüne MOUSEBUTTONDOWN ile fare tuşuna basıldığı an dedik; event.button == 1 ile de sol tuşa basılmışsa ses dosyamızı oynatmasını söyledik. Kodu çalıştırdığımızda her sol fare tıklamasında ateş etme sesi gelecektir.

Şimdi ise hedefimizi ekrana rasgele çizdirelim ve çarpışma algılaması ile ateş etme durumunda hedefi başka yere taşıyalım. Ekranın üst-orta kısmına ise kaç kez vurduğumuzu yazdıralım. 20 kez vurduğumuzda oyun bitiş ekranı karşılaşın.

```text
import pygame, sys, random

pygame.init()

pencere = pygame.display.set_mode((640,480))

ses = pygame.mixer.Sound("shotgun3.wav")

nisan = pygame.image.load("nisan.png")
nisan_rect = pygame.Rect((0, 0), nisan.get_size())

hedef = pygame.image.load("mario.png")
hedef_rect = pygame.Rect((0, 0), hedef.get_size())

font = pygame.font.Font("GentiumPlus.ttf",50)

pygame.mouse.set_visible(False)
hedefte_mi = False
sayac = 0
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            if event.button == 1:
                if hedefte_mi:
                    sayac += 1
                    hedef_rect.x, hedef_rect.y = random.randrange(600), random.randrange(400)
                ses.play()

    if sayac < 20:
        sayac_yaz = font.render("%.2d"%sayac,True,(255,255,0))
        if hedef_rect.collidepoint(pygame.mouse.get_pos()):
            hedefte_mi = True
        else:
            hedefte_mi = False

        pencere.fill((0,0,0))

        nisan_rect.center = pygame.mouse.get_pos()
        pencere.blit(hedef, hedef_rect)
        pencere.blit(sayac_yaz, (300,0))
        pencere.blit(nisan, nisan_rect)
    else:
        pencere.fill((0,0,0))
        oyun_bitti = font.render("OYUN BİTTİ. KAZANDINIZ!",True,(0,255,0))
        pencere.blit(oyun_bitti, (50,200))

    pygame.display.flip()
```

hedef ismiyle mario.png yi tanımladık. hedef\_rect ile ilk rect değerini girdik. font değişkenine de Font nesnemizi tanımladık. Kaç kez vurduğumuzu hesaplamak için sayac adlı değişkeni, fare pozisyonu ile hedef çarpışıyor mu diye hedefte\_mi değişkenini tanımladık. Olay kontolümüzde fare sol tuşuna bastığımızda hedefte\_mi değişkeninin değerini kontrol ediyoruz. Eğer True ise sayacımız +1 yükseliyor. ve hedef\_rect in x ve y koordinatlarını rasgele bir değere atıyoruz. Yani hedef vurulduğu zaman sayaç yükselecek ve hedef konum değiştirecektir. Oyun döngümüzde ise bir if bloku açtık. Oyunumuz sayaç 20 değerini alınca biteceği için sayac değişkeninin değeri 20 den küçük olduğu sürece alttaki işlemleri yapsın dedik.

if blokunda sayac\_yaz değişkenine sayac değişkenin değerini kullanarak sarı renkli olacak şekilde Surface nesnesi oluşturduk. Her döngüde bu Surface nesnesi yeniden oluşacağı için sayac değişkenindeki değişiklikleri de güncellemiş oluyoruz. Hemen altına hedef ile fare imlecinin pozisyonunun çarpışma kontrolünü yaptık ve çarpışma varsa hedefte\_mi değişkeninin değerini True yaptık. Aksi durumlarda False olsun dedik. ana if blokunun geriye kalan kısmında ise değişikliklere göre nişangahı ve hedefi sürekli çizdiren kodları yazdık. Sayacımızı ise yukaro-orta kısma yazdırdık.

else blokunda ise sayac değişkeninin değeri 19 dan yüksek olduğunda ise ekranı temizledik ve ortasına OYUN BİTTİ. KAZANDINIZ! yazısını yazdırdık. Bu halde sol fare tıklamasıyla ateş etme sesini hala oynatabilirsiniz. if blokundan çıktık ve ekranı güncelleyen flip\(\) fonkyonumuzu yazdık.

