# tarfile Modülü

Python tarfile modülü tar arşivleriyle çalışabilmemizi sağlar. Bu modül, _.tar dosyalarını okuma, yazma ve ekleme;_ .tar.gz ve \*.tar.bz2 dosyalarını ise okuma ve yazma marifetine sahiptir.

Elinizde \*.tar uzantılı bir dosya var ve siz bunun tar dosyası olduğundan emin değilsiniz, ama Ark, Winrar veya bir başka arşiv yöneticisiyle açabiliyorsunuz. Peki, bu dosyanın tar arşivi olduğundan nasıl emin olabiliriz?

tarfile modülü bize bunu anlamak için güzel ve kullanımı basit bir fonksiyon sunar. Bunu güzel bir örnekle anlatalım.

```text
import tarfile
tar_file = "python.tar"
print(tarfile.is_tarfile(tar_file))
```

Burada tar\_file değişkenine tar dosyamızın adını atadık ve is\_tarfile\(\) fonksiyonu ile kontrol ettirip çıktıyı print ile yazdırdık. Çıktımız True olduğuna göre python.tar isimli dosya kesinlikle bir tar dosyasıdır. Gzip ve bzip ile sıkıştırılmış tar dosyalarıda bu testte True sonucunu döndürecektir.

tarfile modülüyle bir tar dosyasını okumak için open\(\) fonksiyonu kullanılır.

```text
import tarfile

tar_file = "python.tar"
tar = tarfile.open(tar_file, "r") # yazma veya ekleme kipinde açmayacaksak "r" kısmını atlayabiliriz.
print(tar.getnames())
```

```text
["zipfile.py","tarfile.py"]
```

getnames\(\) fonksiyonu ise yukarıda görüldüğü gibi tar arşivinde gömülü olan dosyaları bir liste olarak verir. Biz istersek bunu kısa bir for döngüsüyle sıralayabiliriz.

```text
for name in tar.getnames():
    print(name)
```

Elimizdeki tar arşivini bir arşivleme aracı ile açtığımızda görürüz ki; her dosyanın sağ tarafında oluşturulma tarihi, boyutu veya sıkıştırılmış boyutu vardır. Bizde eğer arayüzlü bir uygulama yazacaksak getmembers\(\) ve getmember\(\) fonksiyonlarını kullanabiliriz. Bu iki fonksiyonun kullanımını görelim:

```text
import tarfile

tar = tarfile.open("python.tar")
bilgi = tar.getmember("zipfile")

print("Dosya adı:", bilgi.name) # dosya adını verir
print("Boyut:", bilgi.size) # sıkıştırılmış dosya boyutunu verir
print("mtime:", bilgi.mtime) # dosyanın oluşturulma tarihini verir. Diğer: time.ctime(bilgi.mtime)
print("Dosya yolu:", bilgi.path) # Dosyanın yolunu verir.
```

Diğer seçenekleriniz ise linkpath, uid, gid, uname, gname.

```text
import tarfile

tar = tarfile.open("python.tar")

for bilgi in tar.getmembers():
    print("Dosya adı:", bilgi.name)
    print("Boyut:", bilgi.size)
    print("mtime:", bilgi.mtime)
    print("Dosya yolu:", bilgi.path)
```

İlk örneğimizde tek bir dosyanın bilgilerini öğrendik, ikinci örneğimizde ise for döngüsü yardımıyla arşivdeki bütün dosyaların bilgilerini elde ettik. Şimdi ise bir tar arşivinin nasıl oluşturulacağını ve arşiv dosyalarının nasıl çıkarılacağını görelim.

Bir tar arşivinden üç ayrı fonksiyonla dosya çıkartabiliriz. Bunlardan en az pratiği olan extractfile\(\) fonksiyonunun kullanımı şu şekildedir:

```text
import tarfile

tar = tarfile.open("python.tar")
dosya = tar.extractfile(filename)
dosya.read()
```

Yukarıdaki kodları açıklayacak olursak; extractfile\(\) ile belirttiğimiz dosyayı arşivden çıkardık, ama herhangi bir yere yazmadık ya da okumadık. Okuma işlemini read\(\) fonksiyonuyla yaptık. Tabi böyle bir şeyi yaptığımızda dosyanın boyutuna göre konsol ekranı o kadar çok dolacaktır. Çıkardığımız dosyayı yazdırmak için ise Python ortamında gömülü\(built-in\) olarak bulunan open\(\) fonksiyonundan yararlanacağız.

```text
d = open(filename, "w")
d.write(dosya.read())
d.close()
```

Bu yazdırma tarzı size zahmetli geliyorsa diğer fonksiyonları deneyebilirsiniz.

```text
import tarfile

tar = tarfile.open("python.tar")
tar.extract(filename)
```

Üç satırlık bir kodla belirttiğimiz dosyayı bulunduğumuz dizine çıkardık. Tabi biz istersek farklı bir dizine de çıkarabiliriz.

```text
tar.extract(filename, path="/home/metehan") # 'path=' ifadesini kullanmasanız da olur
```

