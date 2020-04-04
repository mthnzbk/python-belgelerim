# Django ile Çevirimiçi Ziyaretçi Sayacı Yapımı

İlk Django yazımda çevirimiçi ziyaretçilerin hesabını yapan bir uygulama yapımını anlatacağım. Bu dersten bir şey anlamak için Django hakkında biraz bilgi sahibi olmanız gerekecektir.

Türkçe kaynak için Google araması yaptığınızda karşınıza çıkacak kaynaklar büyük ihtimal şöyle olacaktır:

* Django ile blog yapımı
* Django belgelendirmelerindeki tutorialın aynısı, yani anket uygulaması

Ve başlangıç düzeyinde; internet üzerinde Türkçe olarak zaten yaygın olan başlangıç konuları… Benim anlatacağım konu da belki bu seviyede olacaktır, ama daha önce kafamın almadığı middleware konusunu azcık kavramış olmak ve bunu pratik olarak kullanmak olacak.

Öncelikle uygulamamızı deneyebilmek için bir proje oluşturmamız gerekiyor:

```text
django-admin.py startproject sayacprojesi
```

Bu komutu konsolda veriyoruz ve projemizi oluşturuyoruz. Şimdi proje dizinine girip konsoldan şu komutu veriyoruz:

```text
python manage.py startapp onlinecounter
```

Sayaç uygulamamızın temelini atmış olduk. Uygulamanın içine girelim ve çevirimiçi kullanıcıların veritabanında saklanması için gerekli olan model sınıfımızı models.py içine yazalım.

```text
from django.db import models

class OnlineCounter(models.Model):
    ip = models.IPAddressField(verbose_name="IP Address", db_column="visitor_ip")
    is_user = models.BooleanField(verbose_name="User ?", db_column="is_user", default=False)
    visited_time = models.TimeField(verbose_name="Visited Time", db_column="visitor_time", auto_now_add=True)

    class Meta:
        db_table = "online_counter"
        verbose_name_plural = "Online Counter"
```

OnlineCounter adında bir Model sınıfı oluşturduk ve ip, is\_user ve visited\_time adında üç değişken tanımladık. Bunlardan ip değişkenine ip adresi kayıt edileceğinden “models” modülünün sağladığı IPAddressField sınıfını kullandık. Bu sınıf ip adresi için oluşturacağınız sütun için gerekli sql kodunu sağlar. Bu sütunun adının ise visitor\_ip olarak gözükmesini sağlıyoruz.

is\_user değişkenine ise BooleanField sınıfını kullandık. Siteyi ziyaret eden kişinin kayıtlı bir üye mi yoksa misafir mi olduğunu True ve False olarak veritabanında saklayacağız. BooleanField sınıfına db\_column parametresiyle girdiğimiz “is\_user” verisiylede sütun adını belirlemiş olduk. verbose\_name parametresi Django yönetici panelinde verinin ne olduğu belirten bir ayrıntı olarak kullanılır.

Ziyaretçinin siteye girdiği anı belirlemek için de visited\_time değişkenine TimeField sınıfını atadık. Sütun adını "visitor\_time" olarak ayarladık ve veritabanına kayıt yapıldığında o anki zamanı sütuna yazması için auto\_now\_add parametresini True olarak belirledik.

Meta sınıfında ise OnlineCounter sınıfıyla ilgili düzenlemeler yapıyoruz. django.contrib.admin uygulamasını kullananların yönetici panelinde uygulamamızı “Online Counter” olarak görmeleri için verbose\_name\_plural değişkenini oluşturuyoruz ve tablo adını db\_table değişkenine girdiğimiz değer ile online\_counter yapıyoruz.

Şimdi uygulama dizinimizde middleware.py adında bir dosya oluşturuyoruz ve şunları yazıyoruz:

```text
from onlinecounter.models import OnlineCounter
from datetime import datetime, time

class OnlineCounterMiddleware(object):
    def process_request(self, request):
        now_time = datetime.now().time()
        limit = time(now_time.hour, now_time.minute-5, now_time.second, now_time.microsecond)
        OnlineCounter.objects.filter(visited_time__lt=limit).delete()
        online, create = OnlineCounter.objects.get_or_create(ip=request.META["REMOTE_ADDR"])
        if request.user.is_authenticated():
            online.is_user = True
        else:
            online.is_user = False
        if not create:
            online.visited_time = now_time
        online.save()
        request.online = self

    def total(self):
        total = OnlineCounter.objects.all().count()
        return total

    def guest(self):
        guest = OnlineCounter.objects.filter(is_user=False).count()
        return guest

    def users(self):
        users = OnlineCounter.objects.filter(is_user=True).count()
        return users
```

