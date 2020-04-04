# Pygame ile Oyun Programlama - Sprite 4. Bölüm Son

pygame.rect modülünde kullandığımız çarpışma kontrolünü pygame.sprite modülünün sağladıklarıyla daha etkili bir şekilde yönetebiliriz. Sprite nesneleri kullandığımız için bu araçları kullanırken Sprite ve gruplarını kullanmış olacağız.

Dikdörtgensel bir Sprite nesnesinin başka bir Sprite nesnesi ile çarpışmasını collide\_rect fonksiyonu ile kontrol edebiliriz.

```text
pygame.sprite.collide_rect(Sprite1, Sprite2)
```

Çarpışma varsa bu fonksiyon geriye True değeri döndürecektir. Aksi halde False değerini döndürecektir.

Dairesel Sprite nesnelerinin çarpışma kontrolünü ise collide\_circle\(\) fonksiyonu ile kontrol ediyoruz. Bu fonksiyonuda collide\_rect\(\) fonksiyonu ile aynı şekilde kullanıyoruz…

Bu iki fonksiyon ile bir Sprite ın olağan rect değerine göre kontrol sağlanıyor. Eğer bir Sprite nesnesinin çarpışma alanını genişletmek isterseniz collide\_rect\_ratio ile collide\_circle\_ratio sınıflarını kullanmalısınız…

```text
pygame.sprite.collide_rect_ratio(2.0)(Sprite1, Sprite2)
pygame.sprite.collide_circle_ratio(1.5)(Sprite1, Sprite2)
```

Bu sınıflar ilk verilen Sprite nesnesinin dikdörtgensel alanını ya da dairesel olarak çapını değiştirmenizi ve bu yeni değerlerle çarpışma kontrolünü yapmanızı sağlar. Bu sınıfların kullanımı size garip gelebilir, eğer gelirse şu yöntemi kullanarak daha anlaşılır kılabilirsiniz.

```text
collide_rect = pygame.sprite.collide_rect_ratio(2.0)
collide_rect(Sprite1, Sprite2)

collide_circle = pygame.sprite.collide_circle_ratio(1.5)
collide_circle(Sprite1, Sprite2)
```

Bu şekilde olmasını bu sınıfların **call** methodu sağlıyor. Nasıl **init** methodu sınıf başlatılınca çalışıyorsa, **call** methodu ise bu şekilde çalışıyor…

Burada verilen float değerlere bakarsak 2.0 değeri dikdörtgen alanın iki katına çıakrtır. Eğer -1.0 verirseniz aynı değerde kalır, 0.5 ise yarıya düşürür. Bu oranlara göre istediğiniz gibi Sprite nesnenize görünmez bir alan sağlıyabilirsiniz. Örnek olarak radar alanı verilebilir…

groupcollide fonksiyonu ise iki Sprite grubu nesnesinin çarpışmalarını kontrol ediyor. Ve gerekli parametre verilirse çarpışan grupların elemanlarını sildirebiliyorsunuz…

```text
pygame.sprite.groupcollide(grup1, grup2, True, False)
```

Parametre olarak iki grup girdik. Üçüncü parametre ile grup1 in elemanları çarpışınca silinecek, dördüncü parametrenin False olmasıyla grup2 nin elemanları silinmeyecektir. Bu fonksiyon ile Kamikaze Saldırısı adında bir oyun yapabilirsiniz.

Son fonksiyonumuz ise spritecollide\(\). Bu fonksiyon ile de bir Sprite ın, bir sprite grubuna temas etmesini kontrol ediyoruz.

```text
pygame.sprite.spritecollide(sprite, grup, True)
```

Bu kod ile sprite a temas eden grup üyeleri True parametresi sayesinde yok olacaktır. Eğer False verirseniz spritecollide\(\) fonksiyonu çarpışan Sprite nesnelerini liste olarak döndürecektir…

pygame.sprite modülünde bulunan bu fonksiyon ve sınıfları öğrenerek Sprite derslerinin sonuna gelmiş olduk.

