# QLineEdit ile Otomatik ve Sekme ile Tamamlama

QLineEdit’e girdiğimiz bilgiyi kontrol ederek string verilerden oluşan bir listede arama yapıp eşleşen verileri listeleyebiliriz. Bu sayede; bir arama işlemi yapıyorsanız geçmiş kaydı tutabilirsiniz.

Listede arama yapıp eşleşen sonuçları bulan sınıf ise QCompleter'dir. Bu sınıfa vereceğimiz bir liste argümanı ile QLineEdit’te otomatik tamamlama yapabiliriz.

```text
import sys
from PyQt4.QtGui import QApplication, QLineEdit, QCompleter

meyveler = ["elma", "armut", "karpuz", "ayva", "erik"]

class LineEdit(QLineEdit):
    def __init__(self, parent = None):
        QLineEdit.__init__(self, parent)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = LineEdit()
    completer = QCompleter(meyveler)
    window.setCompleter(completer)
    window.show()
    sys.exit(app.exec_())
```

Çok kısa bir kod ile işimizi hallettik. “meyveler” adında bir liste tanımladık ve bu listeye mevye isimlerini girdik. if blokunda ise “completer” adında bir QCompleter nesnesi oluşturduk ve argüman olarak “meyveler” adlı listeyi verdik. QLineEdit’in setCompleter\(\) methoduyla QCompleter nesnemizi tanıttık. Yukarıdaki kodu çalıştırdığınızda girdiğini karaktere göre sonuçları QLineEdit’in altında listeleyecektir. Örneğin ilk karakteri “e” olarak girerseniz “elma” ve “erik” verileri alt alta sıralanacaktır.

Şimdi buna TAB yani sekme tuşuna basıldığında ilk sıradaki verinin QLineEdit’e yazılmasını sağlayalım…

```text
import sys
from PyQt4.QtCore import Qt
from PyQt4.QtGui import QApplication, QLineEdit, QCompleter

meyveler = ["elma", "armut", "karpuz", "ayva", "erik"]

class LineEdit(QLineEdit):
    def __init__(self, parent = None):
        QLineEdit.__init__(self, parent)

    def keyPressEvent(self, event):
        if event.key() == Qt.Key_Tab:
            for item in meyveler:
                if item.startswith(self.text()):
                    self.setText(item)
                    break
            event.accept()
        else:
            QLineEdit.keyPressEvent(self, event)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = LineEdit()
    completer = QCompleter(meyveler)
    window.setCompleter(completer)
    window.show()
    sys.exit(app.exec_())
```

QLineEdit sınıfının keyPressEvent\(\) methodunu kullanarak klavyeden basılan tuşları kontrol edecek kodlarımızı yazdık. Eğer klavyeden sekme tuşuna basarsak if bloku işlenecek ve listedeki elemanları sırayla kontrol edip girdiğiniz karakterlerle başlayan bir eleman varsa QLineEdit’e yazdırdık. Sekme tuşuna basılmaması durumunda da ekrana karakter yazmaya devam edebilmek için else blokunu yazdık. Eğer bu kısım olmaz ise klavyeden basılan hiç bir tuş ile QLineEdit’e veri girişi yapamayız.

Örnek olarak verdiğim listeyi veritabanından çektiğiniz verilerden de oluşturabilirsiniz ve aklınızı kullanarak girdiğiniz verileri veritabanına kayıt ederek kalıcı olmasını ve sonraki kullanımlarda hatırlanmasını sağlayabilirsiniz.

