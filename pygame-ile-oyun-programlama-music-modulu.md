# Pygame ile Oyun Programlama - music Modülü

Oyunların vazgeçilmezlerinden biri de ses efektleri ve oyun içi müziktir. Oyuna kendinizi kaptırmanızda arka planda çalan müzik, düşmana savurduğunuz kılıcın çarpma sesi büyük bir etkendir. Pygame de bize oyunumuzda efekt ve/veya müzik çaldırmamız için modüller sunar. Bu derste mixer modülünde bulunan music modülünü öğreneceğiz ve küçük ve kolay bir müzik çalar yapacağız.

Öncelikle kendinize bir mp3 ya da ogg müzik dosyası bulunuz. Ben TheStronges.mp3 adlı müzik dosyası ile çalışacağım. İlk örneğimizde bu dosyayı basit olarak oynatacağız…

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

muzik_adi = "TheStronges.mp3"

pygame.mixer.music.load(muzik_adi)
pygame.mixer.music.play()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

    pencere.fill((0,0,0))

    pygame.display.flip()
```

300×200 boyutlarında boş ve siyah bir ekran karşımıza çıkacak. music modülünün load\(\) methodu müzik dosyasını yükleyecek. play\(\) methodu ise müziği oynatacaktır. Bu method isteğe bağlı olarak iki adet parametre alır. İlk parametresine gireceğiniz pozitif bir sayıyla kaç kere çalması gerektiğini belirtebilirsiniz. İkinci parametresi ise müziğin kaçıncı saniyesinden başlatacağınızı float yani kayan noktalı bir değer girebilirsiniz. Ör: 10.5 değeri girerseniz 10.5 saniyeden itibaren oynatılır.

Şimdi ise uygulamamızı geliştirelim. Klavye tuşlarıyla müzik dosyasını oynatmayı, durdurmayı, bekletmeyi vs. kontrol edelim.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

muzik_adi = "TheStronges.mp3"
pygame.mixer.music.load(muzik_adi)

pause = False
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_f:
                pygame.mixer.music.play()
            if event.key == pygame.K_s:
                pygame.mixer.music.stop()
            if event.key == pygame.K_p:
                if pause:
                    pygame.mixer.music.unpause()
                    pause = False
                else:
                    pygame.mixer.music.pause()
                    pause = True
            if event.key == pygame.K_r:
                pygame.mixer.music.rewind()

    pencere.fill((0,0,0))

    pygame.display.flip()
```

Mükemmel olmasa da öğrenmek için ideal bir örnek olduğunu düşünüyorum. Örneğimizi çalıştırdığımızda karşımıza boş ve siyah bir ekran gelecek. Klavyeden F tuşuna bastığımızda play\(\) methodu müziği oynatacak, S tuşuna basınca stop\(\) methodu müziği durduracaktır. stop\(\) ile durdurulan müzik dosyası durdurulduğu saniyede kalmaz ve başa döner. P tuşuna bastığımızda ise False değerini verdiğimiz pause değişkeni yardımıyla duruma göre müziği bekletmeye ya da kaldığı yerden oynatılacaktır. R tuşuyla da rewind\(\) methodu çalışacak ve çalan müziği başa sarıp oynatacaktır.

Uygulamamız dediğim gibi mükemmel değil. Özellikle müziği oynatmadan P tuşuna basarsak pause değişkeninin değeri değişeceğinden hata vermese de hatalı olacaktır. Müziği oynatmadan bir kere P tuşuna basın sonra oynatın ve tekrar P tuşuna basarak ne demek istediğimi anlayacaksınız. music modülünün get\_busy\(\) methodu müzik dosyası oynatıldıktan çalıp bitene kadar True değeri döndürür. Bu method yardımıyla bekletme işini yapan kodumuzu iyileştirebiliriz.

```text
if pause:
    pygame.mixer.music.unpause()
    pause = False
else:
    pygame.mixer.music.pause()
    pause = True
```

Bu koda tek bir satır ekleyerek sorunu giderebiliriz.

```text
if pygame.mixer.music.get_busy():
    if pause:
        pygame.mixer.music.unpause()
        pause = False
    else:
        pygame.mixer.music.pause()
        pause = True
```

Uygulamamızı biraz daha geliştirelim ve ses seviyesini değiştirme özelliği kazandıralım. Ayrıca çalan müziğin kaçıncı saniyede olduğunu, dosya adını ve ses seviyesini pygame penceresinde gösterelim.

