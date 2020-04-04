# Python'da Kaynak Kodları Gizlemek

Genelde Python ile kodladığımız bir yazılımı kaynak kodlarıyla paylaşırız. Python öğrenenlerde genellikle açık kaynak ve/veya özgür yazılım olarak kodlarını yayınlarlar. Eğer siz diğer gruba dahilseniz ya da en azından ilk başta kaynak kodlarınızı incelemek isteyenlerden; kötü yazılmış kodlarınızı saklamak isteyenlerdenseniz okumaya devam edin…

Amacı, kaynak kodları kapalı bir yazılım geliştirmek isteyen birisi ~~en basitinden py uzantılı dosyaları derleyerek pyc olarak dağıtabilir~~ ya da py2exe, pyinstaller ve cx\_freeze gibi modüllerle çalıştırılabilir binary dosyaya dönüştürebilir.

Bizim amacımız ise masumca :\) Amacımız python modüllerimizi ve/veya paketlerimizi tek bir yerde toplamak ve kalabalık yapmamak. Örneğin çalıştıracağımız py uzantılı dosyamızın adı oyun.py olsun ve aynı dizinde “lib” adında bir dizin içinde ise **init**.py, gui.py, ai.py dosyaları olsun. Bu durumda dosya yapımız şöyle olacaktır:

```text
oyun.py
lib/
---- __init__.py
---- ai.py
---- gui.py
```

lib dizinini zip dosyası haline getireceğiz ve farklı bir isim ve uzantıyla kaydedeceğiz. Sizin bu yöntemi kullandığınızı bilmeyen acemiler kaynak kodlarınızı bulamayacaktır :\) Amacım kapalı kaynak yazılım yazmayı özendirmek değil. Şu güne kadar ne kodladıysam hepsini gpl v3 ile lisanslamışımdır.

Konuya dönelim ve bu zipleme işini de zipfile modülü ile yapalım. Bu modülü bilmeyenler daha önce yazdığım [yazımı](https://github.com/mthnzbk/python-belgelerim/tree/2f9256f0ea623d872b452bdcf6cd3d9537a86158/python-zipfile-modulu.html) inceleyebilirler.

```text
import zipfile

zip = zipfile.ZipFile('save.data',"w",zipfile.ZIP_DEFLATED)
zip.write("lib/__init__.py")
zip.write("lib/ai.py")
zip.write("lib/gui.py")
zip.close()
```

lib diziniyle aynı dizinde bu betiği çalıştırırsanız save.data adında bir zip arşivi oluşacaktır. Şimdi bu save.data adlı arşivimizi oyun.py de çağıralım.

```text
import sys

sys.path.insert(0, "save.data")

import lib.ai
from lib import gui
```

Görüldüğü gibi fazladan iki satır kod yazıp arşiv dosyamızı path'e ekledik ve kodları gizlemiş olduk.

Eğer modüllerinizde aynı dizin içinde, yahut zip arşivinin içine gelecek şekilde bir dosya okumaya ve/veya yazmaya çalışırsanız hata alırsınız. Pygame ile resim dosyasını göstererek Surface nesnesi oluşturmayı denerseniz hata alırsınız. Benim deneyimlerimden bunlar açığa çıktı. Bu yöntemi kullanacaksanız bunun gibi hata çıkaracak unsurlara dikkat ediniz.

