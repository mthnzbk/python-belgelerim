# PyQt5 ile Değişken Parametreli Sinyal Oluşturmak

[PisiLinux](http://www.pisilinux.org/) için yardım ettiğim sıralarda package-manager adlı yazılımı PyQt4 den PyQt5 e geçirmeye çalışmıştım. Bu geçişin en önemli kısmı da sinyallerdi. Çünkü Qt5 ile gelen en önemli değişiklik sinyal yapısındaydı. PyQt4'ün son sürümlerinde eski ve yeni diye tabir edeceğimiz iki farklı sinyal-yuva yapısı vardı ve PyQt5\(Qt5\) ile eski sinyal yapısı artık kullanımdan çıkmış oldu. Tam bu noktada package-manager yazılımında bir sinyal yaratımı ve kullanımı beni zor duruma soktu ve bunun çözümü için uğraştım.

Şimdi eski sinyal yapısına bir göz atalım:

```text
from PyQt4.QtGui import *
from PyQt4.QtCore import *
import sys

class Pencere(QMainWindow):

    def __init__(self):
        super().__init__()
        self.widget = QWidget(self)
        self.setCentralWidget(self.widget)

        self.buton = QPushButton(self.widget)
        self.buton.setText("Buton")
        self.connect(self.buton, SIGNAL("clicked()"), self.sinyalCalistir)
        self.connect(self, SIGNAL("ornekSinyal(str"), self.yazdir)
        self.connect(self, SIGNAL("ornekSinyal(int, str, int, str"), self.yazdir)

    def yazdir(self, *args):
        print(*args)

    def sinyalCalistir(self):
        self.emit(SIGNAL("ornekSinyal(str)"), "Merhaba Dünya!")
        self.emit(SIGNAL("ornekSinyal(int, str, int, str"), 1, "Merhaba Dünya!", 2, "Merhaba!")

uygulama = QApplication(sys.argv)
pencere = Pencere()
pencere.show()
uygulama.exec_()
```

Örnek kodda göreceğiniz gibi ürettiğimiz sinyali önceden değil, yayarken\(emit\) üretiyoruz ve SIGNAL\(\) içine yazdığımız string ifade ile sinyalin adını ve parametrelerini belirtiyoruz.

Sinyallere bağlanırken de yine aynı mantık ile bağlanıyoruz. Bunu da **init** methodunda görebiliyoruz.

Eski sinyal yapısına aşina olanlar için kolay gelebilir ve çoğu üretilen sinyaller de bu şekilde üretilmez\(farklı argüman kullanımı\). En azından ben bunu package-manager de görene kadar hiç rastlamadım.

Peki bunu yeni sinyal yapısı ile nasıl yapacaktım?

Yeni sinyal yapısında bir sinyali önceden hazırlıyoruz. Şöyle ki;

```text
from PyQt5.QtWidgets import *
from PyQt5.QtCore import *
import sys

class Pencere(QMainWindow): 

    ornekSinyal = pyqtSignal(str) 

    def __init__(self): 
        super().__init__() 
        self.widget = QWidget(self) 
        self.setCentralWidget(self.widget) 
        self.buton = QPushButton(self.widget) 
        self.buton.setText("Buton") 

uygulama = QApplication(sys.argv)
pencere = Pencere() 
pencere.show() 
uygulama.exec_()
```

Gördüğünüz gibi ornekSinyal adında bir sınıf örneği tanımladık ve SIGNAL\(\) yerine pyqtSignal\(\) kullandık. Peki burada önceki örnekteki gibi sinyali nasıl tanımlayacağız? Eski yöntemle SIGNAL\(\) e string ifade vererek durumu çözüyorduk, ama yeni yöntemle ornekSinyal adını taşıyan sadece tek sınıf örneği olabilir. Aynı isimle birden fazla yazarsak en son yazılan sinyal geçerli olacaktır.

Her ne kadar çözümü pyqt belgelerinde varsa da yeterli dil bilgisi olmadığından bunu çözmem bayağı zaman aldı.

Bir örnek üzerinden çözümü sunalım:

```text
from PyQt5.QtWidgets import *
from PyQt5.QtCore import *
import sys

class Pencere(QMainWindow):

    ornekSinyal = pyqtSignal([int, str, int, str], [str])

    def __init__(self):
        super().__init__()
        self.widget = QWidget(self)
        self.setCentralWidget(self.widget)

        self.buton = QPushButton(self.widget)
        self.buton.setText("Buton")
        self.buton.clicked.connect(self.sinyalCalistir)
        self.ornekSinyal[str].connect(self.yazdir)
        self.ornekSinyal[int, str, int, str].connect(self.yazdir)

    def yazdir(self, *args):
        print(*args)

    def sinyalCalistir(self):
        self.ornekSinyal[str].emit("Merhaba Dünya!")
        self.ornekSinyal[int, str, int, str].emit(1, "Merhaba Dünya!", 2, "Merhaba!")


uygulama = QApplication(sys.argv)
pencere = Pencere()
pencere.show()
uygulama.exec_()
```

Örnek kodda göreceğiniz gibi ornekSinyal adıyla oluşturduğumuz sinyale iki farklı argüman verdik. İlki liste halinde \[int, str, int, str\] ve ikincisi de yine liste halinde \[str\]. PyQt5 de bu durum için bu şekilde bir çözüm üretilmiş. Farklı argümanlı sinyallerimiz için bu örnekteki gibi bir kullanım işinizi görecektir.

sinyalCalistir\(\) methodunda da bu sinyalin yayımı görülüyor. Sinyal adındansonra gelen köşeli parenteze, yukarıdaki argüman tiplerini yazıyoruz ve farklı argümanlar için farklı veriler giriyoruz. Aynı şekilde **init** içinde bu farklı argümanlı sinyallere bağlanırken de aynı yolu izliyoruz ve eğer istersek slot yani yuva için farklı methodlar tanımlama imkanımız da olmuş oluyor.

Bir gün böyle bir sorunla karşılaşırsanız bu yazı aklınızda bulunsun :\)