models.py modülümüzü ve datetime modülünün datetime ve time fonksiyonlarını içe aktardık ve OnlineCounterMiddleware adında bir sınıf oluşturduk.

Django, her istek\(request\) yaptığında projeye ekli middleware sınıflarının process\_request\(\) methodunu çağırır. Bu da siteye giren herkesin sayacımız tarafından yakalanmasını sağlar. Php de olduğu gibi gerekli her php dosyasına kod eklemek zorunda kalmayız.

process\_request\(\) methodumuz çağırılacağına göre kodumuzu da bu method altına yazıyoruz.

Methoda request adında bir parametre atıyoruz. Bir kullanıcı istek yaptığında gerekli verileri Django, methodun bu parametresine atamaya çalışır.

now\_time değişkenine istek yapıldığı anda ki zamanı öğrenmek için gerekli kodu yazıyoruz. limit değişkenine ise bir ziyaretçinin kaç dakika işlem yapmadığında veritabanından atılacağını belirten zamanı ayarlıyoruz. Hemen altındaki satırda limit değişkenindeki zamanın veritabanındaki zamandan küçük olan ziyaretçileri veritabanından siliyoruz. filter\(\) methoduna girdiğimiz visited\_time değişkenine eklediğimiz \_\_lt ise “&lt; " operatörünün sihirli method adı verilen methodlardaki adını temsil ediyor. Bu sayede zaman karşılaştırılmasını yapıyoruz.

Bir sonraki satırda ise get\_or\_create\(\) methodu ile eğer request.META\["REMOTE\_ADDR"\] ile belirtilen ziyaretçi sitede ise verisini çek, yoksa veri oluştur diyoruz. Geriye dönüş olarak bir tuple döndürür. Tuple verisinin ilk elemanı OnlineCounter nesnemiz, ikincisi ise veri çekildiyse False veri oluşturduysa True olur. Bu verileri de online ve create değişkenine pay ettik.

request.user.is\_authenticated\(\) kodu bize kullanıcı girişi yapıldıysa if blokunu isletmemizi sağlar ve is\_user niteliğini True olarak ayarlarız. Giriş yapılmadıysa else bloku çalışır ve is\_user niteliğini False olarak ayarlarız. Sonraki if blokunda ise eğer veri oluşturulmayıp çağırıldıysa, ziyaretçinin ziyaret zamanını güncelleriz.

online.save\(\) ile değişiklikleri kaydediyoruz ve request e online adında bir nitelik oluşturup "self" ile sınıfımızı atıyoruz.

Oluşturduğumuz total\(\) methodunda bütün veriyi çağırıp nesnemizin count\(\) methoduyla kaç çevirimiçi kişi olduğunun verisi alıyoruz. guest\(\) ve users\(\) methodlarında ise OnlineCounter sınıfının is\_user niteliğini kullanarak misafir mi, üye mi diye filtreleme yapıyoruz ve sonucu döndürüyoruz. Middleware sınıfımızla işimiz bu kadar.

Şimdi proje dizinine dönüp views.py adında bir dosya oluşturun ve şu şekilde yazın:

```text
from django.shortcuts import HttpResponse

def main(request):
    online = """<p>Toplam: %s
    <p>Misafir: %s</p>
    <p>Kullanıcı: %s</p>"""%(request.online.total(), request.online.guest(), request.online.users())
    return HttpResponse(online)
```

urls.py dosyasını ise şöyle düzenleyin:

```text
from django.conf.urls import patterns, include, url

urlpatterns = patterns('',
    url(r'^onlinecounter/$', 'views.main'),

)
```

settings.py dosyasına ise aşağıdaki şekilde düzenleyin:

```text
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'visitors.db', # veritabanı ayarları.

MIDDLEWARE_CLASSES = (
    'onlinecounter.middleware.OnlineCounterMiddleware', # middleware sınıfını ekleyin.
)

INSTALLED_APPS = (

    'onlinecounter', # uygulamamızı ekliyoruz.
)
```

Uygulamamız hazır olduğuna göre veritabanı tablomuzu eklemek için

```text
python manage.py migrate
python manage.py makemigrations
```

komutunu veriyoruz ve ardından

```text
python manage.py runserver
```

komutunu verip sunucuyu başlatıyoruz. İnternet tarayıcınızın adres çubuğuna localhost:8000/onlinecounter/ yazarak sonucu görebilirsiniz. admin uygulaması ekliyse ve giriş yaptıysanız kullanıcı sayacında, aksi halde misafir sayacında gözükeceksiniz. middleware sınıfımız sayesinde de istediğiniz bir template dosyanıza

```text
{{ request.online.total }}
{{ request.online.guest }}
{{ request.online.users }}
```

şeklinde yazarak sonucu görebilirsiniz.

