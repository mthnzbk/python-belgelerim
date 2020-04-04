# Python da Sonsuz Alt Kategori Sıralama

RSS okuyucu yazılımım için kategorileme özelliği eklemek istiyordum ve bunun sınırsız alt kategori alabilecek şekilde kodlamak istiyordum, ama yazdığım kod sadece bir alt kategoriyi gösterecek şekilde iç içe geçmiş iki for döngüsünden ibaretti. [Google](http://google.com.tr/) da arama yaptığımda karşıma %90 Php, %10 aspx ve diğer dillerde örnekler çıkıyordu ve çoğu kendine has fonksiyon gibi özellikler içerdiğinden kodu anlayamıyordum. Sorumu bir kaç Python forumunda dile getirdim ve çeşitli cevaplar aldım. Yalnız bunlar ya sql koduyla hallediliyordu ya da karışık bir kod kümesiydi. Yine bir konu da php bilen bir arkadaşın örnek kod istemesi üzerine araştırma yaptığımda en kısa ve anlaşılır bir php koduna ulaştım.

```text
function menu ($ust) {
    $git=mysql_query("SELECT * FROM menu WHERE ust_id='$ust'");
    echo "<ul>\n";
    while ($getir=mysql_fetch_array($git)) {
        echo "<li><a href=\"index.php?sayfa=".$getir[id]."\">".$getir[ism]."</a>";
        $ust_git=mysql_query("SELECT * FROM menu WHERE ust_id='$getir[id]'");
        $ust_say=mysql_num_rows($ust_git);
        if ($ust_say > 0) {
            menu ($getir[id]);
        }
        echo "</li>\n";
    }
    echo "</ul>\n";
```

Bu fonksiyonu Python'a uyarladım ve başarılı oldum.

```text
def kategoriSirala(x=0):
    execute("select * from categories where subcategory=?",(x,))
    items = cursor.fetchall()
    for item in items:
        print(item["id"], item["category_name"], item["subcategory"])
        kategoriSirala(item["id"])
```

Oldukça kısa bir kod. Hatta ilk defa php den çevirirken if bloğunu da eklemiştim, ama o kod olmadan da aynı işi görüyor. Tabii bu fonksiyon tek başına bir işe yaramaz. Veritabanında buna uygun bir tablo ve veri olması da gerekiyor. Mesela kategoriSirala\(\) fonksiyonunda bulunan execute\(\) ve cursor.fetchall\(\) fonksiyonları sqlite3 modülünün parçası şu şekilde yazarsak fonksiyon hata vermez.

```text
import sqlite3 as sql

connect = sql.connect("veri.db")
connect.row_factory = sql.Row

cursor = connect.cursor()
execute = cursor.execute
executemany = cursor.executemany
```

kategoriSirala\(\) fonksiyonunun başına bu kodu eklerseniz hata vermez. Şimdi gerekli kategori tablosunu ve verileri ekleyelim.

```text
create table categories (
id integer not null primary key autoincrement,
category_name text not null,
subcategory integer not null,
);
```

bu kodu tablo\_olustur değişkenine atayalım ve yukarıdaki koda göre tablomuzu oluşturup kategoriler ekleyelim.

```text
tablo_olustur = """create table categories (
id integer not null primary key autoincrement,
category_name text not null,
subcategory integer not null,
);"""

execute(tablo_olustur)
connect.commit()

kategoriler = (
    (1, "Programlama", 0),
    (2, "Haberler", 0),
    (3, "Forumlar", 0),
    (4, "Python", 1),
    (5, "Ruby", 1),
    (6, "Hürriyet", 2),
    (7, "istihza.com", 3),
)

executemany("insert into categories values(?, ?, ?)", kategoriler)
connect.commit()

kategoriSirala()
```

Kodu çalıştırdığınızda tablo oluşturulur kategoriler girilir ve kategoriSirala\(\) fonksiyonu çalışır. Eğer bu kodu yazarken hata yapmadıysam hatasız şekilde çalışacak ve çıktı şu şekilde olacaktır.

```text
1, "Programlama", 0
4, "Python", 1
5, "Ruby", 1
2, "Haberler", 0
6, "Hürriyet", 2
3, "Forumlar", 0
7, "istihza.com", 3
```

Sıralama doğru şekilde sıralandı... Yapacağınız uygulamaya göre bu fonksiyonu özelleştirebilirsiniz. Kodlar hata verirse yazdığınız kod ile hata çıktısını yorum olarak paylaşırsanız elimden geldiği kadarıyla yardımcı olurum. Bu arada bu konuyu Türkçe olarak bir tek ben yazmış oldum. En azından google aramalarında Python ile ilgili hiç bir sonuç çıkmadı.

Ufak bir bilgi daha ekleyeyim: Eğer bir konuyu google da arayınca çok çöplük konu varsa [bing](http://bing.com/) ve [yandex](http://yandex.com.tr/) sitelerini deneyin. Daha temiz sonuçlar karşınıza çıkacaktır.

