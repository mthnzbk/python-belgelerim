# QThread ile QProgressBar Kullanımında Uygulamanın Çökmesi

Bir çok kez PyQt de QProgressBar’ı uygulama donmadan ilerletmek için QThread kullanmayı denedim, ama her seferinde uygulama çöküyordu. Bunun sebebini öğrenmek ve çözümünü bulmak için zamanında çok araştırma yapmıştım ve çözümü bulmuştum. Aradan zaman geçti ve bu sefer [~~Virux~~](https://github.com/mthnzbk/Virux) projemde QThread ve QProgressBar kullanmam icap etti. Zaman aralığı çok olduğundan çözümü unutmuştum ve aynı hataları almamla timer event kullanmak zorunda kaldım. Bugün jeton düştü ve nasıl yaptığımı tekrar hatırladım ve yeni sinyal yapısıyla cebelleştikten sonra uygun bir kodla sorunumu hallettim.

Bu konuda QProgressBar’ın uygulamayı dondurmadan ve çökertmeden nasıl kullanacağımızı göreceğiz… Öncelikle basit bir dialog oluşturup QProgressBar ekleyeceğiz ve uygulama çalıştığında QThread sayesinde ilerlemesini sağlayacağız.

```text
import sys
from PyQt4.QtGui import QDialog, QApplication, QProgressBar
from PyQt4.QtCore import QThread

class Thread(QThread):
    def __init__(self, parent):
        QThread.__init__(self, parent)
        self.parent = parent
        self.sayac = 0

    def run(self):
        while self.sayac<1000000:
            self.parent.progress.setValue(self.sayac)
            self.sayac += 1

class Dialog(QDialog):
    def __init__(self):
        QDialog.__init__(self)
        self.resize(400,250)
        self.progress = QProgressBar(self)
        self.progress.setGeometry(100,75,200,25)
        self.progress.setProperty("value", 0)
        self.progress.setMaximum(1000000)
        self.thread = Thread(self)
        self.thread.start()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    gui = Dialog()
    gui.show()
    sys.exit(app.exec_())
```

Dialog sınıfımızda dialogumuzu oluşturduk ve QProgressBar’ı ekledik. setGeometry\(\) methodu ekrandaki konumunu ayarlamayı, setProperty\(\) methodu ile QProgressBar’ın nasıl ilerleme yapacağını\(%0-%100, 1/100 gibi\), setMaximum\(\) methodu ile de ilerleyişin en fazla hangi rakama kadar çıkacağını ayarladık. Yukarıda oluşturduğumuz Thread sınıfımızı tanımladık ve start\(\) methodu ile de süreçi başlatmış olduk.

Thread sınıfını olabilecek en basit şekilde oluşturmaya çalıştım. Dialog sınıfında kullandığımız start\(\) methodu Thread sınıfımızdaki run\(\) methodunu çalıştıracaktır. Biz bu run\(\) methodunda bir while döngüsü oluşturduk ve sayacı her döngüde +1 artırarak sonucu QProgressBar’ın setValue\(\) methodu aracılığıyla aktardık.

Gayet basit, ama bir kaç tesadüf dışında anlam veremediğiniz hata çıktılarıyla uygulamanızın çöktüğünü göreceksiniz. Buna neden olan şey ise tek satır koddur:

```text
self.parent.progress.setValue(self.sayac)
```

Nedenini bilmeme rağmen bu kodun varlığı hata oluşmasına ve uygulamanın ölmesine-çökmesine neden oluyor. Bu yüzden hatasız bir ilerleme için döngü içinde bu kodu kullanmayacağız…

QThread’ın msleep\(\) methodu ile milisaniye olarak döngü arasında bekletme yaparak ilerleme çubuğunun hareket etmesini izleme fırsatı yakalayabilirsiniz, ama %99 ihtimal ile çökme yaşayacaksınız. Bu sorunu ortadan kaldırmak için kendi sinyalimizi oluşturacağız\(PyQt ye yabancıysanız konuyu bile okumayın bence :\) \). Ve sinyalimizin bir değeri yaymasını sağlayacağız. Dialog sınıfında yazacağımız kod ile bu sinyali yakalayıp taşıdığı değeri QProgressBar’a aktaracağız. Bunu radyo sinyalini yakalayıp sesi duymaya benzetebiliriz…

```text
import sys
from PyQt4.QtGui import QDialog, QApplication, QProgressBar
from PyQt4.QtCore import QThread, SIGNAL

class Thread(QThread):
    def __init__(self, parent):
        QThread.__init__(self, parent)
        self.parent = parent
        self.sayac = 0

    def run(self):
        while self.sayac<1000000:
            self.emit(SIGNAL("setValue(int)"), self.sayac)
            self.sayac += 1

class Dialog(QDialog):
    def __init__(self):
        QDialog.__init__(self)
        self.resize(400,250)
        self.progress = QProgressBar(self)
        self.progress.setGeometry(100,75,200,25)
        self.progress.setProperty("value", 0)
        self.progress.setMaximum(10000)
        self.thread = Thread(self)
        self.thread.start()

        self.connect(self.thread, SIGNAL("setValue(int)"), self.progress.setValue)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    gui = Dialog()
    gui.show()
    sys.exit(app.exec_())
```

Thread sınıfımızda yukarıda söylediğimiz kodu çıkardık ve QThread’ın emit\(\) methodunu kullandık. İlk parametreye SIGNAL\(\) i kullanarak yayılacak sinyalin adını belirledik ve son parametreye ise yayılacak bilgiyi girdik. Biz burada tek bilgi yaymasını sağladık, ama bunu çoğaltabilirsiniz…

Dialog sınımızda self.thread.start\(\) kod parçasıyla sürecimizi başlattık ve sinyay yaymaya başladık. QDialog’ sınıfının connect\(\) methodu ile bu işlemi gerçekleştirdik. İlk parametreye hangi nesnenin sinyalini dinleyeceğimizi yazdık; ikinci parametrede ise hangi sinyali dinleyeceğimizi; son parametremizde ise sinyalin taşıdığı bilgiyi nereye aktaracağımızı yazdık. setValue\(\) methodu tek parametre aldığına göre ve “setValue\(int\)” sinyali tek bilgi taşıdığına göre taşınan bilgi QProgressBar’ aktarılmış oldu ve sorunsuz bir şekilde ilerleme çubuğunun ilerleyişini görme imkanımız doğdu.