Hiç zahmete girmeden bütün dosyaları çıkarmak istiyorsak son fonksiyonumuz işinizi görecektir.

```text
tar.extractall()
```

Tabi ki bununda aldığı parametler vardır.

```text
tar.extractall(path, members)
```

Buradaki path ifadesini yukarıda görmüştük. members ifadesi ise eğer siz tüm arşivi çıkarmayacaksanız dosya isimlerinin yer aldığı bir listeyi belirtirseniz fonksiyon belirttiğiniz listedeki dosyaları çıkartacaktır. Eğer amacınız tar dosyalarıyla ilgili bir uygulama yazmak ise ve çıkartılan dosyaların hangileri olduğunu göstermek istiyorsanız şöyle küçük bir kod yazabiliriz:

```text
for i in tar.getnames():
    print i
    tar.extract(i)
```

Tar arşivleri üçe ayrılır: Sıkıştırılmamış _.tar arşivleri, Gzip ile sıkıştırılmış_ .tar.gz arşivleri ve BZ2 ile sıkıştırılmış \*.tar.bz2 arşivleridir. Bu dosyalarla çalışmak için her birine özel fonksiyonlarda mevcuttur\(taropen\(\), gzopen\(\), bz2open\(\)\). Fakat biz bu üç fonksiyonu kullanmayacağız. Yukarıda kullandığımız open\(\) fonksiyonu zaten hepsini barındırmaktadır. Her üç arşiv tipi için değiştirmemiz gereken tek şey açma modudur. Hemen üç arşiv tipinin nasıl yazma kipinde açılacağını örnekle verelim.

```text
import tarfile

tar = tarfile.open("tar_arsivi.tar","w")
tarbz2 = tarfile.open("targz_arsivi.tar.bz2","w:bz2")
targz = tarfile.open("targz_arsivi.tar.gz","w:gz")
```

Bu üç arşiv tipinin tek farkı açma modudur. Dosya ekleme işlemleri aynıdır. Ben tar değişkeniyle devam ediyorum.

```text
import tarfile

tar = tarfile.open("tar_arsivi.tar","w")
tar.add("nrg2iso.c")
tar.close()
```

Aynı şekilde bir dizin ismi belirttiğinizde, dizin ve içeriği arşive eklenecektir. Eğer biz dizin belirttiğimizde dizindeki dosyaların teker teker eklenişini ekranda görmek istiyorsak biraz meşakkatli olacak. Ben de ~~**linzip**~~ adı verdiğim uygulamamda bunu kullanmak isteğim sonucu forumlardan birkaç arkadaşın yardımıyla çözdüm ve hem örneğimizde kullanmış oluruz, hem de dakikalarca akabilecek bir çıktı sayesinde acemi arkadaşlarınıza sisteminiz siliniyor şakası yapabilirsiniz :\)

```text
import os
import tarfile

def listele(dizin):
    liste = []
    for i, z, c in os.walk(dizin):
        for x in c:
            liste.append(i +"/"+ x)
    return liste

dizin = "/home/MtHan/Belgeler"

tar = tarfile.open("tar_arsivi.tar","w")

if os.path.isdir(dizin):
    for i in listele(dizin):
        print i
        tar.add(i)
```

listele\(\) fonksiyonumuz bizim için düzgün bir şekilde dosya yolunu verecek ve biz bunu for döngüsü aracılığıyla tek tek tar arşivimize ekleyeceğiz. Eklenmeden öncede konsola hangi dosyanıneklendiğini yazdırmış olacağız.

Evet, son olarak var olan bir tar arşivine nasıl dosya eklenildiğini görelim. Python hakkında bilgi sahibiyseniz, normal bir dosyanın nasıl eklenmek üzere açıldığını bilirsiniz. Açma modunu “a” olarak ayarladığımızda dosyamızı üzerine ekleme yapacak şekilde açmış oluruz. Deneyimlerim sonucu bu “a” modu sadece tar dosyalarında kullanılabiliyor. Yani tar.gz ve tar.bz2 dosyalarını “a” modunda açamıyoruz. Bu yazıyı okuduktan sonra “a:gz” veya “a:bz2″ modunu deneyerek beni yalancı çıkarmak isteyenlere teessüflerimi bildiririm :\) Küçük bir örnekle nasıl bir tar arşivine dosya ekleyebileceğimizi görelim.

```text
import tarfile

tar = tarfile.open("tar_arsivi.tar","a")
tar.add("night.jpg")
tar.close()
```

Aynı şekilde dizin de eklendiğini öğrenmiştik. Yine üstteki örneğimizden yararlanarak daha güzel kodlar geliştirebiliriz. tarfile modülünün en önemli özelliklerini öğrenmiş olduk. Daha başka neler var diye merak ederseniz tarfile modülünü import ettikten sonra help\(tarfile\) diyerek yardım alabilirsiniz. Burada anlatılanlardan daha fazlasını öğrenmeye niyetiniz varsa sizin, güzel bir arşiv yöneticisi yapmaya çalıştığınızı tahmin ediyorum.

