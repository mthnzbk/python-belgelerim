# QSettings Sınıfı Kullanımı

PyQt kullanarak bir uygulama geliştiriyor ve bu uygulamaya ayarlar ekleyecekseniz QtCore modülünde bulunan QSettings sınıfını kullanabilirsiniz. QSettings sınıfı ile _.ini-_.conf uzantılı dosya formatlarında ayarlarınızı saklayabilirsiniz.

QSettings sınıfı ayar dosyanızı olağan şeklinde GNU/Linux sistemlerde /home/kullanıcı/.config/ dizini altında kaydeder. Verdiğiniz parametrelere göre uygulamanızın adıyla bir dizin oluşturulup ayar dosyanız bu dizinde saklanabilir.

```text
from PyQt5.QtCore import *

ayarlar = QSettings("AyarDizini", "AyarDosyasi")
ayarlar.setValue("PencereAyarlari/PencereBoyutu", QSize(300, 300))

print(ayarlar.value("PencereAyarlari/PencereBoyutu"))

PyQt5.QtCore.QSize(300, 300) # çıktı
```

Örneğimizde; ayar dosyasını .config/ içerisine **AyarDizini** adında dizin oluşturup **AyarDosyasi.conf** adında olacak şekilde ayarladık. QSettings modülünün setValue\(\) methodu ile değer yazdırmadan ne dizin oluşur, ne de dosya. Örnekte ise bu methodu kullanarak ayar bilgilerimizi yazdırdık ve value\(\) methodu ile bu değerimizi yazdırdık.

Bu örnekte dikkat edilecek nokta “PencereAyarlari/PencereBoyutu” kısmıdır. Bu string değer ile ayar dosyamızda **PencereAyarlari** adında bir grup oluşturmuş ve bu grubun bir üyesi olarak **PencereBoyutu**'nu eklemiş olduk. PencereBoyutu'nun değerini ise QSize\(300, 300\) olarak atamış olduk. Bu kodun ayar dosyasındaki çıktısı ise şöyle olur:

```text
[PencereAyarlari]
PencereBoyutu=@Size(300 300)
```

Eğer setValue\(\) methodunu kullanırken bölü\(/\) karakteriyle gruplamayı istemiyorsanız beginGroup\(\) methoduyla grup oluşturabilirsiniz. Devamında yazacağınız değerlerde sadece ayarın ismini ve değerini girerek grup üyesi ayarları tanımlayabilirsiniz. Gruba son vermek için ise endGroup\(\) methodunu kullanabilirsiniz.

```text
from PyQt5.QtCore import *

ayarlar = QSettings("AyarDizini", "AyarDosyasi")
ayarlar.beginGroup("PencereAyarlari")
ayarlar.setValue("PencereBoyutu", QSize(300, 300))
ayarlar.endGroup()

print(ayarlar.value("PencereAyarlari/PencereBoyutu"))
# veya
ayarlar.beginGroup("PencereAyarlari")
print(ayarlar.value("PencereAyarlari/PencereBoyutu"))
ayarlar.endGroup()

PyQt5.QtCore.QSize(300, 300) # çıktı
```

Genelde setValue\(\) ile değer tanımladığınızda ayar dosyaya yazılır ve okunmaya hazır hale getirilir. Ancak herhangi bir nedenden dolayı dosyaya yazma işlemi yapılmıyorsa\(Hata oluşmadıysa\) sync\(\) methodunu kullanarak senkronizasyon işlemi yapabilirsiniz.

Bir ayarı silmek istiyorsanız value\(\) methodunu kullandığınız mantıkla remove\(\) methodunu kullanarak ayar silebilirsiniz.

```text
ayarlar.remove("PencereAyarlari/PencereBoyutu")
# veya
ayarlar.beginGroup("PencereAyarlari")
ayarlar.remove("PencereBoyutu")
ayarlar.endGroup()
```

Windows’ta ise QSetting sınıfı ayarları regeditte tutar, ama deneyimime dayanarak regedit kaydının sorunlu olduğunu söyleyebilirim. Bu yüzden Windows için de \*.ini dosyası kullanmak isteyebiliriz. O zaman QSettings nesnemizi tanımlarken şu şekilde yazmalıyız.

```text
ayarlar =  QSettings("DosyaYolu\dosya.ini", QSettings.IniFormat)
```

QSettings’e verdiğimiz ilk parametre dosya yolunu, ikinci parametre ise ayar formatının nasıl olacağını belirtir. Burada QSettings.IniFormat ile bir \*.ini dosyası formatında olmasını sağladık.

Ayar dosyasının nerede tutulduğunu fileName\(\) methoduyla öğrenebilirsiniz. Bu bilgiyle başlangıç ayarlarını yazmak için dosyanın varlığını kontrol etmenize olanak tanımış olur…

