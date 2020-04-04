# zipfile Modülü

zipfile modülü zip dosyalarını işlemek için gerekli araçları sunar. Bu modülde zip dosyalarını okumak, yazmak ve var olan bir zip dosyasına dosya eklemek için ZipFile sınıfı mevcuttur. Küçük bir örnek kodla zipfile modülünü öğrenmeye başlayalım…

```text
import zipfile

zip_dosyasi = zipfile.ZipFile("zip_dosyası.zip","r")

for dosya in zip_dosyasi.namelist():
    print(dosya)
```

Yukarıda ki kodda modülümüzü import ettik ve zip\_dosyası.zip dosyasını okuma\("r"\) kipiyle açtık\(okuma kipiyle açacaksak "r" parametresini yazmamız şart değildir\).

ZipFile sınıfının namelist\(\) methodu zip dosyası içerisindeki dosyaların bir listesini döndürür. Bu listeyide for döngüsü ile yazdırmış oluyoruz. Zip dosyamızda **döküman.odt** ve **döküman.pdf** dosyalarının olduğunu varsayarsak çıktımızda alt alta bu iki dosyanın ismini görürüz.

Bu örneğimizde zip\_dosyası.zip dosyasının bir zip dosyası olduğunu varsaydık. Peki, bir dosyanın zip dosyası olduğundan emin olmak için ne yapabiliriz? Tabii ki zipfile modülünde bulunan bir fonksiyon sayesinde:

```text
zip_mi = zipfile.is_zipfile("zip_dosyası.zip")

if zip_mi:
    print("Bu dosya gerçek bir zip dosyasıdır.")
else:
    print("Bu bir zip dosyası değildir.")
```

is\_zipfile\(\) fonksiyonuyla parametre olarak girdiğimiz zip\_dosyası.zip dosyasının gerçek olduğunu döndürdüğü değerden anlıyoruz. Eğer **True** değeri dönerse if bloku işlem görecektir. **False** ise else bloku…

ZipFile sınıfı zip arşivindeki dosyaları çıkartmak için üç adet method sunar. Bu üç methodun işlevlerini öğrenmemizi sağlayacak küçük bir kod yazalım.

```text
zip_dosyasi.extract("döküman.pdf", path=None)
zip_dosyasi.extractall(path=None, members=None)

dosya = zip_dosyasi.read("döküman.pdf")
dosya_yaz = open("döküman.pdf,"wb")
dosya_yaz.write(dosya)
dosya_yaz.close()
```

Örnek kodumuzda göstermek amacıyla path ve members parametrelerini de ekledik; bu sayede methodlarımızın tam olarak işlevini de öğrenmiş olacağız… path parametresi dosya\(lar\)nın nereye çıkarılacağını belirtmek için kullanılır.

extract\(\) methodu ilk parametresinde belirtilen dosyayı varsayılan olarak zip arşiviyle aynı dizine çıkarır, ama path parametresiyle yazılabilir her hangi bir alana dosyamızı çıkarabiliriz.

extractall\(\) ise zip arşivindeki bütün dosyaları çıkarmamıza izin verir. Eğer biz members parametresine zip arşivinde bulunan dosyaların bir kısmının listesini verirsek bu sefer listede belirtilen dosyaları çıkaracaktır.

En uğraştırıcı method ise read\(\) methodu olsa gerek. Bir dosyayı çıkarmaktan öte o dosyayı okuma işini yapar. Bu okunan veriyi de yukarıdaki örneğimizde de görüldüğü gibi dosyaya yazabiliriz. read\(\) methodu file nesnesindeki gibi girilen miktarda okuma yapabilse büyük boyutlu dosyaları okuma da progress gösterebilmek için kullanılabilirdi.

extract\(\) ve extractall\(\) methodları Python2.6 sürümüyle dahil olmuştur. Ayrıca Python2.5 sürümünde Türkçe karakter içeren dosyalarda karakterler bozuk gözüküp işlem yapmanızı engellemektedir.

Bir zip arşivi oluşturmak için open\(\) fonksiyonundaki gibi “w” parametresini kullanıyoruz.