```text
import pygame, sys

pygame.init()

pencere = pygame.display.set_mode((300,200))

muzik_adi = "TheStronges.mp3"
pygame.mixer.music.load(muzik_adi)
pygame.mixer.music.play()

font = pygame.font.Font("GentiumPlus.ttf", 16)

muzik_adi_yazdir = font.render(muzik_adi, True, (255,255,0))

pause = False
ses_seviyesi = 1.0
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_f:
                pygame.mixer.music.play()
            if event.key == pygame.K_s:
                pygame.mixer.music.stop()
            if event.key == pygame.K_p:
                if pygame.mixer.music.get_busy():
                    if pause:
                        pygame.mixer.music.unpause()
                        pause = False
                    else:
                        pygame.mixer.music.pause()
                        pause = True
            if event.key == pygame.K_r:
                pygame.mixer.music.rewind()
            if event.key == pygame.K_UP:
                if not pygame.mixer.music.get_volume() >= 1.0:
                    ses_seviyesi += 0.1
                    pygame.mixer.music.set_volume(ses_seviyesi)
            if event.key == pygame.K_DOWN:
                if not pygame.mixer.music.get_volume() < = 0:
                    ses_seviyesi -= 0.1
                    pygame.mixer.music.set_volume(ses_seviyesi)

    pencere.fill((0,0,0))
    saniye_kac = font.render("Şuan müzik {}. saniyede.".format(int(pygame.mixer.music.get_pos()/1000)), True, (0,255,0))
    ses_seviyesi_kac = font.render("Ses seviyesi: {}".format(int(pygame.mixer.music.get_volume()*100)), True, (0,255,0))

    pencere.blit(muzik_adi_yazdir, (80,50))
    pencere.blit(saniye_kac, (80,75))
    pencere.blit(ses_seviyesi_kac, (80,100))
    pygame.display.flip()
```

Bu örneğimizde font adında bir Font nesnesi oluşturduk. Statik olarak duracağı için hemen altında müzik dosyasının ismini içeren bir Surface nesnesi oluşturduk ve döngümüzde bunu 80x50 konumuza çizdirdik.

ses\_seviyesi adında bir değişken tanımladık ve float olarak 1.0 değerini girdik. Bu değişkeni ses seviyesini değiştirmek istediğimizde değerini değiştireceğiz ve yeni ses seviyesi olarak kullanacağız.

Olay kontrolu kısmına yukarı ok tuşuyla, aşağı ok tuşunun kontrolünü ekledik ve bunların ses seviyesini değiştirmesini sağladık. Tuşlara bastığımızda duruma göre ses seviyesini 0.1 artırıp azaltıyoruz ve set\_volume\(\) methodu ile ses seviyesini belirliyoruz. if bloklarıyla da ses seviyesini 0-1 arasında tutuyoruz.

Dinamik olarak değişkenlik gösterecek olan ses seviyesi ve müziğin o andaki saniyesinin oyun döngümüz arasında Surface nesnelerini oluşturduk ve alt alta gelecek şekilde ekrana çizdirdik.

Kodu çalıştırdığımızda müzik oynamaya başlayacak ve saniyeyi gösteren Surface nesnesi sürekli güncellenecek ve kaçıncı saniyede olduğunu gösterecektir. Yukarı-aşağı ok tuşlarıyla da ses seviyesi değişecek ve 0-100 arası değişiklikleri gösterecektir.

get\_pos\(\) ve get\_volume\(\) methodları float değer döndürdükleri için int\(\) ile bu değerleri integer yaptık ve hata vermesini önledik. Ayrıca görünümleri güzel olsun diye ses seviyesini 100 ile çarpıp müzik çalarlardaki gibi ses seviyesi göstermiş olduk. get\_pos\(\) ise milisaniye döndürdüğü için saniyeye çevirmek için dönen değeri 1000 e bölmüş olduk.

Denemeler yaparken fark ettimki, S tuşuna basıp stop\(\) methodunu çalıştırdığımda saniyenin ilerlemeye devam ediyor. Bunu da bilgi olarak not düşelim...

music modülüyle bir müzik dosyasının oynatılmasını, durdurulmasını vs. öğrendik. Bu modülle oyunun arka planında müzik oynatabilirsiniz. Kılıç, bomba gibi efektleri de bu modülle oynatabilirsiniz, ama müzik oynarken load\(\) methoduyla yükleme yapınca müzik durur. Efektleri music modülüyle değilde mixer modülüyle verebiliriz. İleriki derslerde mixer modülünüde öğreneceğiz.

