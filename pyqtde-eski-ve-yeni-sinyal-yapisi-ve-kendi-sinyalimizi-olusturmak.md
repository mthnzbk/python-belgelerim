# PyQt'de Eski ve Yeni Sinyal Yapısı ve Kendi Sinyalimizi Oluşturmak

Bir PyQt uygulamasında mevcut tüm widgetler kullanıcı eylemi ya da bir durum değişikliğini belirtmek için sinyaller yayar. Örneğin QPushButton, kendisine tıklandığında bir clicked\(\) sinyali yayar. Bir sinyali kullanabilmek için bir yuvaya\(slot\) bağlamamız gerekir. Böylece sinyalin yayılmasına yol açan bir durum olduğunda yuva\(slot\) çalıştırılır. PyQt4 de kullanılan eski tip sinyal yapısı şu şekildedir:

```text
class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super(QMainWindow, self).__init__(parent)
        "..."
        button = QPushButton(self)
        button.setText("Kapat")

        QObject.connect(button, SIGNAL("clicked()"), self, SLOT("close()"))
```

QObject, SIGNAL ve SLOT, QtCore içinde mevcuttur... Örnek kodda görüldüğü gibi sinyal ve yuva bir QObject sınıfında belirtiliyor. Tabii connect\(\) methodu QMainWindow içinde de mevcut olduğu için\(QObject her sınıfın bir alt sınıfıdır\) QObject.connect\(\) yerine self.connect\(\) de kullanabilirsiniz. Qt Designer ile bir pencere tasarladığınızda, ayarladığınız sinyaller yukarıdaki kodda yazıldığı gibi dönüştürülür.

Yine PyQt4 de gelen ve PyQt5 de tek tercih olarak sunulan yeni sinyal yapısı ise geliştiricilerin işini kolaylaştıran ve Qt'yi Python ile kullananlar için daha Pythonic bir kodlama yapısı sunan yeni sinyal/yuva yapısı ise şöyledir:

```text
class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super(QMainWindow, self).__init__(parent)
        "..."
        button = QPushButton(self)
        button.setText("Kapat")
        button.clicked.connect(self.close)
```

Görüldüğü gibi sinyaller birer sınıf methodu gibi kullanılıyor ve bu sinyallere yuva bağlayacağımız zaman da sinyalin connect\(\) methodu ile tetiklenecek methodu belirtiyoruz. Yalnız unutmadan bir sinyal ve yuva belirtirken eski sinyal yapısında kullanılan parantezleri kullanmıyoruz. Eğer bir sinyal None değil de str, int vs. gibi bir veya bir kaç parametrelik veri de yayıyorsa yuvaya bağladığınız methoda-fonksiyona da o kadar parametre tanımlamalısınız. Misal:

```text
class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super(QMainWindow, self).__init__(parent)
        "..."
        horizontalSlider = QSlider(self)
        horizontalSlider.setOrientation(Qt.Horizontal)
        progressBar = QProgressBar(self)

        # eski tip sinyal yapısı
        QObject.connect(horizontalSlider, SIGNAL("valueChanged(int)"), progressBar, SLOT("setValue(int)"))

        #yeni tip sinyal yapısı
        horizantalSlider.valueChanged.connect(progressBar.setValue)

        #yukarıda bahsettiğim gibi kendi yazdığımız bir yuvaya örnek
        horizantalSlider.valueChanged.connect(self.degerYazdir)

    def degerYazdir(self, deger):
         print(deger)
```

En son yazdığım sinyal yapısı ile anlatılanı anlamışsınızdır umarım...

Qt ile gelen varsayılan sinyaller dışında kendi sinyallerimizi de oluşturabiliriz. QThread ile döngü içeren bir sınıf yazdığımızı düşünelim ve bu sınıftan yayılacak bir sinyal ile QProgressBar widgetinin değerini artıracak kodu eski ve yeni tarz sinyal yapısı ile yazalım.

```text
from PyQt5.QtCore import QThread, pyqtSignal
from PyQt5.QtWidgets import QProgressBar, QMainWindow

# Yeni tarz sinyal oluşturma.
class Thread(QThread):
    def __init__(self, parent=None):
        super(Thread, self).__init__(parent)

    counter = pyqtSignal(int)
    def run(self):
        for i in list(range(1,100)):
            self.counter.emit(i)
            self.sleep(1)

class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super(QMainWindow, self).__init__(parent)
        progressBar = QProgressBar(self)
        thread = Thread(self)
        thread.start()
        thread.counter.connect(progressBar.setValue)

# Eski tarz sinyal oluşturma
from PyQt4.QtCore import QThread, SIGNAL, SLOT
from PyQt4.QtGui import QMainWindow, QProgressBar

class Thread(QThread):
    def __init__(self, parent=None):
        super(Thread, self).__init__(parent)

    def run(self):
        for i in list(range(1,100)):
            self.emit(SIGNAL("counter(int)"), i)
            self.sleep(1)

class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super(QMainWindow, self).__init__(parent)
        progressBar = QProgressBar(self)
        thread = Thread(self)
        thread.start()
        self.connect(thread, SIGNAL("counter(int)"), progressBar, SLOT("setValue(int)"))
```

Yeni sinyal yapısında methodların dışında tanımladığımız değişkenin değerini pyqtSignal\(\) yapıyoruz. Eğer bu sinyal bir veri yayacak ise ve kaç taneyse bu fonksiyona parametre olarak belirtilir. Oluşturduğunuz sinyallerle int, str dışında sınıfları, binary verileri vs. hemen hemen her şeyi yayabilirsiniz. Yayma işlemi de yukarıdaki counter örneğinin emit\(\) methodu ile sağlanıyor. Buraya pyqtSignal\(\) de belirttiğiniz kadar parametre girmelisiniz. Böylece Thread sınıfı çalıştırıldığında her saniye counter sinyali yayılacaktır. MainWindow sınıfında ise bu sinyali yakalıyoruz ve QProgressBar'ın setValue\(\) methoduna-yuvasına bağlıyoruz. Kod çalıştığında her saniye sinyal yayılacak ve QProgressbar'ın setValue\(\) methodunca yakalanacaktır. Yukarıdaki kod bu haliyle çalışmaz, ama bu yazıdaki amaç da zaten bu değil, sinyal ve slot yapısının anlatımıdır.

Eski tarz sinyal yapısında ise mevcut QThread sınıfının emit\(\) methodunu kullanıyoruz\(hangi sınıf içerisinde oluşturduysanız o sınıfın emit\(\) methodunu kullanın\). İlk parametreye SIGNAL\(\) fonksiyonunu yazıyoruz ve buna vereceğimiz string parametre ile yayılacak sinyalin adını belirtiyoruz. Eğer yukarıdaki gibi bir veri taşınacaksa o kadar değer emit\(\) methoduna SIGNAL\(\) den sonra parametre olarak girilmelidir. Yukarıdaki kodda tek veri taşındığı için, tek parametre belirttik.