```text
import zipfile

zip_dosyasi = zipfile.ZipFile("zip_dosyası.zip","w")

zip_dosyasi.write("döküman.pdf")
zip_dosyasi.write("döküman.odt")
zip_file.close()
```

Gördüğünüz gibi open\(\) fonksiyonuyla yaptığımız normal dosya yazımıyla neredeyse aynı. write\(\) methodu ise ikinci bir parametre alır. Bu parametreye gireceğimiz string veri zip dosyasına yazdığınız dosyanın zip dosyasındaki ismi olarak yazılır.

```text
zip_dosyasi.write("döküman.pdf","döküman2.pdf")
```

Bu kodla arşive yazdığımız "döküman.pdf" dosyası arşive "döküman2.pdf" olarak yazılır.

Arşivleme yazılımlarında zip dosyasının sıkıştırma oranlarını ayarladığımız kısımı kullanmışsınızdır. ZipFile sınıfına verilecek üçüncü bir parametreyle sıkıştırma oranını belirtebilirsiniz.

```text
zip_dosyasi = zipfile.ZipFile("zip_dosyası.zip","w",8)
```

Sıkıştırma oranı 0-8 değerlerinde olmalıdır. zipfile modülü 0\(sıfır\) sıkıştırma için zipfile.ZIP\_STORED, en iyi sıkıştırma olan 8 için zipfile.ZIP\_DEFLATED değişkenlerini barındırır. Varsayılan parametre değeri zipfile.ZIP\_STORED dır. Ayrıca write\(\) methodu ile dosya eklerken compress\_type parametresiyle 0-8 arası bir değer vererek her dosya için ayrı sıkıştırma oranı verebilirsiniz.

Var olan bir zip dosyasını açıp içindekileri koruyarak dosya eklemek için ise “w” parametresi yerine “a” parametresi kullanıyoruz. Geriye kalan tüm işlemleri “w” parametresiyle yaptığımız gibi yapabiliriz.

Zip arşivindeki bir dosya hakkında bilgi edinmek için ZipInfo sınıfından yararlanırız. ZipFile sınıfının infolist\(\) methodu arşivdeki her dosya için ZipInfo sınıfı döndürür.

```text
import zipfile

zip_dosyasi = zipfile.ZipFile("zip_dosyası.zip","r")

print(zip_dosyasi.infolist()) # ya da
print(zip_dosyası.getinfo("döküman.pdf"))
```

infolist\(\) methodu bütün dosyaları içeren bir ZipInfo listesi döndürürken getinfo\(\) ile tek bir dosya için ZipInfo döndürür. Biz getinfo\(\) ile aldığımız ZipInfo sınıfının ne gibi özellikleri sakladığını bir kod parçasıyla öğrenelim.

```text
import zipfile

zip_dosyasi = zipfile.ZipFile("zip_dosyası.zip","r")

info = zip_dosyası.getinfo("döküman.pdf")

print("Dosya Adı: ", info.filename)
print("Yorum: ", info.comment)
print("Tarih: ", datetime.datetime(*info.date_time))
print("Sistem: ", info.create_system, "(0 = Windows, 3 = Unix)")
print("Zip version: ", info.create_version)
print("Sıkıştırılmış Boyut: ", info.compress_size, "byte")
print("Sıkıştırma Oranı: ", info.compress_size)
print("Boyut: ", info.file_size, "byte")
```

```text
Dosya Adı: döküman.pdf 
Yorum: Tarih: 2011-06-27 12:26:46 
Sistem: 0 (0 = Windows, 3 = Unix) 
Zip version: 30 
Sıkıştırılmış Boyut: 5643903 byte 
Sıkıştırma Oranı: 8 Boyut: 15698676 byte
```

Örnek kodumuzda ZipInfo sınıfının önemli niteliklerinin ne işe yaradığını açıkladık. Bunun yanında açıklamadığımız niteliklerde mevcut. Bunlarıda Python yorumlayıcısında help\(zipfile\) yazarak ya da Python’un resmi dökümanlarını inceleyerek öğrenebilirsiniz.

