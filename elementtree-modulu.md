# elementtree Modülü

Xml verilerini işlemek için çeşitli yapılar vardır\(dom, sax gibi\). Bu derste ağaç yapısını kullanan ElementTree modülünü öğreneceğiz ve hız ve performans bakımından C ile yazılmışı olan cElementTree modülünü kullanacağız.

Bu modül xml.etree modül paketinin bir parçasıdır. Modülümüzü farklı şekillerde çağırabiliriz.

```text
from xml.etree import cElementTree
import xml.etree.cElementTree as cElementTree
```

Öncelikle xml etiketi oluşturmayı ve bu etiketlere nitelik ve değer atamasını öğrenelim.

```text
from xml.etree import cElementTree

root = cElementTree.Element("diller")

child = cElementTree.SubElement(root, "dil")
child.text = "Python"

child = cElementTree.SubElement(root, "dil")
child.text = "Ruby"
```

Element methoduyla root etiketimizi "diller" adıyla oluşturduk. Çocuk etiket olarak SubElement methodundan faydalandık ve ilk parametresine root değişkenini yazarak ebeveyn etiketini belirledik ve çocuk etiketimizin adını "dil" olarak belirledik. SubElement’in text niteliğini kullanarak etiketimizin değerini "Python" ve sonraki tanımlamamızda "Ruby" olarak girdik.

```text
cElementTree.dump(root)
```

koduyla xml çıktısı alabiliriz.

PythonRuby

Eğer etiketlerimize nitelikte eklemek istersek Element methoduna ikinci, SubElement methoduna üçüncü bir parametreyi sözlük tipinde veri girerek ekleyebiliriz.

```text
from xml.etree import cElementTree

root = cElementTree.Element("diller", {"type":"script"})

child = cElementTree.SubElement(root, "dil", {"version":"3.2"})
child.text = "Python"

child = cElementTree.SubElement(root, "dil")
child.text = "Ruby"

cElementTree.dump(root)
```

PythonRuby

Methodlara parametre olarak girmek yerine methodların attrib niteliğine sözlük verisini girebileceğiniz gibi set\(\) methoduyla da nitelik ekleyebilirsiniz.

```text
child.attrib = {"version":"3.2"}
child.set("version", "3.2")
```

Bir etiketin niteliğinin taşıdığı veriye ihtiyaç duyarsanız;

```text
child.get("version")
```

gibi bir kod ile veriye ulaşabilirsiniz.

cElementTree.dump\(\) sys.stdout ile yazdırır. Bir değer döndürmez. Bunun için cElementTree.tostring\(\) fonksiyonunu kullanmalısınız.

Eğer etiketteki tüm nitelik ve değerlerine ulaşmak isterseniz items\(\) sadece nitelik adına ulaşmak isterseniz de keys\(\) methodunu kullanabilirsiniz.

```text
print(child.items())

[("version", "3.2")]

print(child.keys())

["version"]
```

SubElement kullanmak istemiyorsanız, oluşturduğunuz çocuk Element nesnelerini ebeveyn Element nesnesinin append\(\) methodu ile birer birer, ya da extend\(\) methoduna liste olarak gireceğiniz Element nesneleriyle toplu olarak ekleyebilirsiniz.

Ekleyeceğiniz Element’in sırasını belirlemek isterseniz de insert\(\) methodunu kullanabilirsiniz

```text
root.insert(0, child)
```

Bu kod ile root’a eklenen child ilk sıraya eklenmiş olacaktır.

Kendinize göre bir xml verisi hazırladınız ve bir etiketin çocuğu olan etiketlerin bir listesini almak istediniz. Bunu getchildren\(\) methodu ile alabilirsiniz. Eğer bir etiketin çocuk etiketi varsa Element nesnelerinden oluşan bir liste dönecektir; aksi halde boş bir liste döner. Eğer o çocuk etiketlerinde çocuk etiketi varsa ayrıca o Element nesnelerininde getchildren\(\) methodunu çağırmalısınız.

Silmek istediğiniz bir SubElement nesnesi var ise Element nesnenizin remove\(\) methoduna SubElementi girmelisiniz.

Öğreneceğimiz bir kaç methodu ise örnekle anlatalım ve bunu yaparken de başka şeylerde öğrenelim…

Örneğin elimizde bir xml dosyası var ya da internetten bir xml verisi çekeceğiz. Dışarıdan alınan xml verisini nasıl parse edeceğimizi ve Element’in kalan methodlarını da öğrenelim.

Elimizde şöyle bir xml verisi olduğunu varsayarak kodumuzu yazalım;

http://www.metehan.us/2011-11-20T19:43:57+00:00daily1.0http://www.metehan.us/parcala-buyuk-boyutlu-dosyalari-boler-birlestirir-ve-kontrol-eder.html2011-11-20T19:43:57+00:00daily0.8http://www.metehan.us/python-kurulum-betigi-hazirlamak.html2011-10-16T22:07:54+00:00daily0.8http://www.metehan.us/qlineedit-ile-otomatik-ve-sekme-iletamamlama.html2011-09-23T18:42:40+00:00daily0.8http://www.metehan.us/qsettings-sinifi-kullanimi.html2011-09-14T11:59:34+00:00daily0.8

bu veriyi ayrıştıracağız ve düzenli bir şekilde çıktısını alacağız…

```text
from xml.etree import cElementTree

xmlVerisi = open("sitemap.xml").read()
xmlParse = cElementTree.XML(xmlVerisi)
urlTagList = xmlParse.findall("url")

for urlTag in urlTagList:
    print("URL: {}".format(urlTag.find("loc").text))
    print("Tarih: {}".format(urlTag.find("lastmod").text))
    print("Değişme Zamanı: {}".format(urlTag.find("changefreq").text))
    print("Öncelik: {}".format(urlTag.find("priority").text))
    print("--------------------------------\n")
```

Örnek kodumuz bu… sitemap.xml dosyasını okuyup XML\(\) fonksiyonuna parametre olarak xml verisini girdik ve parse işlemini gerçekleştirmiş olduk. Bu fonksiyon geriye bir Element nesnesi döndürür. Bu Element nesnesi root etiketi taşır; yani örnek verimizse “urlset” etiketi. Xml verisindeki url etiketine sahip olanlar findall\(\) methoduyla liste olarak aldık ve bir for döngüsü oluşturduk. Bu döngüde url etiketinin çocuğu olan loc, lastmod, changefreq ve priority etiketlerini find\(\) methodu ile taşıdığı veriyi aldık. Örnekten de anlaşılacağı gibi find\(\) methodu tek bir sonuç döndürüyor\(aynı etiketten fazla olsa da ilk sıradakini alır\).

Son olarak ise bu modülle oluşturulan xml verilerinin çıktısı okunabilir olmuyor. Okunabilirlik için **Kaynak**‘tan ikinci bağlantıyı inceleyebilirsiniz.

Kaynak: [The ElementTree XML API](http://docs.python.org/library/xml.etree.elementtree.html)

