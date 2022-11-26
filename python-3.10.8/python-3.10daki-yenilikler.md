# Python 3.10'daki Yenilikler

Resmi içeriğe gitmek için [tıklayınız.](https://docs.python.org/3.10/whatsnew/3.10.html)



Sürüm:

3.10.8

Tarih:

22 Ekim 2022

Editör:

Paul Galindo Salgado

Bu makale, 3.9'a kıyasla Python 3.10'daki yeni özellikleri açıklamaktadır. Python 3.10, 4 Ekim 2021'de yayınlandı. Tüm ayrıntılar için [değişiklik günlüğüne](https://docs.python.org/3.10/whatsnew/changelog.html#changelog) bakın .

### Özet – Sürümün öne çıkan özellikleri&#x20;

Yeni sözdizimi özellikleri:

* [**PEP 634**](https://www.python.org/dev/peps/pep-0634) , Yapısal Model Eşleştirme: Spesifikasyon
* [**PEP 635**](https://www.python.org/dev/peps/pep-0635) , Yapısal Örüntü Eşleştirme: Motivasyon ve Gerekçe
* [**PEP 636**](https://www.python.org/dev/peps/pep-0636) , Yapısal Model Eşleştirme: Öğretici
* [bpo-12782](https://bugs.python.org/issue?@action=redirect\&bpo=12782) , Parantez içindeki bağlam yöneticilerine artık resmi olarak izin veriliyor.

Standart kitaplıktaki yeni özellikler:

* [**PEP 618**](https://www.python.org/dev/peps/pep-0618) , Zip'e Opsiyonel Uzunluk Kontrolü Ekleyin.

Tercüman iyileştirmeleri:

* [**PEP 626**](https://www.python.org/dev/peps/pep-0626) , Hata ayıklama ve diğer araçlar için kesin satır numaraları.

Yeni yazma özellikleri:

* [**PEP 604**](https://www.python.org/dev/peps/pep-0604) , Birleşim türlerinin X olarak yazılmasına izin ver | Y
* [**PEP 613**](https://www.python.org/dev/peps/pep-0613) , Açık Tür Takma Adları
* [**PEP 612**](https://www.python.org/dev/peps/pep-0612) , Parametre Spesifikasyon Değişkenleri

Önemli kullanımdan kaldırmalar, kaldırmalar veya kısıtlamalar:

* [**PEP 644**](https://www.python.org/dev/peps/pep-0644) , OpenSSL 1.1.1 veya daha yenisini gerektirir
* [**PEP 632**](https://www.python.org/dev/peps/pep-0632) , Distutils modülünü kullanımdan kaldır.
* [**PEP 623**](https://www.python.org/dev/peps/pep-0623) , PyUnicodeObject içindeki wstr üyesinin kaldırılması için onaylayın ve hazırlanın.
* [**PEP 624**](https://www.python.org/dev/peps/pep-0624) , Py\_UNICODE kodlayıcı API'lerini kaldır
* [**PEP 597**](https://www.python.org/dev/peps/pep-0597) , İsteğe bağlı Kodlama Uyarısı ekleyin

### Yeni Özellikler&#x20;

#### Parantez içindeki bağlam yöneticileri&#x20;

Bağlam yöneticilerinde birden çok satırda devam etmek için parantez içine alma artık desteklenmektedir. Bu, daha önce import ifadelerinde mümkün olduğu gibi, uzun bir bağlam yöneticisi koleksiyonunu birden çok satırda biçimlendirmeye izin verir. Örneğin, tüm bu örnekler artık geçerlidir:

```
with (CtxManager() as example):
    ...

with (
    CtxManager1(),
    CtxManager2()
):
    ...

with (CtxManager1() as example,
      CtxManager2()):
    ...

with (CtxManager1(),
      CtxManager2() as example):
    ...

with (
    CtxManager1() as example1,
    CtxManager2() as example2
):
    ...
```

ekteki grubun sonunda bir virgül kullanmak da mümkündür:

```
with (
    CtxManager1() as example1,
    CtxManager2() as example2,
    CtxManager3() as example3,
):
    ...
```

Bu yeni sözdizimi, yeni ayrıştırıcının LL(1) olmayan kapasitelerini kullanır. KontrolDaha fazla ayrıntı için[ **PEP 617 .**](https://www.python.org/dev/peps/pep-0617)

(Guido van Rossum, Pablo Galindo ve Lysandros [Nikolaou tarafından katkıda bulunulmuştur ](https://bugs.python.org/issue?@action=redirect\&bpo=40334)[.](https://bugs.python.org/issue?@action=redirect\&bpo=12782) )

#### Daha iyi hata mesajları&#x20;

**SyntaxErrors**

Kapatılmamış parantezler veya parantezler içeren kodu ayrıştırırken yorumlayıcı, ayrıştırma veya yanlış bir konumu işaret _ederken SyntaxError: beklenmeyen EOF'yi görüntülemek yerine artık kapatılmamış parantez parantezinin konumunu içerir._ Örneğin, aşağıdaki kodu göz önünde bulundurun (kapatılmamış '{' işaretine dikkat edin):

```
expected = {9: 1, 18: 2, 19: 2, 27: 3, 28: 3, 29: 3, 36: 4, 37: 4,
            38: 4, 39: 4, 45: 5, 46: 5, 47: 5, 48: 5, 49: 5, 54: 6,
some_other_code = foo()
```

Yorumlayıcının önceki sürümleri, sözdizimi hatasının yeri olarak kafa karıştırıcı yerler bildirdi:

```
File "example.py", line 3
    some_other_code = foo()
                    ^
SyntaxError: invalid syntax
```

ancak Python 3.10'da daha bilgilendirici bir hata yayılıyor:

```
File "example.py", line 1
    expected = {9: 1, 18: 2, 19: 2, 27: 3, 28: 3, 29: 3, 36: 4, 37: 4,
               ^
SyntaxError: '{' was never closed
```

Benzer şekilde, kapatılmamış dize değişmezlerini (tek ve üçlü alıntı) içeren hatalar artık EOF/EOL bildirmek yerine dizenin başlangıcına işaret ediyor.

Bu iyileştirmeler, PyPy yorumlayıcısındaki önceki çalışmalardan esinlenmiştir.

(Bpo-42864'te Pablo Galindo ve [bpo ](https://bugs.python.org/issue?@action=redirect\&bpo=42864)[-40176'da](https://bugs.python.org/issue?@action=redirect\&bpo=40176) Batuhan Taşkaya tarafından katkıda bulunulmuştur .)

[`SyntaxError`](https://docs.python.org/3.10/library/exceptions.html#SyntaxError)yorumlayıcı tarafından oluşturulan istisnalar, artık yalnızca sorunun tespit edildiği yer yerine, sözdizimi hatasının kendisini oluşturan ifadenin tüm hata aralığını vurgulayacaktır. Bu şekilde, görüntülemek yerine (Python 3.10'dan önce):

\>>>

```
>>> foo(x, z for z in range(10), t, w)
  File "<stdin>", line 1
    foo(x, z for z in range(10), t, w)
           ^
SyntaxError: Generator expression must be parenthesized
```

şimdi Python 3.10 istisnayı şu şekilde gösterecek:

\>>>

```
>>> foo(x, z for z in range(10), t, w)
  File "<stdin>", line 1
    foo(x, z for z in range(10), t, w)
           ^^^^^^^^^^^^^^^^^^^^
SyntaxError: Generator expression must be parenthesized
```

Bu iyileştirme, [bpo-43914'te](https://bugs.python.org/issue?@action=redirect\&bpo=43914) Pablo Galindo tarafından sağlanmıştır .

İstisnalar için önemli miktarda yeni özel mesaj [`SyntaxError`](https://docs.python.org/3.10/library/exceptions.html#SyntaxError)dahil edilmiştir. En dikkat çekici olanlardan bazıları aşağıdaki gibidir:

*   `:`Bloklardan önce eksik :

    > \>>>
    >
    > ```
    > >>> if rocket.position > event_horizon
    >   File "<stdin>", line 1
    >     if rocket.position > event_horizon
    >                                       ^
    > SyntaxError: expected ':'
    > ```
    >
    > [( Bpo-42997'de](https://bugs.python.org/issue?@action=redirect\&bpo=42997) Pablo Galindo tarafından katkıda bulunulmuştur .)
*   Kavrama hedeflerindeki parantezsiz demetler:

    > \>>>
    >
    > ```
    > >>> {x,y for x,y in zip('abcd', '1234')}
    >   File "<stdin>", line 1
    >     {x,y for x,y in zip('abcd', '1234')}
    >      ^
    > SyntaxError: did you forget parentheses around the comprehension target?
    > ```
    >
    > [( Bpo-43017'de](https://bugs.python.org/issue?@action=redirect\&bpo=43017) Pablo Galindo tarafından katkıda bulunulmuştur .)
*   Koleksiyon değişmezlerinde ve ifadeler arasında eksik virgül:

    > \>>>
    >
    > ```
    > >>> items = {
    > ... x: 1,
    > ... y: 2
    > ... z: 3,
    >   File "<stdin>", line 3
    >     y: 2
    >        ^
    > SyntaxError: invalid syntax. Perhaps you forgot a comma?
    > ```
    >
    > [( bpo-43822'de](https://bugs.python.org/issue?@action=redirect\&bpo=43822) Pablo Galindo tarafından katkıda bulunulmuştur .)
*   Parantezsiz çoklu İstisna türleri:

    > \>>>
    >
    > ```
    > >>> try:
    > ...     build_dyson_sphere()
    > ... except NotEnoughScienceError, NotEnoughResourcesError:
    >   File "<stdin>", line 3
    >     except NotEnoughScienceError, NotEnoughResourcesError:
    >            ^
    > SyntaxError: multiple exception types must be parenthesized
    > ```
    >
    > [( Bpo-43149'da](https://bugs.python.org/issue?@action=redirect\&bpo=43149) Pablo Galindo tarafından katkıda bulunulmuştur .)
*   Sözlük değişmezlerinde eksik `:`ve değerler:

    > \>>>
    >
    > ```
    > >>> values = {
    > ... x: 1,
    > ... y: 2,
    > ... z:
    > ... }
    >   File "<stdin>", line 4
    >     z:
    >      ^
    > SyntaxError: expression expected after dictionary key and ':'
    >
    > >>> values = {x:1, y:2, z w:3}
    >   File "<stdin>", line 1
    >     values = {x:1, y:2, z w:3}
    >                         ^
    > SyntaxError: ':' expected after dictionary key
    > ```
    >
    > [( Bpo-43823'te](https://bugs.python.org/issue?@action=redirect\&bpo=43823) Pablo Galindo tarafından katkıda bulunulmuştur .)
*   `try`bloklar `except`veya `finally`bloklar:

    > \>>>
    >
    > ```
    > >>> try:
    > ...     x = 2
    > ... something = 3
    >   File "<stdin>", line 3
    >     something  = 3
    >     ^^^^^^^^^
    > SyntaxError: expected 'except' or 'finally' block
    > ```
    >
    > [( Bpo-44305'te](https://bugs.python.org/issue?@action=redirect\&bpo=44305) Pablo Galindo tarafından katkıda bulunulmuştur .)
*   `=`Karşılaştırmalar yerine kullanımı `==`:

    > \>>>
    >
    > ```
    > >>> if rocket.position = event_horizon:
    >   File "<stdin>", line 1
    >     if rocket.position = event_horizon:
    >                        ^
    > SyntaxError: cannot assign to attribute here. Maybe you meant '==' instead of '='?
    > ```
    >
    > [( Bpo-43797'de](https://bugs.python.org/issue?@action=redirect\&bpo=43797) Pablo Galindo tarafından katkıda bulunulmuştur .)
*   `*`f-stringlerde kullanımı :

    > \>>>
    >
    > ```
    > >>> f"Black holes {*all_black_holes} and revelations"
    >   File "<stdin>", line 1
    >     (*all_black_holes)
    >      ^
    > SyntaxError: f-string: cannot use starred expression here
    > ```
    >
    > [( Bpo-41064'te](https://bugs.python.org/issue?@action=redirect\&bpo=41064) Pablo Galindo tarafından katkıda bulunulmuştur .)

**IndentationErrors**

Artık birçok [`IndentationError`](https://docs.python.org/3.10/library/exceptions.html#IndentationError)istisna, ifadenin konumu da dahil olmak üzere ne tür bir bloğun girinti beklediğiyle ilgili daha fazla içeriğe sahip:

\>>>

```
>>> def foo():
...    if lel:
...    x = 2
  File "<stdin>", line 3
    x = 2
    ^
IndentationError: expected an indented block after 'if' statement in line 2
```

**AttributeErrors**

yazdırırken [`AttributeError`](https://docs.python.org/3.10/library/exceptions.html#AttributeError), `PyErr_Display()`istisnanın ortaya çıktığı nesnede benzer öznitelik adlarına ilişkin öneriler sunar:

\>>>

```
>>> collections.namedtoplo
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'collections' has no attribute 'namedtoplo'. Did you mean: namedtuple?
```

[( Bpo-38530'da](https://bugs.python.org/issue?@action=redirect\&bpo=38530) Pablo Galindo tarafından katkıda bulunulmuştur .)

> Uyarı
>
> &#x20;
>
> `PyErr_Display()`Başka bir özel hata görüntüleme işlevi kullanıldığında meydana gelebilecek hatayı görüntülemek için çağrılmazsa bunun işe yaramayacağına dikkat edin . Bu, IPython gibi bazı REPL'lerde yaygın bir senaryodur.

**NameErrors**

[`NameError`](https://docs.python.org/3.10/library/exceptions.html#NameError)Yorumlayıcı tarafından yazdırıldığında `PyErr_Display()` , istisnanın ortaya çıktığı fonksiyonda benzer değişken isimleri önerileri sunacaktır:

\>>>

```
>>> schwarzschild_black_hole = None
>>> schwarschild_black_hole
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'schwarschild_black_hole' is not defined. Did you mean: schwarzschild_black_hole?
```

[( Bpo-38530'da](https://bugs.python.org/issue?@action=redirect\&bpo=38530) Pablo Galindo tarafından katkıda bulunulmuştur .)

> Uyarı
>
> &#x20;
>
> `PyErr_Display()`Başka bir özel hata görüntüleme işlevi kullanıldığında meydana gelebilecek hatayı görüntülemek için çağrılmazsa bunun işe yaramayacağına dikkat edin . Bu, IPython gibi bazı REPL'lerde yaygın bir senaryodur.

#### PEP 626: Hata ayıklama ve diğer araçlar için kesin satır numaraları&#x20;

PEP 626, hata ayıklama, profil oluşturma ve kapsama araçları için daha kesin ve güvenilir satır numaraları sağlar. Doğru satır numarasına sahip izleme olayları, yürütülen tüm kod satırları için ve yalnızca yürütülen kod satırları için oluşturulur.

`f_lineno`Çerçeve nesnelerinin özniteliği her zaman beklenen satır numarasını içerecektir .

Kod `co_lnotab`nesnelerinin özniteliği kullanımdan kaldırılmıştır ve 3.12'de kaldırılacaktır. Ofsetten satır numarasına dönüştürülmesi gereken kod, bunun yerine yeni `co_lines()`yöntemi kullanmalıdır.

#### PEP 634: Yapısal Model Eşleştirme&#x20;

Yapısal kalıp eşleştirme, bir _eşleşme ifadesi_ ve ilişkili eylemlerle kalıpların _vaka ifadeleri şeklinde eklendi._ Modeller, dizilerden, eşlemelerden, ilkel veri türlerinden ve sınıf örneklerinden oluşur. Model eşleştirme, programların karmaşık veri türlerinden bilgi çıkarmasına, veri yapısında dallara ayrılmasına ve farklı veri biçimlerine dayalı olarak belirli eylemler uygulamasına olanak tanır.

**Sözdizimi ve işlemler**&#x20;

Kalıp eşleştirmenin genel sözdizimi şöyledir:

```
match subject:
    case <pattern_1>:
        <action_1>
    case <pattern_2>:
        <action_2>
    case <pattern_3>:
        <action_3>
    case _:
        <action_wildcard>
```

Bir eşleşme ifadesi bir ifade alır ve değerini bir veya daha fazla vaka bloğu olarak verilen ardışık kalıplarla karşılaştırır. Özellikle, desen eşleştirme şu şekilde çalışır:

> 1. tür ve şekle sahip verileri kullanma ( `subject`)
> 2. açıklamadaki değerlendirme `subject`\_`match`
> 3. `case`Bir eşleşme onaylanana kadar konuyu yukarıdan aşağıya bir ifadede her bir kalıpla karşılaştırmak .
> 4. teyit edilen eşleşmenin modeliyle ilişkili eylemi yürütmek
> 5. Tam bir eşleşme onaylanmazsa, son durum, `_`sağlanırsa bir joker karakter eşleşen durum olarak kullanılacaktır. Tam eşleşme onaylanmazsa ve joker karakter durumu yoksa, eşleşme bloğunun tamamı işlemsizdir.

**Bildirimsel yaklaşım**&#x20;

Okuyucular, bir özneyi (veri nesnesini) C, Java veya JavaScript'te (ve diğer birçok dilde) bulunan switch ifadesiyle bir hazır bilgiyle (kalıp) eşleştirmenin basit örneği aracılığıyla kalıp eşleştirmenin farkında olabilirler. Genellikle switch ifadesi, bir nesnenin/ifadenin değişmez değerler içeren vaka ifadeleriyle karşılaştırılması için kullanılır.

Scala ve Elixir gibi dillerde desen eşleştirmenin daha güçlü örnekleri bulunabilir. Yapısal örüntü eşleştirme ile, yaklaşım "bildirimseldir" ve verilerin eşleşmesi için koşulları (kalıpları) açıkça belirtir.

Yapısal model eşleştirmeye benzer bir şeyi başarmak için iç içe "if" ifadelerini kullanan "zorunlu" bir dizi talimat kullanılabilirken, "bildirimsel" yaklaşımdan daha az açıktır. Bunun yerine, "bildirimsel" yaklaşım, bir eşleşme için karşılanması gereken koşulları belirtir ve açık kalıpları aracılığıyla daha okunabilirdir. Yapısal örüntü eşleştirme, bir vaka ifadesinde bir değişkeni bir hazır bilgiyle karşılaştırarak en basit biçiminde kullanılabilirken, Python için gerçek değeri, öznenin türünü ve şeklini ele alışında yatar.

**Basit model: bir değişmezle eşleştir**&#x20;

Bu örneğe en basit haliyle kalıp eşleştirme olarak bakalım: bir değer, özne, birkaç değişmez değerle eşleştiriliyor, kalıplar. Aşağıdaki örnekte `status`, eşleşme ifadesinin konusudur. Kalıplar, değişmez değerlerin istek durum kodlarını temsil ettiği durum ifadelerinin her biridir. Vakayla ilişkili eylem, bir eşleşmeden sonra yürütülür:

```
def http_error(status):
    match status:
        case 400:
            return "Bad request"
        case 404:
            return "Not found"
        case 418:
            return "I'm a teapot"
        case _:
            return "Something's wrong with the internet"
```

Yukarıdaki fonksiyon a `status`418 ile geçilirse, “I'm a teapot” döndürülür. Yukarıdaki işlev `status`500 üzerinden geçirilirse, case ifadesi `_`joker karakter olarak eşleşir ve “İnternet'te bir sorun var” döndürülür. Son bloğa dikkat edin: değişken adı, bir _joker karakter_`_` gibi davranır ve konunun her zaman eşleşmesini sağlar. kullanımı isteğe bağlıdır.`_`

`|`(“veya”) kullanarak birkaç değişmez değeri tek bir desende birleştirebilirsiniz :

```
case 401 | 403 | 404:
    return "Not allowed"
```

**Joker karakter olmadan davranış**&#x20;

Yukarıdaki örneği son durum bloğunu kaldırarak değiştirirsek, örnek şöyle olur:

```
def http_error(status):
    match status:
        case 400:
            return "Bad request"
        case 404:
            return "Not found"
        case 418:
            return "I'm a teapot"
```

Case deyimi kullanılmadan `_`, bir eşleşme olmayabilir. Eşleşme yoksa, davranış işlemsizdir. Örneğin, `status`500 üzerinden geçilirse, işlem yapılmaz.

**Değişmez ve değişkenli kalıplar**&#x20;

Kalıplar, paket açma atamaları gibi görünebilir ve değişkenleri bağlamak için bir kalıp kullanılabilir. Bu örnekte, bir veri noktası x-koordinatına ve y-koordinatına göre açılabilir:

```
# point is an (x, y) tuple
match point:
    case (0, 0):
        print("Origin")
    case (0, y):
        print(f"Y={y}")
    case (x, 0):
        print(f"X={x}")
    case (x, y):
        print(f"X={x}, Y={y}")
    case _:
        raise ValueError("Not a point")
```

İlk kalıbın iki değişmez değeri vardır ve yukarıda gösterilen değişmez kalıbın bir uzantısı olarak düşünülebilir. Sonraki iki kalıp, bir hazır bilgi ve bir değişkeni birleştirir ve değişken, özneden ( ) bir değer _bağlar_ . Dördüncü model, kavramsal olarak paket açma atamasına benzer kılan iki değeri yakalar .`(0, 0)point(x, y) = point`

**Modeller ve sınıflar**&#x20;

Verilerinizi yapılandırmak için sınıfları kullanıyorsanız, bir yapıcıya benzeyen bir bağımsız değişken listesinin ardından sınıf adını bir kalıp olarak kullanabilirsiniz. Bu model, sınıf niteliklerini değişkenler halinde yakalama yeteneğine sahiptir:

```
class Point:
    x: int
    y: int

def location(point):
    match point:
        case Point(x=0, y=0):
            print("Origin is the point's location.")
        case Point(x=0, y=y):
            print(f"Y={y} and the point is on the y-axis.")
        case Point(x=x, y=0):
            print(f"X={x} and the point is on the x-axis.")
        case Point():
            print("The point is located somewhere else on the plane.")
        case _:
            print("Not a point")
```

**Konumsal parametrelere sahip modeller**&#x20;

Konumsal parametreleri, öznitelikleri için bir sıralama sağlayan bazı yerleşik sınıflarla kullanabilirsiniz (örn. veri sınıfları). `__match_args__`Ayrıca , sınıflarınızda özel niteliği ayarlayarak, kalıplardaki nitelikler için belirli bir konum tanımlayabilirsiniz . (“x”, “y”) olarak ayarlanmışsa, aşağıdaki kalıpların tümü eşdeğerdir (ve tümü `y`özelliği `var`değişkene bağlar):

```
Point(1, var)
Point(1, y=var)
Point(x=1, y=var)
Point(y=var, x=1)
```

**İç içe desenler**&#x20;

Desenler keyfi olarak iç içe yerleştirilebilir. Örneğin, verilerimiz kısa bir puan listesiyse, şu şekilde eşleştirilebilir:

```
match points:
    case []:
        print("No points in the list.")
    case [Point(0, 0)]:
        print("The origin is the only point in the list.")
    case [Point(x, y)]:
        print(f"A single point {x}, {y} is in the list.")
    case [Point(0, y1), Point(0, y2)]:
        print(f"Two points on the Y axis at {y1}, {y2} are in the list.")
    case _:
        print("Something else is found in the list.")
```

**Karmaşık desenler ve joker karakter**&#x20;

Bu noktaya kadar, `_`son durum deyiminde örnekler tek başına kullanılmıştır. Bir joker karakter, gibi daha karmaşık desenlerde kullanılabilir . Örneğin:`('error', code, _)`

```
match test_variable:
    case ('warning', code, 40):
        print("A warning has been received.")
    case ('error', code, _):
        print(f"An error {code} occurred.")
```

Yukarıdaki durumda, `test_variable`('hata', kod, 100) ve ('hata', kod, 800) için eşleşir.

**Guard**

`if`Bir kalıba “koruyucu” olarak bilinen bir cümle ekleyebiliriz . Koruma yanlışsa, `match`sonraki vaka bloğunu denemeye devam eder. Değer yakalamanın koruma değerlendirilmeden önce gerçekleştiğini unutmayın:

```
match point:
    case Point(x, y) if x == y:
        print(f"The point is located on the diagonal Y=X at {x}.")
    case Point(x, y):
        print(f"Point is not on the diagonal.")
```

**Diğer Temel Özellikler**&#x20;

Diğer birkaç önemli özellik:

* Paket açma atamaları gibi, demet ve liste desenleri de tamamen aynı anlama sahiptir ve aslında rastgele dizilerle eşleşir. Teknik olarak, konu bir dizi olmalıdır. Bu nedenle, önemli bir istisna, kalıpların yineleyicilerle eşleşmemesidir. Ayrıca, yaygın bir hatayı önlemek için dizi desenleri dizelerle eşleşmez.
* Sıra kalıpları joker karakterleri destekler: ve paket açma atamalarında joker karakterlere benzer şekilde çalışır. Sonraki ad da olabilir , bu nedenle kalan öğeleri bağlamadan en az iki öğeden oluşan bir diziyle eşleşir.`[x, y, *rest](x, y, *rest)*_(x, y, *_)`
* Eşleme desenleri: bir dikteden ve değerlerini yakalar . Sıralama kalıplarının aksine, fazladan tuşlar yok sayılır. Bir joker karakter de desteklenir. (Ama gereksiz olurdu, bu yüzden izin verilmez.)`{"bandwidth": b, "latency": l}"bandwidth""latency"**rest**_`
*   Alt modeller, anahtar kelime kullanılarak yakalanabilir `as`:

    ```
    case (Point(x1, y1), Point(x2, y2) as p2): ...
    ```

    Bu, x1, y1, x2, y2'yi yan tümce olmadan beklediğiniz gibi `as`ve p2'yi konunun ikinci maddesinin tamamına bağlar.
* Çoğu değişmez eşitlikle karşılaştırılır. Ancak, singletons `True`, `False`ve `None`kimliğe göre karşılaştırılır.
*   Adlandırılmış sabitler desenlerde kullanılabilir. Bu adlandırılmış sabitler, sabitin bir yakalama değişkeni olarak yorumlanmasını önlemek için noktalı adlar olmalıdır:

    ```
    from enum import Enum
    class Color(Enum):
        RED = 0
        GREEN = 1
        BLUE = 2

    match color:
        case Color.RED:
            print("I see red!")
        case Color.GREEN:
            print("Grass is green")
        case Color.BLUE:
            print("I'm feeling the blues :(")
    ```

Tüm özellikler için bkz.[**PEP 634**](https://www.python.org/dev/peps/pep-0634) . Motivasyon ve mantık ortada[**PEP 635**](https://www.python.org/dev/peps/pep-0635) ve daha uzun bir eğitim[**PEP 636**](https://www.python.org/dev/peps/pep-0636) .

#### İsteğe bağlı `EncodingWarning`ve `encoding="locale"`seçenek&#x20;

`TextIOWrapper`ve öğesinin varsayılan kodlaması [`open()`](https://docs.python.org/3.10/library/functions.html#open)platforma ve yerel ayara bağlıdır. UTF-8 çoğu Unix platformunda kullanıldığından, `encoding`UTF-8 dosyalarını açarken seçeneğin atlanması (örn. JSON, YAML, TOML, Markdown) çok yaygın bir hatadır. Örneğin:

```
# BUG: "rb" mode or encoding="utf-8" should be used.
with open("data.json") as f:
    data = json.load(f)
```

Bu tür bir hatayı bulmak için isteğe bağlı `EncodingWarning`bir eklenir. [`sys.flags.warn_default_encoding`](https://docs.python.org/3.10/library/sys.html#sys.flags) Doğru olduğunda ve yerel ayara özgü varsayılan kodlama kullanıldığında yayılır .

`-X warn_default_encoding`seçenek ve[`PYTHONWARNDEFAULTENCODING`](https://docs.python.org/3.10/using/cmdline.html#envvar-PYTHONWARNDEFAULTENCODING) uyarıyı etkinleştirmek için eklenir.

Daha fazla bilgi için [Metin Kodlaması konusuna bakın.](https://docs.python.org/3.10/library/io.html#io-text-encoding)

### Tip İpuçları ile İlgili Yeni Özellikler&#x20;

Bu bölüm, aşağıdakileri etkileyen önemli değişiklikleri kapsar:[**PEP 484**](https://www.python.org/dev/peps/pep-0484) tipi ipuçları ve[`typing`](https://docs.python.org/3.10/library/typing.html#module-typing)modül.

#### PEP 604: Yeni Tip Birleştirme Operatörü&#x20;

Sözdizimini etkinleştiren yeni bir tür birleştirme operatörü tanıtıldı . Bu , özellikle tür ipuçlarında kullanmak yerine 'X tipi veya Y tipi' ifade etmenin daha temiz bir yolunu sağlar .`X | Y`[`typing.Union`](https://docs.python.org/3.10/library/typing.html#typing.Union)

Python'un önceki sürümlerinde, birden çok türden bağımsız değişkenleri kabul eden işlevler için bir tür ipucu uygulamak için [`typing.Union`](https://docs.python.org/3.10/library/typing.html#typing.Union)kullanıldı:

```
def square(number: Union[int, float]) -> Union[int, float]:
    return number ** 2
```

Tip ipuçları artık daha özlü bir şekilde yazılabilir:

```
def square(number: int | float) -> int | float:
    return number ** 2
```

[`isinstance()`](https://docs.python.org/3.10/library/functions.html#isinstance) Bu yeni sözdizimi, and öğesinin ikinci argümanı olarak da kabul edilir [`issubclass()`](https://docs.python.org/3.10/library/functions.html#issubclass):

\>>>

```
>>> isinstance(1, int | str)
True
```

[Birlik Tipine](https://docs.python.org/3.10/library/stdtypes.html#types-union) bakın veDaha fazla ayrıntı için[ **PEP 604 .**](https://www.python.org/dev/peps/pep-0604)

(bpo-41428'de Maggie Moss ve Philippe Prados tarafından, [bpo-44490'da ](https://bugs.python.org/issue?@action=redirect\&bpo=41428)[Yurii](https://bugs.python.org/issue?@action=redirect\&bpo=44490) Karabas ve Serhiy Storchaka tarafından ilaveler yapılmıştır .)

#### PEP 612: Parametre Spesifikasyonu Değişkenleri&#x20;

Statik tür denetleyicilerine sağlanan bilgileri iyileştirmek için iki yeni seçenek Modüle[ **PEP 484'ler**](https://www.python.org/dev/peps/pep-0484) eklendi`Callable`.[`typing`](https://docs.python.org/3.10/library/typing.html#module-typing)

Birincisi parametre spesifikasyon değişkenidir. Bir çağrılabilir öğenin parametre türlerini başka bir çağrılabilir öğeye iletmek için kullanılırlar - genellikle daha yüksek dereceli işlevlerde ve dekoratörlerde bulunan bir model. Kullanım örnekleri şurada bulunabilir [`typing.ParamSpec`](https://docs.python.org/3.10/library/typing.html#typing.ParamSpec). Önceden, parametre türlerinin açıklama bağımlılığını bu kadar kesin bir şekilde yazmanın kolay bir yolu yoktu.

İkinci seçenek yeni `Concatenate`operatördür. Başka bir çağrılabilir parametrenin parametrelerini ekleyen veya kaldıran daha yüksek dereceli bir çağrılabilir not eklemek için parametre belirtim değişkenleriyle birlikte kullanılır. Kullanım örnekleri şurada bulunabilir [`typing.Concatenate`](https://docs.python.org/3.10/library/typing.html#typing.Concatenate).

Bkz [`typing.Callable`](https://docs.python.org/3.10/library/typing.html#typing.Callable). , [`typing.ParamSpec`](https://docs.python.org/3.10/library/typing.html#typing.ParamSpec), [`typing.Concatenate`](https://docs.python.org/3.10/library/typing.html#typing.Concatenate), [`typing.ParamSpecArgs`](https://docs.python.org/3.10/library/typing.html#typing.ParamSpecArgs), [`typing.ParamSpecKwargs`](https://docs.python.org/3.10/library/typing.html#typing.ParamSpecKwargs)veDaha fazla ayrıntı için[ **PEP 612 .**](https://www.python.org/dev/peps/pep-0612)

(Bpo-41559'da Ken Jin tarafından, [bpo-43783'te](https://bugs.python.org/issue?@action=redirect\&bpo=41559) Jelle Zijlstra tarafından küçük geliştirmelerle [katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=43783) . Mark Mendoza tarafından yazılan PEP.)

#### PEP 613: TypeAlias&#x20;

[**PEP 484**](https://www.python.org/dev/peps/pep-0484) , yalnızca üst düzey açıklamasız atamalar olmalarını gerektiren tür takma adları kavramını tanıttı. Bu basitlik, özellikle ileriye dönük başvurular veya geçersiz türler söz konusu olduğunda, tür denetleyicilerinin tür takma adları ile sıradan atamaları ayırt etmesini bazen zorlaştırdı. Karşılaştırmak:

```
StrCache = 'Cache[str]'  # a type alias
LOG_PREFIX = 'LOG[DEBUG]'  # a module constant
```

Artık modülün, tür takma adlarını daha açık bir şekilde bildirmenizi sağlayan [`typing`](https://docs.python.org/3.10/library/typing.html#module-typing)özel bir değeri vardır:`TypeAlias`

```
StrCache: TypeAlias = 'Cache[str]'  # a type alias
LOG_PREFIX = 'LOG[DEBUG]'  # a module constant
```

GörmekDaha fazla ayrıntı için[ **PEP 613 .**](https://www.python.org/dev/peps/pep-0613)

[( Bpo-41923'te](https://bugs.python.org/issue?@action=redirect\&bpo=41923) Mikhail Golubev tarafından katkıda bulunulmuştur .)

#### PEP 647: Kullanıcı Tanımlı Tip Korumaları&#x20;

`TypeGuard`[`typing`](https://docs.python.org/3.10/library/typing.html#module-typing)tip koruma işlevlerine açıklama eklemek ve tip daraltma sırasında statik tip denetleyicilerine sağlanan bilgileri iyileştirmek için modüle eklenmiştir . Daha fazla bilgi için lütfen `TypeGuard`belgelerine bakın ve[**PEP 647**](https://www.python.org/dev/peps/pep-0647) .

[( Bpo-43766'da](https://bugs.python.org/issue?@action=redirect\&bpo=43766) Ken Jin ve Guido van Rossum tarafından katkıda bulunulmuştur . Eric Traut tarafından yazılan PEP.)

### Diğer Dil Değişiklikleri&#x20;

* Türün , nüfus sayımı olarak da bilinen belirli bir tamsayının ikili genişlemesindekilerin sayısını döndüren [`int`](https://docs.python.org/3.10/library/functions.html#int)yeni bir yöntemi vardır . [(Bpo -29882'de](https://bugs.python.org/issue?@action=redirect\&bpo=29882)[`int.bit_count()`](https://docs.python.org/3.10/library/stdtypes.html#int.bit\_count) Niklas Fiekas tarafından katkıda bulunulmuştur .)
* tarafından döndürülen görünümler ve şimdi tümü, orijinal sözlüğü saran bir nesne veren bir [`dict.keys()`](https://docs.python.org/3.10/library/stdtypes.html#dict.keys)özniteliğe sahiptir . [( Bpo-40890'da](https://bugs.python.org/issue?@action=redirect\&bpo=40890) Dennis Sweeney tarafından katkıda bulunulmuştur .)[`dict.values()`](https://docs.python.org/3.10/library/stdtypes.html#dict.values)[`dict.items()`](https://docs.python.org/3.10/library/stdtypes.html#dict.items)`mapping`[`types.MappingProxyType`](https://docs.python.org/3.10/library/types.html#types.MappingProxyType)
* [**PEP 618**](https://www.python.org/dev/peps/pep-0618) :tüm yinelenebilirlerin eşit uzunlukta olmasını gerektiren[`zip()`](https://docs.python.org/3.10/library/functions.html#zip)isteğe bağlı bir bayrağı vardır`strict`
* Tamsayı argümanları alan yerleşik ve genişletme işlevleri, artık [`Decimal`](https://docs.python.org/3.10/library/decimal.html#decimal.Decimal)s, [`Fraction`](https://docs.python.org/3.10/library/fractions.html#fractions.Fraction)s ve yalnızca bir kayıpla tamsayılara dönüştürülebilen diğer nesneleri kabul etmez (örneğin, [`__int__()`](https://docs.python.org/3.10/reference/datamodel.html#object.\_\_int\_\_)yöntemi olan ancak [`__index__()`](https://docs.python.org/3.10/reference/datamodel.html#object.\_\_index\_\_)yöntemi olmayan). [(Bpo -37999'da](https://bugs.python.org/issue?@action=redirect\&bpo=37999) Serhiy Storchaka tarafından katkıda bulunulmuştur .)
* Döndürürse , operatör doğru bir şekilde ve beklendiği gibi geri [`object.__ipow__()`](https://docs.python.org/3.10/reference/datamodel.html#object.\_\_ipow\_\_)dönecektir . [(Bpo -38302'de](https://bugs.python.org/issue?@action=redirect\&bpo=38302) Alex Shkop tarafından katkıda bulunulmuştur .)[`NotImplemented`](https://docs.python.org/3.10/library/constants.html#NotImplemented)[`object.__pow__()`](https://docs.python.org/3.10/reference/datamodel.html#object.\_\_pow\_\_)[`object.__rpow__()`](https://docs.python.org/3.10/reference/datamodel.html#object.\_\_rpow\_\_)
* Atama ifadeleri artık küme değişmezleri ve küme anlamaları içinde ve sıra dizinlerinde (ancak dilimlerde değil) parantezsiz olarak kullanılabilir.
* `__builtins__`İşlevler, bir işlev yürütüldüğünde içine bakmak yerine yerleşik sembolleri aramak için kullanılan yeni bir özniteliğe sahiptir `__globals__['__builtins__']`. Öznitelik, `__globals__["__builtins__"]`varsa, mevcut yerleşiklerden başlatılır. [( Bpo-42990'da](https://bugs.python.org/issue?@action=redirect\&bpo=42990) Mark Shannon tarafından katkıda bulunulmuştur .)
* İki yeni yerleşik işlev – [`aiter()`](https://docs.python.org/3.10/library/functions.html#aiter)ve sırasıyla ve ile [`anext()`](https://docs.python.org/3.10/library/functions.html#anext)eşzamansız karşılıklar sağlamak için eklenmiştir . [( Bpo-31861'de](https://bugs.python.org/issue?@action=redirect\&bpo=31861) Joshua Bronson, Daniel Pope ve Justin Wang tarafından katkıda bulunulmuştur .)[`iter()`](https://docs.python.org/3.10/library/functions.html#iter)[`next()`](https://docs.python.org/3.10/library/functions.html#next)
* Statik yöntemler ( [`@staticmethod`](https://docs.python.org/3.10/library/functions.html#staticmethod)) ve sınıf yöntemleri ( [`@classmethod`](https://docs.python.org/3.10/library/functions.html#classmethod)) artık yöntem özniteliklerini ( `__module__`, `__name__`, `__qualname__`, `__doc__`, `__annotations__`) devralır ve yeni bir `__wrapped__`özniteliğe sahiptir. Ayrıca, statik yöntemler artık normal işlevler olarak çağrılabilir. [(Bpo -43682'de](https://bugs.python.org/issue?@action=redirect\&bpo=43682) Victor Stinner tarafından katkıda bulunulmuştur .)
* Karmaşık hedefler için ek açıklamalar ( tarafından tanımlanan hedeflerin yanı sıra her şey`simple name`[**PEP 526**](https://www.python.org/dev/peps/pep-0526) ) ile artık herhangi bir çalışma zamanı etkisine neden olmaz. (Batuhan Taşkaya,[ bpo-42737'de katkıda bulunmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=42737) .)`from __future__ import annotations`
* Sınıf ve modül nesneleri artık istek üzerine boş açıklamalar dikte ederek tembel oluşturun. `__dict__`Ek açıklamalar , geriye dönük uyumluluk için nesnenin içinde saklanır . Bu, birlikte çalışmak için en iyi uygulamaları geliştirir `__annotations__`; daha fazla bilgi için lütfen [Ek Açıklamalar En İyi Uygulamaları](https://docs.python.org/3.10/howto/annotations.html#annotations-howto) bölümüne bakın . [( bpo-43901'de](https://bugs.python.org/issue?@action=redirect\&bpo=43901) Larry Hastings tarafından katkıda bulunulmuştur .)
* `yield`, , veya adlandırılmış ifadelerden oluşan ek açıklamalar, yan etkileri nedeniyle artık yasaklanmıştır . (Batuhan Taşkaya tarafından [bpo-42725'te eklenmiştir](https://bugs.python.org/issue?@action=redirect\&bpo=42725) .)`yield fromawaitfrom __future__ import annotations`
* Bağlantısız değişkenlerin kullanımı `super()`ve sembol tablosunun ek açıklama olarak işlenmesini değiştirebilecek diğer ifadeler artık altında etkisiz hale getirildi . (Batuhan Taşkaya tarafından [bpo-42725'te eklenmiştir](https://bugs.python.org/issue?@action=redirect\&bpo=42725) .)`from __future__ import annotations`
* [`float`](https://docs.python.org/3.10/library/functions.html#float)Hem tür hem de türdeki NaN değerlerinin karma değerleri [`decimal.Decimal`](https://docs.python.org/3.10/library/decimal.html#decimal.Decimal)artık nesne kimliğine bağlıdır. `0`Eskiden NaN değerleri birbirine eşit olmasa da hep hash yapıyorlardı . Bu, birden çok NaN içeren sözlükler ve kümeler oluştururken aşırı karma çarpışmalar nedeniyle potansiyel olarak ikinci dereceden çalışma zamanı davranışına neden oldu. (Raymond Hettinger tarafından [bpo-43475'te katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=43475) .)
* Sabit silinirken A [`SyntaxError`](https://docs.python.org/3.10/library/exceptions.html#SyntaxError)( a yerine [`NameError`](https://docs.python.org/3.10/library/exceptions.html#NameError)) yükseltilecektir . [( Bpo-45000'de](https://bugs.python.org/issue?@action=redirect\&bpo=45000)[`__debug__`](https://docs.python.org/3.10/library/constants.html#debug\_\_) Dong-hee Na tarafından katkıda bulunulmuştur .)
* [`SyntaxError`](https://docs.python.org/3.10/library/exceptions.html#SyntaxError)istisnalar artık var `end_lineno`ve `end_offset`nitelikler. Belirlenmezse olacaklar `None`. [( Bpo-43914'te](https://bugs.python.org/issue?@action=redirect\&bpo=43914) Pablo Galindo tarafından katkıda bulunulmuştur .)

### Yeni Modüller&#x20;

* Henüz yok.

### Geliştirilmiş Modüller&#x20;

#### asyncio

Eksik `connect_accepted_socket()` yöntemi ekleyin. [( Bpo-41332'de](https://bugs.python.org/issue?@action=redirect\&bpo=41332) Alex Grönholm tarafından katkıda bulunulmuştur .)

#### argparse

Argparse yardımında yanıltıcı "isteğe bağlı argümanlar" ifadesi "seçenekler" ile değiştirildi. Bazı testler, tam çıktı eşleşmesine dayanıyorsa uyarlama gerektirebilir. (Raymond Hettinger tarafından [bpo-9694'te katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=9694) .)

#### array

[`index()`](https://docs.python.org/3.10/library/array.html#array.array.index)Şimdi yöntemi [`array.array`](https://docs.python.org/3.10/library/array.html#array.array)isteğe bağlı _başlatma_ ve _durdurma_ parametrelerine sahiptir . [(Bpo -31956'da](https://bugs.python.org/issue?@action=redirect\&bpo=31956) Anders Lorentsen ve Zackery Spytz tarafından katkıda bulunulmuştur .)

#### eşzamansız, eşzamansız, smtpd&#x20;

Bu modüller, Python 3.6'dan beri modül belgelerinde kullanımdan kaldırılmış olarak işaretlenmiştir. [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning)Bu modüllerin üçüne de bir içe aktarma zamanı eklendi.

#### base64

Genişletilmiş Hex Alfabesi ile Base32 Kodlama'yı ekleyin [`base64.b32hexencode()`](https://docs.python.org/3.10/library/base64.html#base64.b32hexencode)ve destekleyin.[`base64.b32hexdecode()`](https://docs.python.org/3.10/library/base64.html#base64.b32hexdecode)

#### bdb

Ayarlanan tüm kesme noktalarını sıfırlamak için ekleyin `clearBreakpoints()`. (Bpo [-24160'da](https://bugs.python.org/issue?@action=redirect\&bpo=24160) Irit Katriel tarafından katkıda bulunulmuştur .)

#### bisect

Modüldeki API'lere bir _anahtar_ işlevi sağlama olasılığı eklendi . [`bisect`](https://docs.python.org/3.10/library/bisect.html#module-bisect)(Raymond Hettinger tarafından [bpo-4356'da](https://bugs.python.org/issue?@action=redirect\&bpo=4356) katkıda bulunulmuştur .)

#### codecs

[`codecs.unregister()`](https://docs.python.org/3.10/library/codecs.html#codecs.unregister)Bir codec bileşeni arama işlevinin kaydını silmek için bir işlev ekleyin . [( Bpo-41842'de](https://bugs.python.org/issue?@action=redirect\&bpo=41842) Hai Shi tarafından katkıda bulunulmuştur .)

#### collections.abc&#x20;

Parametreli [jenerik](https://docs.python.org/3.10/library/stdtypes.html#types-genericalias) for artık `__args__`ile tutarlıdır . jenerik şimdi, şu anda ne yaptığına benzer şekilde, tür parametrelerini düzleştirir . Bu, sahip olacağı anlamına gelir ; önceden bu vardı . Bu değişikliğe izin vermek için , artık alt sınıflanabilir ve türün aboneliği yapılırken bir alt sınıf döndürülür. Python 3.9'da sessizce geçmiş olabilecek geçersiz parametreleştirme biçimleri için a'nın yükseltilebileceğini unutmayın . [( Bpo-42195'te](https://bugs.python.org/issue?@action=redirect\&bpo=42195) Ken Jin tarafından katkıda bulunulmuştur .)[`collections.abc.Callable`](https://docs.python.org/3.10/library/collections.abc.html#collections.abc.Callable)[`typing.Callable`](https://docs.python.org/3.10/library/typing.html#typing.Callable)[`collections.abc.Callable`](https://docs.python.org/3.10/library/collections.abc.html#collections.abc.Callable)[`typing.Callable`](https://docs.python.org/3.10/library/typing.html#typing.Callable)`collections.abc.Callable[[int, str], str]__args__(int, str, str)([int, str], str)`[`types.GenericAlias`](https://docs.python.org/3.10/library/types.html#types.GenericAlias)[`collections.abc.Callable`](https://docs.python.org/3.10/library/collections.abc.html#collections.abc.Callable)[`TypeError`](https://docs.python.org/3.10/library/exceptions.html#TypeError)[`collections.abc.Callable`](https://docs.python.org/3.10/library/collections.abc.html#collections.abc.Callable)

#### contextlib

[`contextlib.aclosing()`](https://docs.python.org/3.10/library/contextlib.html#contextlib.aclosing)Zaman uyumsuz olarak yayınlanmış kaynakları temsil eden zaman uyumsuz oluşturucuları ve nesneleri güvenli bir şekilde kapatmak için bir bağlam yöneticisi ekleyin . [(Bpo -41229'da](https://bugs.python.org/issue?@action=redirect\&bpo=41229) Joongi Kim ve John Belmonte tarafından katkıda bulunulmuştur .)

NET'e eşzamansız bağlam yöneticisi desteği ekleyin [`contextlib.nullcontext()`](https://docs.python.org/3.10/library/contextlib.html#contextlib.nullcontext). [(Bpo -41543'te](https://bugs.python.org/issue?@action=redirect\&bpo=41543) Tom Gringauz tarafından katkıda bulunulmuştur .)

`AsyncContextDecorator`Zaman uyumsuz bağlam yöneticilerinin dekoratör olarak kullanımını desteklemek için ekleyin .

#### curses

ncurses 6.1'e eklenen genişletilmiş renk işlevleri , , ve tarafından [`curses.color_content()`](https://docs.python.org/3.10/library/curses.html#curses.color\_content)şeffaf [`curses.init_color()`](https://docs.python.org/3.10/library/curses.html#curses.init\_color)olarak [`curses.init_pair()`](https://docs.python.org/3.10/library/curses.html#curses.init\_pair)kullanılacaktır [`curses.pair_content()`](https://docs.python.org/3.10/library/curses.html#curses.pair\_content). Yeni bir işlev, [`curses.has_extended_color_support()`](https://docs.python.org/3.10/library/curses.html#curses.has\_extended\_color\_support), temeldeki ncurses kitaplığı tarafından genişletilmiş renk desteğinin sağlanıp sağlanmadığını gösterir. [(Bpo -36982'de](https://bugs.python.org/issue?@action=redirect\&bpo=36982) Jeffrey Kintscher ve Hans Petter Jansson tarafından katkıda bulunulmuştur .)

Sabitler, temel alınan küfürler kitaplığı tarafından sağlandıkları takdirde `BUTTON5_*`artık modülde açığa çıkar . [(Bpo -39273'te](https://bugs.python.org/issue?@action=redirect\&bpo=39273)[`curses`](https://docs.python.org/3.10/library/curses.html#module-curses) Zackery Spytz tarafından katkıda bulunulmuştur .)

#### dataclasses

**\_\_yuvalar\_\_**&#x20;

Dekoratöre `slots`parametre eklendi . [( Bpo-42269'da](https://bugs.python.org/issue?@action=redirect\&bpo=42269)[`dataclasses.dataclass()`](https://docs.python.org/3.10/library/dataclasses.html#dataclasses.dataclass) Yurii Karabas tarafından katkıda bulunulmuştur )

**Yalnızca anahtar kelime alanları**&#x20;

dataclasses artık oluşturulan \_\_init\_\_ yönteminde yalnızca anahtar sözcük olan alanları desteklemektedir. Yalnızca anahtar kelime alanlarını belirtmenin birkaç yolu vardır.

Her alanın yalnızca anahtar kelime olduğunu söyleyebilirsiniz:

```
from dataclasses import dataclass

@dataclass(kw_only=True)
class Birthday:
    name: str
    birthday: datetime.date
```

Her ikisi de `name`, `birthday`oluşturulan \_\_init\_\_ yönteminin yalnızca anahtar sözcüğü parametreleridir.

Alan bazında yalnızca anahtar kelime belirtebilirsiniz:

```
from dataclasses import dataclass

@dataclass
class Birthday:
    name: str
    birthday: datetime.date = field(kw_only=True)
```

Burada sadece `birthday`anahtar kelime var. Tek tek alanlar ayarlarsanız `kw_only`, yalnızca anahtar kelime olmayan alanları takip etmesi gereken yalnızca anahtar kelime alanları nedeniyle alanları yeniden sıralamayla ilgili kurallar olduğunu unutmayın. Ayrıntılar için tam veri sınıfı belgelerine bakın.

Ayrıca bir KW\_ONLY işaretçisini izleyen tüm alanların yalnızca anahtar sözcük olduğunu belirtebilirsiniz. Bu muhtemelen en yaygın kullanım olacaktır:

```
from dataclasses import dataclass, KW_ONLY

@dataclass
class Point:
    x: float
    y: float
    _: KW_ONLY
    z: float = 0.0
    t: float = 0.0
```

Burada `z`ve `t`yalnızca anahtar kelime parametreleridir, while `x`ve `y`değildir. [( Bpo-43532'de](https://bugs.python.org/issue?@action=redirect\&bpo=43532) Eric V. Smith tarafından katkıda bulunulmuştur .)

#### distutils

Paketin tamamı `distutils`Python 3.12'de kaldırılmak üzere kullanımdan kaldırılmıştır. Paket derlemelerini belirleme işlevi, zaten tamamen üçüncü taraf paketleri `setuptools`ve `packaging`ile değiştirildi ve yaygın olarak kullanılan diğer API'lerin çoğu, standart kitaplığın başka bir yerinde ( [`platform`](https://docs.python.org/3.10/library/platform.html#module-platform), [`shutil`](https://docs.python.org/3.10/library/shutil.html#module-shutil), [`subprocess`](https://docs.python.org/3.10/library/subprocess.html#module-subprocess)veya gibi [`sysconfig`](https://docs.python.org/3.10/library/sysconfig.html#module-sysconfig)) mevcuttur. 'dan başka herhangi bir işlevi geçirme planı yoktur `distutils`ve diğer işlevleri kullanan uygulamalar, kodun özel kopyalarını oluşturmayı planlamalıdır. bkz.Tartışma için[ **PEP 632 .**](https://www.python.org/dev/peps/pep-0632)

`bdist_wininst`Python 3.8'de kullanımdan kaldırılan komut kaldırıldı . `bdist_wheel`Komutun şimdi Windows'ta ikili paketleri dağıtması önerilir . [(Bpo -42802'de](https://bugs.python.org/issue?@action=redirect\&bpo=42802) Victor Stinner tarafından katkıda bulunulmuştur .)

#### doctest

Bir modül tanımlamadığında `__loader__`, geri dönün `__spec__.loader`. [( Bpo-42133'te](https://bugs.python.org/issue?@action=redirect\&bpo=42133) Brett Cannon tarafından katkıda bulunulmuştur .)

#### encodings

`encodings.normalize_encoding()`artık ASCII olmayan karakterleri yok sayar. [( Bpo-39337'de](https://bugs.python.org/issue?@action=redirect\&bpo=39337) Hai Shi tarafından katkıda bulunulmuştur .)

#### fileinput

ve içine _kodlama_ ve _hata_ parametreleri ekleyin . (Inada Naoki tarafından [bpo-43712'de katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=43712) .)[`fileinput.input()`](https://docs.python.org/3.10/library/fileinput.html#fileinput.input)[`fileinput.FileInput`](https://docs.python.org/3.10/library/fileinput.html#fileinput.FileInput)

[`fileinput.hook_compressed()`](https://docs.python.org/3.10/library/fileinput.html#fileinput.hook\_compressed)şimdi _mod_ “r” olduğunda ve sıkıştırılmamış dosyalar gibi dosya sıkıştırıldığında `TextIOWrapper`nesneyi döndürür. (Inada Naoki tarafından [bpo-5758'de](https://bugs.python.org/issue?@action=redirect\&bpo=5758) katkıda bulunulmuştur .)

#### faulthandler

Modül [`faulthandler`](https://docs.python.org/3.10/library/faulthandler.html#module-faulthandler)artık bir çöp toplayıcı toplama sırasında önemli bir hata oluşup oluşmadığını algılar. [(Bpo -44466'da](https://bugs.python.org/issue?@action=redirect\&bpo=44466) Victor Stinner tarafından katkıda bulunulmuştur .)

#### gc

ve [`gc.get_objects()`](https://docs.python.org/3.10/library/gc.html#gc.get\_objects)için denetim kancaları ekleyin . [( Bpo-43439'da](https://bugs.python.org/issue?@action=redirect\&bpo=43439) Pablo Galindo tarafından katkıda bulunulmuştur .)[`gc.get_referrers()`](https://docs.python.org/3.10/library/gc.html#gc.get\_referrers)[`gc.get_referents()`](https://docs.python.org/3.10/library/gc.html#gc.get\_referents)

#### glob

Arama için kök dizini belirlemeye izin veren root\_dir _ve_ dir\_fd _parametrelerini_ ekleyin . [(Bpo -38144'te](https://bugs.python.org/issue?@action=redirect\&bpo=38144) Serhiy Storchaka tarafından katkıda bulunulmuştur .)[`glob()`](https://docs.python.org/3.10/library/glob.html#glob.glob)[`iglob()`](https://docs.python.org/3.10/library/glob.html#glob.iglob)

#### hashlib

hashlib modülü OpenSSL 1.1.1 veya daha yenisini gerektirir. (Christian Heimes tarafından[**PEP 644**](https://www.python.org/dev/peps/pep-0644) ve[ bpo-43669](https://bugs.python.org/issue?@action=redirect\&bpo=43669) .)

hashlib modülü, OpenSSL 3.0.0 için ön desteğe sahiptir. [(Bpo -38820](https://bugs.python.org/issue?@action=redirect\&bpo=38820) ve diğer sayılarda Christian Heimes tarafından katkıda bulunulmuştur .)

Pure-Python geri dönüşü [`pbkdf2_hmac()`](https://docs.python.org/3.10/library/hashlib.html#hashlib.pbkdf2\_hmac)kullanımdan kaldırıldı. Gelecekte PBKDF2-HMAC, yalnızca Python OpenSSL desteği ile oluşturulduğunda kullanılabilir olacaktır. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43880) 43880'de Christian Heimes tarafından katkıda bulunulmuştur .)

#### hmac

hmac modülü artık OpenSSL'nin HMAC uygulamasını dahili olarak kullanıyor. [(Bpo -40645'te](https://bugs.python.org/issue?@action=redirect\&bpo=40645) Christian Heimes tarafından katkıda bulunulmuştur .)

#### boşta ve boşta&#x20;

IDLE çağrısı yap [`sys.excepthook()`](https://docs.python.org/3.10/library/sys.html#sys.excepthook)('-n' olmadan başlatıldığında). Kullanıcı kancaları daha önce yoksayıldı. [( Bpo-43008'de](https://bugs.python.org/issue?@action=redirect\&bpo=43008) Ken Hilton tarafından katkıda bulunulmuştur .)

Ayarlar iletişim kutusunu yeniden düzenleyin. Genel sekmesini Windows ve Kabuk/Ed sekmelerine ayırın. Yardım menüsünü genişleten yardım kaynaklarını Uzantılar sekmesine taşıyın. Yeni seçenekler için yer açın ve iletişim kutusunu kısaltın. İkincisi, diyaloğu küçük ekranlara daha iyi sığdırır. [( Bpo-40468'de](https://bugs.python.org/issue?@action=redirect\&bpo=40468) Terry Jan Reedy tarafından katkıda bulunulmuştur .) Girinti alanı ayarını Yazı Tipi sekmesinden yeni Windows sekmesine taşıyın. [( Bpo-33962'de](https://bugs.python.org/issue?@action=redirect\&bpo=33962) Mark Roseman ve Terry Jan Reedy tarafından katkıda bulunulmuştur .)

Yukarıdaki değişiklikler 3.9 bakım sürümüne geri aktarıldı.

Bir Kabuk kenar çubuğu ekleyin. Birincil istemi ('>>>') kenar çubuğuna taşıyın. Kenar çubuğuna ikincil istemler ('…') ekleyin. Sol tıklama ve isteğe bağlı sürükleme, düzenleyici satır numarası kenar çubuğunda olduğu gibi bir veya daha fazla metin satırı seçer. Metin satırlarını seçtikten sonra sağ tıklama, 'isteklerle kopyala' içeren bir bağlam menüsü görüntüler. Bu, seçili metinden satırlarla birlikte kenar çubuğundan istemleri sıkıştırır. Bu seçenek, metnin bağlam menüsünde de görünür. [( Bpo-37903'te](https://bugs.python.org/issue?@action=redirect\&bpo=37903) Tal Einat tarafından katkıda bulunulmuştur .)

Etkileşimli kodu girintilemek için sekmeler yerine boşluklar kullanın. Bu, etkileşimli kod girişlerinin 'doğru görünmesini' sağlar. Bunu mümkün kılmak, kabuk kenar çubuğunu eklemek için büyük bir motivasyondu. [( Bpo-37892'de](https://bugs.python.org/issue?@action=redirect\&bpo=37892) Terry Jan Reedy tarafından katkıda bulunulmuştur .)

[Yeni geçici anahtar kelimeleri](https://docs.python.org/3.10/reference/lexical\_analysis.html#soft-keywords) [`match`](https://docs.python.org/3.10/reference/compound\_stmts.html#match) , [`case`](https://docs.python.org/3.10/reference/compound\_stmts.html#match)ve [`_`](https://docs.python.org/3.10/reference/compound\_stmts.html#wildcard-patterns)kalıp eşleştirme ifadelerinde vurgulayın . Bununla birlikte, bu vurgulama mükemmel değildir ve desenlerdeki bazı `_`-s dahil olmak üzere bazı nadir durumlarda yanlış olacaktır. [( Bpo-44010'da](https://bugs.python.org/issue?@action=redirect\&bpo=44010)`case` Tal Einat tarafından katkıda bulunulmuştur .)

3.10 bakım sürümlerinde yeni.

`.pyi`Dosyalara sözdizimi vurgulama uygulayın . [(Bpo -45447'de](https://bugs.python.org/issue?@action=redirect\&bpo=45447) Alex Waygood ve Terry Jan Reedy tarafından katkıda bulunulmuştur .)

Shell'i girdiler ve çıktılarla kaydederken istemleri dahil edin. [( Gh-95191'de](https://github.com/python/cpython/issues/95191) Terry Jan Reedy tarafından katkıda bulunulmuştur .)

#### importlib.metadata&#x20;

`importlib_metadata`4.6 ( [geçmiş](https://importlib-metadata.readthedocs.io/en/latest/history.html) ) ile özellik paritesi .

[importlib.metadata giriş noktaları](https://docs.python.org/3.10/library/importlib.metadata.html#entry-points) artık yeni bir `importlib.metadata.EntryPoints`sınıf aracılığıyla grup ve ada göre giriş noktalarını seçmek için daha iyi bir deneyim sunuyor. Kullanımdan kaldırma ve kullanım hakkında daha fazla bilgi için belgelerdeki Uyumluluk Notuna bakın.

Üst `importlib.metadata.packages_distributions()`düzey Python modüllerini ve paketlerini `importlib.metadata.Distribution`.

#### inspect

Bir modül tanımlamadığında `__loader__`, geri dönün `__spec__.loader`. [( Bpo-42133'te](https://bugs.python.org/issue?@action=redirect\&bpo=42133) Brett Cannon tarafından katkıda bulunulmuştur .)

[`inspect.get_annotations()`](https://docs.python.org/3.10/library/inspect.html#inspect.get\_annotations)Bir nesnede tanımlanan ek açıklamaları güvenli bir şekilde hesaplayan Add . Çeşitli nesne türleri üzerindeki ek açıklamalara erişmenin tuhaflıkları etrafında çalışır ve incelediği nesne hakkında çok az varsayımda bulunur. [`inspect.get_annotations()`](https://docs.python.org/3.10/library/inspect.html#inspect.get\_annotations)ayrıca dizilmiş ek açıklamaları doğru şekilde kaldırabilir. [`inspect.get_annotations()`](https://docs.python.org/3.10/library/inspect.html#inspect.get\_annotations)artık herhangi bir Python nesnesinde tanımlanan ek açıklamalara erişim için en iyi uygulama olarak kabul edilir; Ek açıklamalarla çalışmak için en iyi uygulamalar hakkında daha fazla bilgi için lütfen [Ek Açıklamalar En İyi Uygulamaları](https://docs.python.org/3.10/howto/annotations.html#annotations-howto) bölümüne bakın . İlgili olarak [`inspect.signature()`](https://docs.python.org/3.10/library/inspect.html#inspect.signature), [`inspect.Signature.from_callable()`](https://docs.python.org/3.10/library/inspect.html#inspect.Signature.from\_callable), ve `inspect.Signature.from_function()` şimdi [`inspect.get_annotations()`](https://docs.python.org/3.10/library/inspect.html#inspect.get\_annotations)ek açıklamaları almak için arayın. Bu, artık dizgeleştirilmiş ek açıklamaların dizgisizleştirilmesi anlamına gelir [`inspect.signature()`](https://docs.python.org/3.10/library/inspect.html#inspect.signature)ve şimdi de yapabilir. [( bpo-43817'de](https://bugs.python.org/issue?@action=redirect\&bpo=43817)[`inspect.Signature.from_callable()`](https://docs.python.org/3.10/library/inspect.html#inspect.Signature.from\_callable) Larry Hastings tarafından katkıda bulunulmuştur .)

#### itertools

Ekle [`itertools.pairwise()`](https://docs.python.org/3.10/library/itertools.html#itertools.pairwise). [( Bpo-38200'de](https://bugs.python.org/issue?@action=redirect\&bpo=38200) Raymond Hettinger tarafından katkıda bulunulmuştur .)

#### linecache

Bir modül tanımlamadığında `__loader__`, geri dönün `__spec__.loader`. [( Bpo-42133'te](https://bugs.python.org/issue?@action=redirect\&bpo=42133) Brett Cannon tarafından katkıda bulunulmuştur .)

#### os

[`os.cpu_count()`](https://docs.python.org/3.10/library/os.html#os.cpu\_count)VxWorks RTOS için destek ekleyin . [( bpo-41440'da](https://bugs.python.org/issue?@action=redirect\&bpo=41440) Peixing Xin tarafından katkıda bulunulmuştur .)

Sistem çağrısını Linux'ta [`os.eventfd()`](https://docs.python.org/3.10/library/os.html#os.eventfd)sarmak için yeni bir işlev ve ilgili yardımcılar ekleyin . [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=41001)`eventfd2` 41001'de Christian Heimes tarafından katkıda bulunulmuştur .)

[`os.splice()`](https://docs.python.org/3.10/library/os.html#os.splice)Dosya tanımlayıcılarından birinin bir kanala atıfta bulunması gereken, çekirdek adres alanı ve kullanıcı adres alanı arasında kopyalama yapmadan iki dosya tanımlayıcı arasında veri taşımaya izin veren ekleme . [( Bpo-41625'te](https://bugs.python.org/issue?@action=redirect\&bpo=41625) Pablo Galindo tarafından katkıda bulunulmuştur .)

[`O_EVTONLY`](https://docs.python.org/3.10/library/os.html#os.O\_EVTONLY), [`O_FSYNC`](https://docs.python.org/3.10/library/os.html#os.O\_FSYNC)ve macOS için [`O_SYMLINK`](https://docs.python.org/3.10/library/os.html#os.O\_SYMLINK) ekleyin . [( Bpo-43106'da](https://bugs.python.org/issue?@action=redirect\&bpo=43106)[`O_NOFOLLOW_ANY`](https://docs.python.org/3.10/library/os.html#os.O\_NOFOLLOW\_ANY) Dong-hee Na tarafından katkıda bulunulmuştur .)

#### os.path&#x20;

[`os.path.realpath()`](https://docs.python.org/3.10/library/os.path.html#os.path.realpath)artık yalnızca anahtar kelime içeren _katı_ bir argümanı kabul ediyor. olarak ayarlandığında `True`, [`OSError`](https://docs.python.org/3.10/library/exceptions.html#OSError)bir yol yoksa veya bir sembolik bağlantı döngüsüyle karşılaşılırsa yükseltilir. [( Bpo-43757'de](https://bugs.python.org/issue?@action=redirect\&bpo=43757) Barney Gale tarafından katkıda bulunulmuştur .)

#### pathlib

için dilim desteği ekleyin [`PurePath.parents`](https://docs.python.org/3.10/library/pathlib.html#pathlib.PurePath.parents). [( Bpo-35498'de](https://bugs.python.org/issue?@action=redirect\&bpo=35498) Joshua Cannon tarafından katkıda bulunulmuştur .)

Negatif indeksleme desteği ekleyin [`PurePath.parents`](https://docs.python.org/3.10/library/pathlib.html#pathlib.PurePath.parents). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=21041) 21041'de Yaroslav Pankovych tarafından katkıda bulunulmuştur .)

[`Path.hardlink_to`](https://docs.python.org/3.10/library/pathlib.html#pathlib.Path.hardlink\_to)yerine geçen yöntemi ekleyin [`link_to()`](https://docs.python.org/3.10/library/pathlib.html#pathlib.Path.link\_to). Yeni yöntem, ile aynı argüman sırasına sahiptir [`symlink_to()`](https://docs.python.org/3.10/library/pathlib.html#pathlib.Path.symlink\_to). [( Bpo-39950'de](https://bugs.python.org/issue?@action=redirect\&bpo=39950) Barney Gale tarafından katkıda bulunulmuştur .)

[`pathlib.Path.stat()`](https://docs.python.org/3.10/library/pathlib.html#pathlib.Path.stat)ve [`chmod()`](https://docs.python.org/3.10/library/pathlib.html#pathlib.Path.chmod)şimdi , modüldeki karşılık gelen işlevlerle tutarlılık için yalnızca _follow\_symlinks_[`os`](https://docs.python.org/3.10/library/os.html#module-os) anahtar kelime argümanını kabul edin . [( Bpo-39906'da](https://bugs.python.org/issue?@action=redirect\&bpo=39906) Barney Gale tarafından katkıda bulunulmuştur .)

#### platform

[Freedesktop.org işletim sistemi sürümü](https://www.freedesktop.org/software/systemd/man/os-release.html) standart dosyasından [`platform.freedesktop_os_release()`](https://docs.python.org/3.10/library/platform.html#platform.freedesktop\_os\_release)işletim sistemi kimliğini almak için ekleyin . [(Bpo -28468'de](https://bugs.python.org/issue?@action=redirect\&bpo=28468) Christian Heimes tarafından katkıda bulunulmuştur .)

#### pprint

[`pprint.pprint()`](https://docs.python.org/3.10/library/pprint.html#pprint.pprint)şimdi yeni bir `underscore_numbers`anahtar kelime argümanını kabul ediyor. [( bpo-](https://bugs.python.org/issue?@action=redirect\&bpo=42914) 42914'te sblondon tarafından katkıda bulunulmuştur .)

[`pprint`](https://docs.python.org/3.10/library/pprint.html#module-pprint)[`dataclasses.dataclass`](https://docs.python.org/3.10/library/dataclasses.html#dataclasses.dataclass)şimdi örnekleri güzelce yazdırabilir . [( Bpo-43080'de](https://bugs.python.org/issue?@action=redirect\&bpo=43080) Lewis Gaul tarafından katkıda bulunulmuştur .)

#### py\_compile&#x20;

`--quiet`NET'in komut satırı arayüzüne seçenek ekleyin [`py_compile`](https://docs.python.org/3.10/library/py\_compile.html#module-py\_compile). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=38731) 38731'de Gregory Schevchenko tarafından katkıda bulunulmuştur .)

#### pyclbr

ve tarafından döndürülen ağaçtaki ve nesnelerine bir `end_lineno`öznitelik ekleyin . Mevcut (başlangıç) ile eşleşir . [(Bpo -38307'de](https://bugs.python.org/issue?@action=redirect\&bpo=38307) Aviral Srivastava tarafından katkıda bulunulmuştur .)`FunctionClasspyclbr.readline()pyclbr.readline_ex()lineno`

#### shelve

Modül [`shelve`](https://docs.python.org/3.10/library/shelve.html#module-shelve)artık raflar oluştururken varsayılan olarak protokol [`pickle.DEFAULT_PROTOCOL`](https://docs.python.org/3.10/library/pickle.html#pickle.DEFAULT\_PROTOCOL)yerine kullanır. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=34204) 34204'te Zackery Spytz tarafından katkıda bulunulmuştur .)[`pickle`](https://docs.python.org/3.10/library/pickle.html#module-pickle)`3`

#### statistics

[`covariance()`](https://docs.python.org/3.10/library/statistics.html#statistics.covariance)Pearson [`correlation()`](https://docs.python.org/3.10/library/statistics.html#statistics.correlation)ve basit [`linear_regression()`](https://docs.python.org/3.10/library/statistics.html#statistics.linear\_regression)fonksiyonları ekleyin . [(Bpo -38490'da](https://bugs.python.org/issue?@action=redirect\&bpo=38490) Tymoteusz Wołodźko tarafından katkıda bulunulmuştur .)

#### site

Bir modül tanımlamadığında `__loader__`, geri dönün `__spec__.loader`. [( Bpo-42133'te](https://bugs.python.org/issue?@action=redirect\&bpo=42133) Brett Cannon tarafından katkıda bulunulmuştur .)

#### socket

İstisna [`socket.timeout`](https://docs.python.org/3.10/library/socket.html#socket.timeout)artık bir takma addır [`TimeoutError`](https://docs.python.org/3.10/library/exceptions.html#TimeoutError). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=42413) 42413'te Christian Heimes tarafından katkıda bulunulmuştur .)

MPTCP yuvaları oluşturma seçeneği ekleyin ( [bpo-43571'de](https://bugs.python.org/issue?@action=redirect\&bpo=43571)`IPPROTO_MPTCP` Rui Cunha tarafından sağlanmıştır .)

Hizmet türünü (ToS) veya DSCP/ECN alanlarını alma seçeneği ekleyin ( [bpo-](https://bugs.python.org/issue?@action=redirect\&bpo=44077)`IP_RECVTOS` 44077'de Georg Sauthoff tarafından sağlanmıştır .)

#### ssl

SSL modülü, OpenSSL 1.1.1 veya daha yenisini gerektirir. (Christian Heimes tarafından[**PEP 644**](https://www.python.org/dev/peps/pep-0644) ve[ bpo-43669](https://bugs.python.org/issue?@action=redirect\&bpo=43669) .)

SSL modülü, OpenSSL 3.0.0 ve yeni seçenek için ön desteğe sahiptir [`OP_IGNORE_UNEXPECTED_EOF`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_IGNORE\_UNEXPECTED\_EOF). (Bpo [-38820](https://bugs.python.org/issue?@action=redirect\&bpo=38820) , [bpo-43794](https://bugs.python.org/issue?@action=redirect\&bpo=43794) , [bpo-43788](https://bugs.python.org/issue?@action=redirect\&bpo=43788) , [bpo-43791](https://bugs.python.org/issue?@action=redirect\&bpo=43791) , [bpo-43799](https://bugs.python.org/issue?@action=redirect\&bpo=43799) , [bpo-43920](https://bugs.python.org/issue?@action=redirect\&bpo=43920) , [bpo-43789](https://bugs.python.org/issue?@action=redirect\&bpo=43789) ve [bpo-43811'de Christian Heimes tarafından katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=43811) .)

Kullanımdan kaldırılan işlev ve kullanımdan kaldırılan sabitlerin kullanımı artık bir [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning). [`ssl.SSLContext.options`](https://docs.python.org/3.10/library/ssl.html#ssl.SSLContext.options)varsayılan olarak vardır [`OP_NO_SSLv2`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_SSLv2)ve [`OP_NO_SSLv3`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_SSLv3)ayarlanır ve bu nedenle bayrağın yeniden ayarlanması konusunda uyarıda bulunamaz. Kullanımdan [kaldırma bölümünde](https://docs.python.org/3.10/whatsnew/3.10.html#whatsnew310-deprecated) , kullanımdan kaldırılan özelliklerin bir listesi bulunur. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43880) 43880'de Christian Heimes tarafından katkıda bulunulmuştur .)

SSL modülü artık daha güvenli varsayılan ayarlara sahiptir. İletim gizliliği veya SHA-1 MAC içermeyen şifreler varsayılan olarak devre dışıdır. Güvenlik düzeyi 2, 112 bitten daha az güvenlikle zayıf RSA, DH ve ECC anahtarlarını yasaklar. [`SSLContext`](https://docs.python.org/3.10/library/ssl.html#ssl.SSLContext)varsayılan olarak minimum protokol sürümü TLS 1.2'dir. Ayarlar Hynek Schlawack'ın araştırmasına dayanmaktadır. [(Bpo -43998'de](https://bugs.python.org/issue?@action=redirect\&bpo=43998) Christian Heimes tarafından katkıda bulunulmuştur .)

Kullanımdan kaldırılan protokoller SSL 3.0, TLS 1.0 ve TLS 1.1 artık resmi olarak desteklenmemektedir. Python onları aktif olarak engellemez. Ancak OpenSSL oluşturma seçenekleri, dağıtım yapılandırmaları, satıcı yamaları ve şifre paketleri başarılı bir el sıkışmasını önleyebilir.

_İşleve bir zaman aşımı_ parametresi ekleyin [`ssl.get_server_certificate()`](https://docs.python.org/3.10/library/ssl.html#ssl.get\_server\_certificate). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=31870) 31870'de Zackery Spytz tarafından katkıda bulunulmuştur .)

SSL modülü, yığın türleri ve çok fazlı başlatma kullanır. [(Bpo -42333'te](https://bugs.python.org/issue?@action=redirect\&bpo=42333) Christian Heimes tarafından katkıda bulunulmuştur .)

Yeni bir doğrulama bayrağı [`VERIFY_X509_PARTIAL_CHAIN`](https://docs.python.org/3.10/library/ssl.html#ssl.VERIFY\_X509\_PARTIAL\_CHAIN)eklendi. [( bpo-40849'da](https://bugs.python.org/issue?@action=redirect\&bpo=40849) l0x tarafından katkıda bulunulmuştur .)

#### sqlite3

`connect/handle()`, [`enable_load_extension()`](https://docs.python.org/3.10/library/sqlite3.html#sqlite3.Connection.enable\_load\_extension)ve için denetim etkinlikleri ekleyin [`load_extension()`](https://docs.python.org/3.10/library/sqlite3.html#sqlite3.Connection.load\_extension). [( Bpo-43762'de](https://bugs.python.org/issue?@action=redirect\&bpo=43762) Erlend E. Aasland tarafından katkıda bulunulmuştur .)

#### sys

Öznitelik ekle [`sys.orig_argv`](https://docs.python.org/3.10/library/sys.html#sys.orig\_argv): Python yürütülebilir dosyasına iletilen orijinal komut satırı bağımsız değişkenlerinin listesi. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=23427) 23427'de Victor Stinner tarafından katkıda bulunulmuştur .)

[`sys.stdlib_module_names`](https://docs.python.org/3.10/library/sys.html#sys.stdlib\_module\_names)Standart kitaplık modülü adlarının listesini içeren ekleyin . [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=42955) 42955'te Victor Stinner tarafından katkıda bulunulmuştur .)

#### \_thread

[`_thread.interrupt_main()`](https://docs.python.org/3.10/library/\_thread.html#thread.interrupt\_main)şimdi simüle etmek için isteğe bağlı bir sinyal numarası alır (varsayılan hala [`signal.SIGINT`](https://docs.python.org/3.10/library/signal.html#signal.SIGINT)). [(Bpo -43356'da](https://bugs.python.org/issue?@action=redirect\&bpo=43356) Antoine Pitrou tarafından katkıda bulunulmuştur .)

#### threading

Ekle [`threading.gettrace()`](https://docs.python.org/3.10/library/threading.html#threading.gettrace)ve sırasıyla ve [`threading.getprofile()`](https://docs.python.org/3.10/library/threading.html#threading.getprofile)tarafından ayarlanan işlevleri almak için . [(Bpo -42251'de](https://bugs.python.org/issue?@action=redirect\&bpo=42251) Mario Corchero tarafından katkıda bulunulmuştur .)[`threading.settrace()`](https://docs.python.org/3.10/library/threading.html#threading.settrace)[`threading.setprofile()`](https://docs.python.org/3.10/library/threading.html#threading.setprofile)

Bozuk veya farklı bir değere ayarlanmış olması durumunda [`threading.__excepthook__`](https://docs.python.org/3.10/library/threading.html#threading.\_\_excepthook\_\_)orijinal değerinin alınmasına izin vermek için ekleyin . [(Bpo -42308'de](https://bugs.python.org/issue?@action=redirect\&bpo=42308)[`threading.excepthook()`](https://docs.python.org/3.10/library/threading.html#threading.excepthook) Mario Corchero tarafından katkıda bulunulmuştur .)

#### traceback

, [`format_exception()`](https://docs.python.org/3.10/library/traceback.html#traceback.format\_exception)ve işlevleri artık yalnızca konumsal bir argüman [`format_exception_only()`](https://docs.python.org/3.10/library/traceback.html#traceback.format\_exception\_only)olarak [`print_exception()`](https://docs.python.org/3.10/library/traceback.html#traceback.print\_exception)bir istisna nesnesi alabilir. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=26389) 26389'da Zackery Spytz ve Matthias Bussonnier tarafından katkıda bulunulmuştur .)

#### types

Tür denetleyicileri tarafından kolayca yorumlanabilen yeni bir tür kümesi sağlayarak [`types.EllipsisType`](https://docs.python.org/3.10/library/types.html#types.EllipsisType), [`types.NoneType`](https://docs.python.org/3.10/library/types.html#types.NoneType) ve sınıflarını yeniden tanıtın . [`types.NotImplementedType`](https://docs.python.org/3.10/library/types.html#types.NotImplementedType)(Bas van Beek, [bpo-41810'da katkıda bulunmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=41810) .)

#### typing

Büyük değişiklikler için, bkz . [Tip İpuçlarıyla İlgili Yeni Özellikler](https://docs.python.org/3.10/whatsnew/3.10.html#new-feat-related-type-hints) .

davranışı [`typing.Literal`](https://docs.python.org/3.10/library/typing.html#typing.Literal)ile uyumlu olacak şekilde değiştirildi[**PEP 586**](https://www.python.org/dev/peps/pep-0586) ve PEP'te belirtilen statik tip denetleyicilerinin davranışıyla eşleşmesi için.

1. `Literal`şimdi parametreleri tekilleştirir.
2. Nesneler arasındaki eşitlik karşılaştırmaları `Literal`artık sıra bağımsızdır.
3. `Literal`karşılaştırmalar artık türlere saygı duyuyor. Örneğin, daha önce değerlendirilen . Şimdi oldu . Bu değişikliği desteklemek için, dahili olarak kullanılan tür önbelleği artık türlerin ayırt edilmesini destekler.`Literal[0] == Literal[False]TrueFalse`
4.  `Literal`nesneler artık [`TypeError`](https://docs.python.org/3.10/library/exceptions.html#TypeError), parametrelerinden herhangi biri [hashable](https://docs.python.org/3.10/glossary.html#term-hashable) değilse, eşitlik karşılaştırmaları sırasında bir istisna oluşturacaktır . `Literal`Unhashable olmayan parametrelerle bildirmenin bir hata vermeyeceğini unutmayın :

    \>>>

    ```
    >>> from typing import Literal
    >>> Literal[{0}]
    >>> Literal[{0}] == Literal[{False}]
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: unhashable type: 'set'
    ```

[( Bpo-42345'te](https://bugs.python.org/issue?@action=redirect\&bpo=42345) Yurii Karabas tarafından katkıda bulunulmuştur .)

[`typing.is_typeddict()`](https://docs.python.org/3.10/library/typing.html#typing.is\_typeddict)Bir açıklamanın bir [`typing.TypedDict`](https://docs.python.org/3.10/library/typing.html#typing.TypedDict). [( Bpo-41792'de](https://bugs.python.org/issue?@action=redirect\&bpo=41792) Patrick Reader tarafından katkıda bulunulmuştur .)

Yalnızca veri değişkenleri bildirilmiş olan alt sınıflar, ile dekore edilmedikçe kontrol edildiğinde `typing.Protocol`artık a yükseltecektir . Daha önce, bu kontroller sessizce geçti. Kullanıcılar, çalışma zamanı protokolleri istiyorlarsa , alt sınıflarını dekoratörle dekore etmelidir . [( Bpo-38908'de](https://bugs.python.org/issue?@action=redirect\&bpo=38908) Yurii Karabas tarafından katkıda bulunulmuştur .)`TypeErrorisinstanceruntime_checkable()runtime_checkable()`

`typing.io`ve alt modüllerinden içe aktarma `typing.re`artık [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning). Bu alt modüller Python 3.8'den beri kullanımdan kaldırılmıştır ve Python'un gelecekteki bir sürümünde kaldırılacaktır. Bu alt modüllere ait olan herhangi bir şey doğrudan [`typing`](https://docs.python.org/3.10/library/typing.html#module-typing)bunun yerine içe aktarılmalıdır. [(Bpo -38291'de](https://bugs.python.org/issue?@action=redirect\&bpo=38291) Sebastian Rittau tarafından katkıda bulunulmuştur .)

#### unittest

[`assertNoLogs()`](https://docs.python.org/3.10/library/unittest.html#unittest.TestCase.assertNoLogs)Mevcut olanı tamamlamak için yeni bir yöntem ekleyin [`assertLogs()`](https://docs.python.org/3.10/library/unittest.html#unittest.TestCase.assertLogs). [( Bpo-39385'te](https://bugs.python.org/issue?@action=redirect\&bpo=39385) Kit Yan Choi tarafından katkıda bulunulmuştur .)

#### urllib.parse&#x20;

Python 3.10'dan önceki Python sürümleri, ve içinde hem ve `;`sorgu `&`parametresi ayırıcıları olarak kullanılmasına izin verdi . Güvenlik kaygıları nedeniyle ve daha yeni W3C önerilerine uymak için bu, varsayılan olarak yalnızca tek bir ayırıcı anahtara izin verecek şekilde değiştirilmiştir. Bu değişiklik , etkilenen işlevleri dahili olarak kullandıkları için de etkiler. Daha fazla ayrıntı için lütfen ilgili belgelerine bakın. [( bpo-42967'de](https://bugs.python.org/issue?@action=redirect\&bpo=42967) Adam Goldschmidt, Senthil Kumaran ve Ken Jin tarafından katkıda bulunulmuştur .)[`urllib.parse.parse_qs()`](https://docs.python.org/3.10/library/urllib.parse.html#urllib.parse.parse\_qs)[`urllib.parse.parse_qsl()`](https://docs.python.org/3.10/library/urllib.parse.html#urllib.parse.parse\_qsl)`&`[`cgi.parse()`](https://docs.python.org/3.10/library/cgi.html#cgi.parse)[`cgi.parse_multipart()`](https://docs.python.org/3.10/library/cgi.html#cgi.parse\_multipart)

Bir URL'nin bölümlerinde yeni satır veya sekme karakterlerinin bulunması, bazı saldırı biçimlerine izin verir. Güncellenen WHATWG spesifikasyonunun ardından[**RFC 3986**](https://datatracker.ietf.org/doc/html/rfc3986.html) , ASCII yeni satırve sekme,bu tür saldırıları önlemekiçin ayrıştırıcı tarafından URL'den çıkarılırKaldırma karakterleri, yeni bir modül seviyesi değişkeni tarafından kontrol edilir . (Bkz.[ bpo-43882](https://bugs.python.org/issue?@action=redirect\&bpo=43882) )[`urllib.parse`](https://docs.python.org/3.10/library/urllib.parse.html#module-urllib.parse)`urllib.parse._UNSAFE_URL_BYTES_TO_REMOVE`

#### xml

Modüle bir [`LexicalHandler`](https://docs.python.org/3.10/library/xml.sax.handler.html#xml.sax.handler.LexicalHandler)sınıf ekleyin . [`xml.sax.handler`](https://docs.python.org/3.10/library/xml.sax.handler.html#module-xml.sax.handler)(Jonathan Gossage ve Zackery Spytz tarafından [bpo-](https://bugs.python.org/issue?@action=redirect\&bpo=35018) 35018'de katkıda bulunulmuştur .)

#### zipimport

İle ilgili yöntemler ekleyin[**PEP 451**](https://www.python.org/dev/peps/pep-0451) :[`find_spec()`](https://docs.python.org/3.10/library/zipimport.html#zipimport.zipimporter.find\_spec), [`zipimport.zipimporter.create_module()`](https://docs.python.org/3.10/library/zipimport.html#zipimport.zipimporter.create\_module)ve [`zipimport.zipimporter.exec_module()`](https://docs.python.org/3.10/library/zipimport.html#zipimport.zipimporter.exec\_module). [( Bpo-42131'de](https://bugs.python.org/issue?@action=redirect\&bpo=42131) Brett Cannon tarafından katkıda.)

[`invalidate_caches()`](https://docs.python.org/3.10/library/zipimport.html#zipimport.zipimporter.invalidate\_caches)Yöntem ekleyin . [( Bpo-14678'de](https://bugs.python.org/issue?@action=redirect\&bpo=14678) Desmond Cheong tarafından katkıda bulunulmuştur .)

### Optimizations

* Yapıcılar [`str()`](https://docs.python.org/3.10/library/stdtypes.html#str)ve artık [`bytes()`](https://docs.python.org/3.10/library/stdtypes.html#bytes)daha [`bytearray()`](https://docs.python.org/3.10/library/stdtypes.html#bytearray)hızlılar (küçük nesneler için yaklaşık %30-40). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=41334) 41334'te Serhiy Storchaka tarafından katkıda bulunulmuştur .)
* Modül [`runpy`](https://docs.python.org/3.10/library/runpy.html#module-runpy)artık daha az modül içe aktarıyor. Komut başlatma süresi ortalama olarak 1,4 kat daha hızlıdır . Linux'ta Python 3.9'da 69 modülü içe aktarırken Python 3.10'da yalnızca 51 modülü (-18) içe aktarır. [(Bpo -41006](https://bugs.python.org/issue?@action=redirect\&bpo=41006) ve [bpo-41718'de](https://bugs.python.org/issue?@action=redirect\&bpo=41718) Victor Stinner tarafından katkıda bulunulmuştur .)`python3 -m module-namepython3 -I -m module-name`
* Talimat artık yeni " `LOAD_ATTR`işlem kodu önbelleği başına" mekanizmasını kullanıyor. Artık normal özellikler için yaklaşık %36 ve slotlar için %44 daha hızlı. ( Orijinal olarak PyPy ve MicroPython'da uygulanan fikirlere dayalı olarak, [bpo-42093'te](https://bugs.python.org/issue?@action=redirect\&bpo=42927) Pablo Galindo ve Yury Selivanov ve [bpo-42927'de](https://bugs.python.org/issue?@action=redirect\&bpo=42093) Guido van Rossum tarafından katkıda bulunulmuştur.)
* Python'u oluştururken , hem derleme hem de bağlantı satırına [`--enable-optimizations`](https://docs.python.org/3.10/using/configure.html#cmdoption-enable-optimizations)şimdi `-fno-semantic-interposition`eklenir. Bu, %30'a varan bir oranda [`--enable-shared`](https://docs.python.org/3.10/using/configure.html#cmdoption-enable-shared) ile oluşturulan Python yorumlayıcısının yapılarını hızlandırır . `gcc`Daha fazla ayrıntı için [bu makaleye](https://developers.redhat.com/blog/2020/06/25/red-hat-enterprise-linux-8-2-brings-faster-python-3-8-run-speeds/) bakın. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=38980) 38980'de Victor Stinner ve Pablo Galindo tarafından katkıda bulunulmuştur .)
* [`bz2`](https://docs.python.org/3.10/library/bz2.html#module-bz2)// modülleri için yeni bir çıktı arabelleği yönetim kodu kullanın [`lzma`](https://docs.python.org/3.10/library/lzma.html#module-lzma)ve sınıfa işlev ekleyin. bz2 dekompresyonu artık 1.09x \~ 1.17x, lzma dekompresyonu 1.20x \~ 1.32x, 1.11x \~ 1.18x daha hızlı. (Ma Lin tarafından katkıda bulunulmuştur, Gregory P. Smith tarafından gözden geçirilmiştir, [bpo-41486](https://bugs.python.org/issue?@action=redirect\&bpo=41486) )[`zlib`](https://docs.python.org/3.10/library/zlib.html#module-zlib)`.readall()_compression.DecompressReaderGzipFile.read(-1)`
* Dizgeleştirilmiş ek açıklamalar kullanılırken, işlev oluşturulduğunda işlevler için ek açıklamalar artık oluşturulmaz. Bunun yerine, bir dizi dizi olarak depolanırlar ve işlev nesnesi tembelce bunu talep üzerine ek açıklamalara dönüştürür. Bu optimizasyon, açıklamalı bir işlevi tanımlamak için gereken CPU süresini yarı yarıya azaltır. [( Bpo-42202'de](https://bugs.python.org/issue?@action=redirect\&bpo=42202) Yurii Karabas ve Inada Naoki tarafından katkıda bulunulmuştur .)
* Uzun dizilerde ikinci dereceden davranıştan kaçınmak için şimdi bazen Crochemore & Perrin'in “İki Yönlü” dizi arama algoritmasını kullanan alt dizi arama işlevleri . [( Bpo-41972'de](https://bugs.python.org/issue?@action=redirect\&bpo=41972) Dennis Sweeney tarafından katkıda bulunulmuştur )`str1 in str2str2.find(str1)`
* `_PyType_Lookup()`Yaygın önbellek isabetlerinde tür özniteliği önbellek arama performansını iyileştirmek için mikro optimizasyonlar ekleyin . Bu, yorumlayıcıyı ortalama olarak 1,04 kat daha hızlı hale getirir. [(Bpo -43452'de](https://bugs.python.org/issue?@action=redirect\&bpo=43452) Dino Viehland tarafından katkıda bulunulmuştur .)
* Aşağıdaki yerleşik işlevler artık daha hızlı[**PEP 590**](https://www.python.org/dev/peps/pep-0590) vektörel çağrı çağrı kuralı: [`map()`](https://docs.python.org/3.10/library/functions.html#map),[`filter()`](https://docs.python.org/3.10/library/functions.html#filter),[`reversed()`](https://docs.python.org/3.10/library/functions.html#reversed),[`bool()`](https://docs.python.org/3.10/library/functions.html#bool)ve[`float()`](https://docs.python.org/3.10/library/functions.html#float). [( Bpo-43575](https://bugs.python.org/issue?@action=redirect\&bpo=43575) ,[ bpo-43287](https://bugs.python.org/issue?@action=redirect\&bpo=43287) ,[ bpo-41922](https://bugs.python.org/issue?@action=redirect\&bpo=41922) ,[ bpo-41873](https://bugs.python.org/issue?@action=redirect\&bpo=41873) ve[ bpo-41870'de](https://bugs.python.org/issue?@action=redirect\&bpo=41870) Dong-hee Na ve Jeroen Demeyer tarafından.)
* `BZ2File`performans dahili kaldırılarak artırılır `RLock`. Bu, her zaman olduğu `BZ2File`gibi eşdeğer sınıfları gibi, aynı anda birden fazla okuyucu veya yazar karşısında iş parçacığını güvensiz hale getirir. (Inada Naoki tarafından [bpo-43785'te katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=43785) .)[`gzip`](https://docs.python.org/3.10/library/gzip.html#module-gzip)[`lzma`](https://docs.python.org/3.10/library/lzma.html#module-lzma)

### Deprecated

* Şu anda Python, örneğin , , gibi anahtar kelimelerin hemen ardından gelen sayısal değişmezleri kabul eder . ( veya olarak yorumlanabilir ) gibi kafa karıştırıcı ve belirsiz ifadelere izin verir . Bu sürümden başlayarak, sayısal değişmezin hemen ardından , , , , , ve anahtar sözcüklerinden biri geliyorsa bir kullanımdan kaldırma uyarısı verilir . Gelecek sürümlerde, sözdizimi uyarısı ve son olarak sözdizimi hatası olarak değiştirilecektir. [(Bpo -43833'te](https://bugs.python.org/issue?@action=redirect\&bpo=43833) Serhiy Storchaka tarafından katkıda bulunulmuştur .)`0in x1or x0if 1else 2[0x1for x in y][0x1 for x in y][0x1f or x in y]`[`and`](https://docs.python.org/3.10/reference/expressions.html#and)[`else`](https://docs.python.org/3.10/reference/compound\_stmts.html#else)[`for`](https://docs.python.org/3.10/reference/compound\_stmts.html#for)[`if`](https://docs.python.org/3.10/reference/compound\_stmts.html#if)[`in`](https://docs.python.org/3.10/reference/expressions.html#in)[`is`](https://docs.python.org/3.10/reference/expressions.html#is)[`or`](https://docs.python.org/3.10/reference/expressions.html#or)
* Bu sürümden başlayarak, Python 2.7 uyumluluğu için tutulan eski içe aktarma anlambilimini temizlemeye başlamak için ortak bir çaba gösterilecektir. Spesifik olarak, [`find_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder.find\_loader)/ [`find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Finder.find\_module) ( yerini alır `find_spec()`), [`load_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.load\_module) ( yerine geçer [`exec_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.exec\_module)), [`module_repr()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.module\_repr)( içe aktarma sisteminin sizin için halleder), `__package__`özniteliği ( ile değiştirir `__spec__.parent`), `__loader__`özniteliği ( ile değiştirir `__spec__.loader`) ve `__cached__`özniteliği ( yerini alır `__spec__.cached`) yavaş yavaş kaldırılacaktır (ve içindeki diğer sınıflar ve yöntemler [`importlib`](https://docs.python.org/3.10/library/importlib.html#module-importlib)). [`ImportWarning`](https://docs.python.org/3.10/library/exceptions.html#ImportWarning)ve/veya [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning)bu geçiş sırasında güncellenmesi gereken kodun belirlenmesine yardımcı olmak için uygun şekilde yükseltilecektir.
* Tüm `distutils`ad alanı, Python 3.12'de kaldırılmak üzere kullanımdan kaldırılmıştır. Daha fazla bilgi için [modül değişiklikleri](https://docs.python.org/3.10/whatsnew/3.10.html#distutils-deprecated) bölümüne bakın.
* Tamsayı olmayan bağımsız değişkenler [`random.randrange()`](https://docs.python.org/3.10/library/random.html#random.randrange)kullanımdan kaldırılmıştır. a [`ValueError`](https://docs.python.org/3.10/library/exceptions.html#ValueError)lehine kullanımdan kaldırılmıştır [`TypeError`](https://docs.python.org/3.10/library/exceptions.html#TypeError). [(Bpo -37319'da](https://bugs.python.org/issue?@action=redirect\&bpo=37319) Serhiy Storchaka ve Raymond Hettinger tarafından katkıda bulunulmuştur .)
* Çeşitli `load_module()`yöntemleri [`importlib`](https://docs.python.org/3.10/library/importlib.html#module-importlib)Python 3.6'dan beri kullanımdan kaldırılmış olarak belgelenmiştir, ancak şimdi bir [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning). Bunun yerine kullanın [`exec_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.exec\_module). [( Bpo-26131'de](https://bugs.python.org/issue?@action=redirect\&bpo=26131) Brett Cannon tarafından katkıda bulunulmuştur .)
* `zimport.zipimporter.load_module()`için tercih edilmemiştir [`exec_module()`](https://docs.python.org/3.10/library/zipimport.html#zipimport.zipimporter.exec\_module). [( Bpo-26131'de](https://bugs.python.org/issue?@action=redirect\&bpo=26131) Brett Cannon tarafından katkıda bulunulmuştur .)
* İthalat sistemi tarafından kullanılması artık tercih edilen bir as [`load_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.load\_module)tetikler . [( Bpo-26131'de](https://bugs.python.org/issue?@action=redirect\&bpo=26131) Brett Cannon tarafından katkıda bulunulmuştur .)[`ImportWarning`](https://docs.python.org/3.10/library/exceptions.html#ImportWarning)[`exec_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.exec\_module)
* İthalat sistemi tarafından ve kullanımı şimdi [`importlib.abc.MetaPathFinder.find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.MetaPathFinder.find\_module)sırasıyla as ve tercih edilir. Taşımada yardımcı olmak için kullanabilirsiniz . [( Bpo-42134'te](https://bugs.python.org/issue?@action=redirect\&bpo=42134) Brett Cannon tarafından katkıda bulunulmuştur .)[`importlib.abc.PathEntryFinder.find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder.find\_module)[`ImportWarning`](https://docs.python.org/3.10/library/exceptions.html#ImportWarning)[`importlib.abc.MetaPathFinder.find_spec()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.MetaPathFinder.find\_spec)[`importlib.abc.PathEntryFinder.find_spec()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder.find\_spec)[`importlib.util.spec_from_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.util.spec\_from\_loader)
* İthalat sistemi tarafından kullanılması artık tercih edilen bir as [`importlib.abc.PathEntryFinder.find_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder.find\_loader)tetikler . Taşımada yardımcı olmak için kullanabilirsiniz . [( Bpo-43672'de](https://bugs.python.org/issue?@action=redirect\&bpo=43672) Brett Cannon tarafından katkıda bulunulmuştur .)[`ImportWarning`](https://docs.python.org/3.10/library/exceptions.html#ImportWarning)[`importlib.abc.PathEntryFinder.find_spec()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder.find\_spec)[`importlib.util.spec_from_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.util.spec\_from\_loader)
* [`importlib.abc.MetaPathFinder.find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.MetaPathFinder.find\_module)( `importlib.machinery.BuiltinImporter.find_module()`, `importlib.machinery.FrozenImporter.find_module()`, `importlib.machinery.WindowsRegistryFinder.find_module()`, [`importlib.machinery.PathFinder.find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.machinery.PathFinder.find\_module), [`importlib.abc.MetaPathFinder.find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.MetaPathFinder.find\_module)), [`importlib.abc.PathEntryFinder.find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder.find\_module)( `importlib.machinery.FileFinder.find_module()`) ve [`importlib.abc.PathEntryFinder.find_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder.find\_loader)( ) öğelerinin çeşitli uygulamaları [`importlib.machinery.FileFinder.find_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.machinery.FileFinder.find\_loader)şimdi yükseltiliyor [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning)ve Python 3.12'de kaldırılmak üzere planlanıyor (önceden Python 3.4'te kullanımdan kaldırıldığı belgelenmişti). [( Bpo-42135'te](https://bugs.python.org/issue?@action=redirect\&bpo=42135) Brett Cannon tarafından katkıda bulunulmuştur .)
* [`importlib.abc.Finder`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Finder)kullanımdan kaldırıldı (tek yöntemi dahil [`find_module()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Finder.find\_module)). Her ikisi de [`importlib.abc.MetaPathFinder`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.MetaPathFinder)artık [`importlib.abc.PathEntryFinder`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.PathEntryFinder) sınıftan miras almaz. Kullanıcılar bunun yerine bu iki sınıftan birinden uygun şekilde miras almalıdır. [( Bpo-42135'te](https://bugs.python.org/issue?@action=redirect\&bpo=42135) Brett Cannon tarafından katkıda bulunulmuştur .)
* [`imp`](https://docs.python.org/3.10/library/imp.html#module-imp), [`importlib.find_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.find\_loader), `importlib.util.set_package_wrapper()`, `importlib.util.set_loader_wrapper()`, [`importlib.util.module_for_loader()`](https://docs.python.org/3.10/library/importlib.html#importlib.util.module\_for\_loader), [`pkgutil.ImpImporter`](https://docs.python.org/3.10/library/pkgutil.html#pkgutil.ImpImporter)ve öğelerinin kullanımdan kaldırılması, [`pkgutil.ImpLoader`](https://docs.python.org/3.10/library/pkgutil.html#pkgutil.ImpLoader)Python 3.12'yi listelenmiş kaldırma sürümü olarak listeleyecek şekilde güncellendi ( [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning)önceki Python sürümlerinde yükselmeye başladılar). [( Bpo-43720'de](https://bugs.python.org/issue?@action=redirect\&bpo=43720) Brett Cannon tarafından katkıda bulunulmuştur .)
* İçe aktarma sistemi artık bir modülün yöntemine `__spec__`geri dönmeden önce modüllerdeki özniteliği kullanır. Python 3.12 için kullanımının kaldırılması planlanmıştır. [( Bpo-42137'de](https://bugs.python.org/issue?@action=redirect\&bpo=42137) Brett Cannon tarafından katkıda bulunulmuştur .)[`module_repr()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.module\_repr)`__repr__()module_repr()`
* [`importlib.abc.Loader.module_repr()`](https://docs.python.org/3.10/library/importlib.html#importlib.abc.Loader.module\_repr), `importlib.machinery.FrozenLoader.module_repr()`, ve `importlib.machinery.BuiltinLoader.module_repr()`Python 3.12'de kullanımdan kaldırıldı ve kaldırılması planlandı. [( Bpo-42136'da](https://bugs.python.org/issue?@action=redirect\&bpo=42136) Brett Cannon tarafından katkıda bulunulmuştur .)
* `sqlite3.OptimizedUnicode`[`str`](https://docs.python.org/3.10/library/stdtypes.html#str). \_ Artık kullanımdan kaldırıldı, Python 3.12'de kaldırılması planlandı. [( Bpo-42264'te](https://bugs.python.org/issue?@action=redirect\&bpo=42264) Erlend E. Aasland tarafından katkıda bulunulmuştur .)
* [`asyncio.get_event_loop()`](https://docs.python.org/3.10/library/asyncio-eventloop.html#asyncio.get\_event\_loop)çalışan bir olay döngüsü yoksa artık bir kullanımdan kaldırma uyarısı veriyor. Gelecekte bir takma adı olacak [`get_running_loop()`](https://docs.python.org/3.10/library/asyncio-eventloop.html#asyncio.get\_running\_loop). [`asyncio`](https://docs.python.org/3.10/library/asyncio.html#module-asyncio)örtük olarak oluşturan işlevler [`Future`](https://docs.python.org/3.10/library/asyncio-future.html#asyncio.Future) veya [`Task`](https://docs.python.org/3.10/library/asyncio-task.html#asyncio.Task)nesneler, çalışan bir olay döngüsü yoksa ve açık bir _döngü_ argümanı iletilmezse artık bir kullanımdan kaldırma uyarısı verir: [`ensure_future()`](https://docs.python.org/3.10/library/asyncio-future.html#asyncio.ensure\_future), [`wrap_future()`](https://docs.python.org/3.10/library/asyncio-future.html#asyncio.wrap\_future), [`gather()`](https://docs.python.org/3.10/library/asyncio-task.html#asyncio.gather), ve yapıcıları [`shield()`](https://docs.python.org/3.10/library/asyncio-task.html#asyncio.shield), , , . [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=39529) 39529'da Serhiy Storchaka tarafından katkıda bulunulmuştur .)[`as_completed()`](https://docs.python.org/3.10/library/asyncio-task.html#asyncio.as\_completed)[`Future`](https://docs.python.org/3.10/library/asyncio-future.html#asyncio.Future)[`Task`](https://docs.python.org/3.10/library/asyncio-task.html#asyncio.Task)[`StreamReader`](https://docs.python.org/3.10/library/asyncio-stream.html#asyncio.StreamReader)`StreamReaderProtocol`
* Belgelenmemiş yerleşik işlev `sqlite3.enable_shared_cache`artık kullanımdan kaldırılmıştır ve Python 3.12'de kaldırılması planlanmıştır. SQLite3 belgeleri tarafından kullanımı kesinlikle önerilmez. Daha fazla ayrıntı için [SQLite3 belgelerine bakın. ](https://sqlite.org/c3ref/enable\_shared\_cache.html)Paylaşılan bir önbellek kullanılması gerekiyorsa, `cache=shared`sorgu parametresini kullanarak veritabanını URI modunda açın. [( Bpo-24464'te](https://bugs.python.org/issue?@action=redirect\&bpo=24464) Erlend E. Aasland tarafından katkıda bulunulmuştur .)
*   Aşağıdaki `threading`yöntemler artık kullanımdan kaldırılmıştır:

    * `threading.currentThread`=>[`threading.current_thread()`](https://docs.python.org/3.10/library/threading.html#threading.current\_thread)
    * `threading.activeCount`=>[`threading.active_count()`](https://docs.python.org/3.10/library/threading.html#threading.active\_count)
    * `threading.Condition.notifyAll`=> [`threading.Condition.notify_all()`](https://docs.python.org/3.10/library/threading.html#threading.Condition.notify\_all)
    * `threading.Event.isSet`=>[`threading.Event.is_set()`](https://docs.python.org/3.10/library/threading.html#threading.Event.is\_set)
    * `threading.Thread.setName`=>[`threading.Thread.name`](https://docs.python.org/3.10/library/threading.html#threading.Thread.name)
    * `threading.thread.getName`=>[`threading.Thread.name`](https://docs.python.org/3.10/library/threading.html#threading.Thread.name)
    * `threading.Thread.isDaemon`=>[`threading.Thread.daemon`](https://docs.python.org/3.10/library/threading.html#threading.Thread.daemon)
    * `threading.Thread.setDaemon`=>[`threading.Thread.daemon`](https://docs.python.org/3.10/library/threading.html#threading.Thread.daemon)

    [( Gh-87889'da](https://github.com/python/cpython/issues/87889) Jelle Zijlstra tarafından katkıda bulunulmuştur .)
* [`pathlib.Path.link_to()`](https://docs.python.org/3.10/library/pathlib.html#pathlib.Path.link\_to)Python 3.12'de kullanımdan kaldırılmıştır ve kaldırılması planlanmıştır. Bunun yerine kullanın [`pathlib.Path.hardlink_to()`](https://docs.python.org/3.10/library/pathlib.html#pathlib.Path.hardlink\_to). [( Bpo-39950'de](https://bugs.python.org/issue?@action=redirect\&bpo=39950) Barney Gale tarafından katkıda bulunulmuştur .)
* `cgi.log()`Python 3.12'de kullanımdan kaldırılmıştır ve kaldırılması planlanmıştır. (Inada Naoki tarafından [bpo-41139'da](https://bugs.python.org/issue?@action=redirect\&bpo=41139) katkıda bulunulmuştur .)
* Aşağıdaki [`ssl`](https://docs.python.org/3.10/library/ssl.html#module-ssl)özellikler Python 3.6, Python 3.7 veya OpenSSL 1.1.0'dan beri kullanımdan kaldırılmıştır ve 3.11'de kaldırılacaktır:
  * [`OP_NO_SSLv2`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_SSLv2), [`OP_NO_SSLv3`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_SSLv3), [`OP_NO_TLSv1`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_TLSv1), [`OP_NO_TLSv1_1`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_TLSv1\_1), [`OP_NO_TLSv1_2`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_TLSv1\_2)ve [`OP_NO_TLSv1_3`](https://docs.python.org/3.10/library/ssl.html#ssl.OP\_NO\_TLSv1\_3)ile değiştirilir `sslSSLContext.minimum_version`ve `sslSSLContext.maximum_version`.
  * [`PROTOCOL_SSLv2`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_SSLv2), [`PROTOCOL_SSLv3`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_SSLv3), [`PROTOCOL_SSLv23`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_SSLv23), [`PROTOCOL_TLSv1`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_TLSv1), [`PROTOCOL_TLSv1_1`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_TLSv1\_1), [`PROTOCOL_TLSv1_2`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_TLSv1\_2)ve ve [`PROTOCOL_TLS`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_TLS)lehine kullanımdan kaldırıldı [`PROTOCOL_TLS_CLIENT`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_TLS\_CLIENT)ve[`PROTOCOL_TLS_SERVER`](https://docs.python.org/3.10/library/ssl.html#ssl.PROTOCOL\_TLS\_SERVER)
  * [`wrap_socket()`](https://docs.python.org/3.10/library/ssl.html#ssl.wrap\_socket)ile değiştirilir[`ssl.SSLContext.wrap_socket()`](https://docs.python.org/3.10/library/ssl.html#ssl.SSLContext.wrap\_socket)
  * [`match_hostname()`](https://docs.python.org/3.10/library/ssl.html#ssl.match\_hostname)
  * [`RAND_pseudo_bytes()`](https://docs.python.org/3.10/library/ssl.html#ssl.RAND\_pseudo\_bytes), `RAND_egd()`
  * Gibi NPN özellikleri ALPN ile değiştirilir [`ssl.SSLSocket.selected_npn_protocol()`](https://docs.python.org/3.10/library/ssl.html#ssl.SSLSocket.selected\_npn\_protocol). [`ssl.SSLContext.set_npn_protocols()`](https://docs.python.org/3.10/library/ssl.html#ssl.SSLContext.set\_npn\_protocols)
* İş parçacığı hata ayıklaması ([`PYTHONTHREADDEBUG`](https://docs.python.org/3.10/using/cmdline.html#envvar-PYTHONTHREADDEBUG)ortam değişkeni) Python 3.10'da kullanımdan kaldırılmıştır ve Python 3.12'de kaldırılacaktır. Bu özellik [, Python'un hata ayıklama yapısını](https://docs.python.org/3.10/using/configure.html#debug-build) gerektirir . [(Bpo -44584'te](https://bugs.python.org/issue?@action=redirect\&bpo=44584) Victor Stinner tarafından katkıda bulunulmuştur .)
* `typing.io`ve alt modüllerinden içe aktarma `typing.re`artık [`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning). Bu alt modüller, Python'un gelecekteki bir sürümünde kaldırılacaktır. Bu alt modüllere ait olan herhangi bir şey doğrudan [`typing`](https://docs.python.org/3.10/library/typing.html#module-typing)bunun yerine içe aktarılmalıdır. [(Bpo -38291'de](https://bugs.python.org/issue?@action=redirect\&bpo=38291) Sebastian Rittau tarafından katkıda bulunulmuştur .)

### Removed

* Özel yöntemler `__int__`, `__float__`, `__floordiv__`, `__mod__`, `__divmod__`, `__rfloordiv__`ve sınıfın kaldırıldı . Her zaman bir yükselttiler . [(Bpo -41974'te](https://bugs.python.org/issue?@action=redirect\&bpo=41974) Serhiy Storchaka tarafından katkıda bulunulmuştur .)`__rmod____rdivmod__`[`complex`](https://docs.python.org/3.10/library/functions.html#complex)[`TypeError`](https://docs.python.org/3.10/library/exceptions.html#TypeError)
* Özel `ParserBase.error()`ve belgesiz `_markupbase` modülden yöntem kaldırıldı. [`html.parser.HTMLParser`](https://docs.python.org/3.10/library/html.parser.html#html.parser.HTMLParser)tek alt sınıfıdır `ParserBase`ve `error()`uygulaması Python 3.5'te zaten kaldırılmıştır. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=31844) 31844'te Berker Peksağ tarafından katkıda bulunulmuştur .)
* `unicodedata.ucnhash_CAPI`Dahili bir PyCapsule nesnesi olan öznitelik kaldırıldı . İlgili özel `_PyUnicode_Name_CAPI`yapı, dahili C API'sine taşındı. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=42157) 42157'de Victor Stinner tarafından katkıda bulunulmuştur .)
* Yeni PEG ayrıştırıcısına geçiş nedeniyle 3.9'da kullanımdan kaldırılan `parser`modülün yanı sıra yalnızca eski ayrıştırıcı tarafından kullanılan tüm C kaynak ve başlık dosyaları `node.h`, `parser.h`, `graminit.h`ve `grammar.h`.
* Yeni PEG ayrıştırıcısına geçiş nedeniyle 3.9'da kullanımdan kaldırılan Genel C API işlevleri `PyParser_SimpleParseStringFlags`, `PyParser_SimpleParseStringFlagsFilename`ve `PyParser_SimpleParseFileFlags`kaldırıldı .`PyNode_Compile`
* `formatter`Python 3.4'te kullanımdan kaldırılan modül kaldırıldı. Biraz eski, az kullanılmış ve test edilmemiştir. Başlangıçta Python 3.6'da kaldırılması planlanmıştı, ancak bu tür kaldırma işlemleri Python 2.7 EOL sonrasına kadar ertelendi. Mevcut kullanıcılar, kullandıkları sınıfları kodlarına kopyalamalıdır. [( Bpo-42299'da](https://bugs.python.org/issue?@action=redirect\&bpo=42299) Dong-hee Na ve Terry J. Reedy tarafından katkıda bulunulmuştur .)
* `PyModule_GetWarningsModule()`\_warnings modülü nedeniyle artık işe yaramaz olan işlev kaldırıldı, 2.6'da yerleşik bir modüle dönüştürüldü. [( Bpo-42599'da](https://bugs.python.org/issue?@action=redirect\&bpo=42599) Hai Shi tarafından katkıda bulunulmuştur .)
* Modülden [Collections Abstract Base Classes](https://docs.python.org/3.10/library/collections.abc.html#collections-abstract-base-classes) için kullanımdan kaldırılan takma adları kaldırın . [(Bpo -37324'te](https://bugs.python.org/issue?@action=redirect\&bpo=37324)[`collections`](https://docs.python.org/3.10/library/collections.html#module-collections) Victor Stinner tarafından katkıda bulunulmuştur .)
*   Parametre , Python 3.8'de kullanımdan `loop`kaldırıldıktan sonra çoğu [üst düzey API'sinden kaldırıldı. ](https://docs.python.org/3.10/library/asyncio-api-index.html)Bu değişikliğin arkasındaki motivasyon çok yönlüdür:[`asyncio`](https://docs.python.org/3.10/library/asyncio.html#module-asyncio)

    1. Bu, üst düzey API'yi basitleştirir.
    2. Üst düzey API'deki işlevler, Python 3.7'den bu yana geçerli iş parçacığının çalışan olay döngüsünü örtük olarak alıyor. Çoğu normal kullanım durumunda olay döngüsünü API'ye geçirmeye gerek yoktur.
    3. Olay döngüsü geçişi, özellikle farklı iş parçacıklarında çalışan döngülerle uğraşırken hataya açıktır.

    Düşük seviyeli API'nin yine de kabul edeceğini unutmayın `loop`. Mevcut kodun nasıl değiştirileceğine ilişkin örnekler [için Python API'sindeki Değişikliklere](https://docs.python.org/3.10/whatsnew/3.10.html#changes-python-api) bakın .

    [( Bpo-42392'de](https://bugs.python.org/issue?@action=redirect\&bpo=42392) Yurii Karabas, Andrew Svetlov, Yury Selivanov ve Kyle Stanley tarafından katkıda bulunulmuştur .)

### Python 3.10'a taşıma&#x20;

Bu bölüm, daha önce açıklanan değişiklikleri ve kodunuzda değişiklik gerektirebilecek diğer hata düzeltmelerini listeler.

#### Python söz dizimindeki değişiklikler&#x20;

* Sayısal değişmezin hemen ardından bir anahtar sözcük geliyorsa ( 'de olduğu gibi ), önceden geçerli sözdizimi derlenirken artık kullanımdan kaldırma uyarısı çıkıyor. Gelecek sürümlerde sözdizimi uyarısına ve son olarak sözdizimi hatasına değiştirilecektir. Uyarıdan kurtulmak ve kodu gelecekteki sürümlerle uyumlu hale getirmek için sayısal değişmez ile aşağıdaki anahtar kelime arasına bir boşluk eklemeniz yeterlidir. [(Bpo -43833'te](https://bugs.python.org/issue?@action=redirect\&bpo=43833) Serhiy Storchaka tarafından katkıda bulunulmuştur .)`0in x`

#### Python API'sindeki değişiklikler&#x20;

* Modüldeki , ve fonksiyonlarının _etype_ parametreleri _exc_ olarak yeniden adlandırıldı . [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=26389) 26389'da Zackery Spytz ve Matthias Bussonnier tarafından katkıda bulunulmuştur .)[`format_exception()`](https://docs.python.org/3.10/library/traceback.html#traceback.format\_exception)[`format_exception_only()`](https://docs.python.org/3.10/library/traceback.html#traceback.format\_exception\_only)[`print_exception()`](https://docs.python.org/3.10/library/traceback.html#traceback.print\_exception)[`traceback`](https://docs.python.org/3.10/library/traceback.html#module-traceback)
* [`atexit`](https://docs.python.org/3.10/library/atexit.html#module-atexit): Python çıkışında, kayıtlı bir geri arama [`atexit.register()`](https://docs.python.org/3.10/library/atexit.html#atexit.register)başarısız olursa, istisnası şimdi günlüğe kaydedilir. Önceden, yalnızca bazı istisnalar günlüğe kaydedilirdi ve son istisna her zaman sessizce yok sayılırdı. [(Bpo -42639'da](https://bugs.python.org/issue?@action=redirect\&bpo=42639) Victor Stinner tarafından katkıda bulunulmuştur .)
* [`collections.abc.Callable`](https://docs.python.org/3.10/library/collections.abc.html#collections.abc.Callable)jenerik şimdi, şu anda ne yaptığına benzer şekilde, tür parametrelerini düzleştirir [`typing.Callable`](https://docs.python.org/3.10/library/typing.html#typing.Callable). Bu, sahip olacağı anlamına gelir ; önceden bu vardı . Bu değişikliği hesaba katması gereken veya argümanlara erişen kod . Ayrıca, Python 3.9'da sessizce geçmiş olabilecek geçersiz parametreleştirme biçimleri için yükseltilebilir . [( Bpo-42195'te](https://bugs.python.org/issue?@action=redirect\&bpo=42195) Ken Jin tarafından katkıda bulunulmuştur .)`collections.abc.Callable[[int, str], str]__args__(int, str, str)([int, str], str)`[`typing.get_args()`](https://docs.python.org/3.10/library/typing.html#typing.get\_args)`__args__`[`TypeError`](https://docs.python.org/3.10/library/exceptions.html#TypeError)[`collections.abc.Callable`](https://docs.python.org/3.10/library/collections.abc.html#collections.abc.Callable)
* [`socket.htons()`](https://docs.python.org/3.10/library/socket.html#socket.htons)ve şimdi , verilen parametre 16 bitlik işaretsiz bir tamsayıya sığmayacaksa yerine [`socket.ntohs()`](https://docs.python.org/3.10/library/socket.html#socket.ntohs)yükseltin . [( Bpo-42393'te](https://bugs.python.org/issue?@action=redirect\&bpo=42393) Erlend E. Aasland tarafından katkıda bulunulmuştur .)[`OverflowError`](https://docs.python.org/3.10/library/exceptions.html#OverflowError)[`DeprecationWarning`](https://docs.python.org/3.10/library/exceptions.html#DeprecationWarning)
*   Parametre , Python 3.8'de kullanımdan `loop`kaldırıldıktan sonra çoğu [üst düzey API'sinden kaldırıldı.](https://docs.python.org/3.10/library/asyncio-api-index.html)[`asyncio`](https://docs.python.org/3.10/library/asyncio.html#module-asyncio)

    Şu anda şuna benzeyen bir eşyordam:

    ```
    async def foo(loop):
        await asyncio.sleep(1, loop=loop)
    ```

    Bununla değiştirilmelidir:

    ```
    async def foo():
        await asyncio.sleep(1)
    ```

    Geçerli iş parçacığının çalışan olay döngüsünde çalışmayacak şekilde özel olarak tasarlanmışsa (örneğin, başka bir iş parçacığının olay döngüsünde çalıştırılıyor), _bunun_`foo()` yerine kullanmayı düşünün .[`asyncio.run_coroutine_threadsafe()`](https://docs.python.org/3.10/library/asyncio-task.html#asyncio.run\_coroutine\_threadsafe)

    [( Bpo-42392'de](https://bugs.python.org/issue?@action=redirect\&bpo=42392) Yurii Karabas, Andrew Svetlov, Yury Selivanov ve Kyle Stanley tarafından katkıda bulunulmuştur .)
* _Globals_ sözlüğünde yerleşik olarak kullanmak yerine anahtar yoksa , [`types.FunctionType`](https://docs.python.org/3.10/library/types.html#types.FunctionType)yapıcı şimdi geçerli yerleşikleri devralır : ve işlevlerle aynı davranış . Python'da bir işlevin tanımlanması etkilenmez, globaller bu sözdizimi ile geçersiz kılınamaz: aynı zamanda mevcut yerleşikleri de devralır. [(Bpo -42990'da](https://bugs.python.org/issue?@action=redirect\&bpo=42990) Victor Stinner tarafından katkıda bulunulmuştur .)`"__builtins__"{"None": None}`[`eval()`](https://docs.python.org/3.10/library/functions.html#eval)[`exec()`](https://docs.python.org/3.10/library/functions.html#exec)`def function(...): ...`

#### C API'deki değişiklikler&#x20;

*   C API işlevleri `PyParser_SimpleParseStringFlags`, `PyParser_SimpleParseStringFlagsFilename`, ve bu işlevler tarafından kullanılan tür `PyParser_SimpleParseFileFlags`, yeni PEG ayrıştırıcısına geçiş nedeniyle kaldırıldı.`PyNode_Compilestruct _node`

    Kaynak şimdi, örneğin, kullanılarak doğrudan bir kod nesnesine derlenmelidir [`Py_CompileString()`](https://docs.python.org/3.10/c-api/veryhigh.html#c.Py\_CompileString). Ortaya çıkan kod nesnesi daha sonra örneğin kullanılarak değerlendirilebilir [`PyEval_EvalCode()`](https://docs.python.org/3.10/c-api/veryhigh.html#c.PyEval\_EvalCode).

    özellikle:

    * `PyParser_SimpleParseStringFlags`Takip eden bir arama, `PyNode_Compile`arama ile değiştirilebilir [`Py_CompileString()`](https://docs.python.org/3.10/c-api/veryhigh.html#c.Py\_CompileString).
    * için doğrudan değiştirme yoktur `PyParser_SimpleParseFileFlags`. Bir argümandan kod derlemek için dosyayı C'de okumanız ve elde edilen arabelleği .`FILE *`[`Py_CompileString()`](https://docs.python.org/3.10/c-api/veryhigh.html#c.Py\_CompileString)
    *   Bir dosya adı verilen bir dosyayı derlemek için dosyayı açıkça açın, okuyun ve sonucu derleyin. Bunu yapmanın bir yolu, modülü aşağıda gösterildiği gibi , , ve ile kullanmaktır. (Beyanlar ve hata işleme atlanmıştır.)`char *`[`io`](https://docs.python.org/3.10/library/io.html#module-io)[`PyImport_ImportModule()`](https://docs.python.org/3.10/c-api/import.html#c.PyImport\_ImportModule)[`PyObject_CallMethod()`](https://docs.python.org/3.10/c-api/call.html#c.PyObject\_CallMethod)[`PyBytes_AsString()`](https://docs.python.org/3.10/c-api/bytes.html#c.PyBytes\_AsString)[`Py_CompileString()`](https://docs.python.org/3.10/c-api/veryhigh.html#c.Py\_CompileString)

        ```
        io_module = Import_ImportModule("io");
        fileobject = PyObject_CallMethod(io_module, "open", "ss", filename, "rb");
        source_bytes_object = PyObject_CallMethod(fileobject, "read", "");
        result = PyObject_CallMethod(fileobject, "close", "");
        source_buf = PyBytes_AsString(source_bytes_object);
        code = Py_CompileString(source_buf, filename, Py_file_input);
        ```
    * `FrameObject`Nesneler için, üye `f_lasti`artık bayt kodu dizesine basit bir uzaklık yerine bir sözcük kodu uzaklığını temsil ediyor. Bu, bunun yerine bir bayt ofseti bekleyen API'lerde kullanılmak üzere bu sayının 2 ile çarpılması gerektiği anlamına gelir ( [`PyCode_Addr2Line()`](https://docs.python.org/3.10/c-api/code.html#c.PyCode\_Addr2Line)örneğin,). `f_lasti`Ayrıca nesnelerin üyesinin `FrameObject`kararlı olarak kabul edilmediğine dikkat edin : lütfen [`PyFrame_GetLineNumber()`](https://docs.python.org/3.10/c-api/reflection.html#c.PyFrame\_GetLineNumber)bunun yerine kullanın.

### CPython bayt kodu değişiklikleri&#x20;

* `MAKE_FUNCTION`Komut artık işlevin açıklamaları olarak bir dict veya bir dizi dizeyi kabul eder . [( Bpo-42202'de](https://bugs.python.org/issue?@action=redirect\&bpo=42202) Yurii Karabas ve Inada Naoki tarafından katkıda bulunulmuştur .)

### Değişiklikleri Oluştur&#x20;

* [**PEP 644**](https://www.python.org/dev/peps/pep-0644) : Python artık OpenSSL 1.1.1 veya daha yenisini gerektiriyor. OpenSSL 1.0.2 artık desteklenmiyor. [(Bpo -43669'da](https://bugs.python.org/issue?@action=redirect\&bpo=43669) Christian Heimes tarafından katkıda.)
* C99 çalışır `snprintf()`ve `vsnprintf()`şimdi Python'u oluşturmak için gereklidir. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=36020) 36020'de Victor Stinner tarafından katkıda bulunulmuştur .)
* [`sqlite3`](https://docs.python.org/3.10/library/sqlite3.html#module-sqlite3)SQLite 3.7.15 veya üstünü gerektirir. [( Bpo-40744](https://bugs.python.org/issue?@action=redirect\&bpo=40744) ve [bpo-40810'da](https://bugs.python.org/issue?@action=redirect\&bpo=40810) Sergey Fedoseev ve Erlend E. Aasland tarafından katkıda bulunulmuştur .)
* Modül [`atexit`](https://docs.python.org/3.10/library/atexit.html#module-atexit)şimdi her zaman yerleşik bir modül olarak oluşturulmalıdır. [(Bpo -42639'da](https://bugs.python.org/issue?@action=redirect\&bpo=42639) Victor Stinner tarafından katkıda bulunulmuştur .)
* [`--disable-test-modules`](https://docs.python.org/3.10/using/configure.html#cmdoption-disable-test-modules)Komut dosyasına seçenek ekleyin `configure`: test modülleri oluşturmayın veya kurmayın. [( bpo-](https://bugs.python.org/issue?@action=redirect\&bpo=27640) 27640'da Xavier de Gaye, Thomas Petazzoni ve Peixing Xin tarafından katkıda bulunulmuştur .)
*   Komut dosyasına ekleyin . Belirtilmişse, modül bu dizinde tekerlek paketlerini arar ve her ikisi de mevcutsa, bu paketler, garantili paket tekerlek paketleri yerine kullanılır.[`--with-wheel-pkg-dir=PATH option`](https://docs.python.org/3.10/using/configure.html#cmdoption-with-wheel-pkg-dir)`./configure`[`ensurepip`](https://docs.python.org/3.10/library/ensurepip.html#module-ensurepip)`setuptoolspip`

    Bazı Linux dağıtım paketleme ilkeleri, paket bağımlılıklarına karşı tavsiyede bulunur. Örneğin, Fedora `/usr/share/python-wheels/`dizine tekerlek paketleri kurar ve `ensurepip._bundled`paketi kurmaz.

    [(Bpo -42856'da](https://bugs.python.org/issue?@action=redirect\&bpo=42856) Victor Stinner tarafından katkıda bulunulmuştur .)
*   Statik kitaplığı oluşturmamak ve nesne dosyasını yüklememek için yeni bir tane ekleyin .[`configure --without-static-libpython option`](https://docs.python.org/3.10/using/configure.html#cmdoption-without-static-libpython)`libpythonMAJOR.MINOR.apython.o`

    [(Bpo -43103'te](https://bugs.python.org/issue?@action=redirect\&bpo=43103) Victor Stinner tarafından katkıda bulunulmuştur .)
* `configure`Komut dosyası `pkg-config`, varsa, Tcl/Tk üstbilgilerinin ve kitaplıklarının konumunu algılamak için yardımcı programı kullanır . [`--with-tcltk-includes`](https://docs.python.org/3.10/using/configure.html#cmdoption-with-tcltk-includes) Daha önce olduğu gibi, bu konumlar ve [`--with-tcltk-libs`](https://docs.python.org/3.10/using/configure.html#cmdoption-with-tcltk-libs)yapılandırma seçenekleriyle açıkça belirtilebilir . [(Bpo -42603'te](https://bugs.python.org/issue?@action=redirect\&bpo=42603) Manolis Stamatogiannakis tarafından katkıda bulunulmuştur .)
* Komut dosyasına [`--with-openssl-rpath`](https://docs.python.org/3.10/using/configure.html#cmdoption-with-openssl-rpath)seçenek ekleyin . `configure`Seçenek, özel bir OpenSSL kurulumu ile Python oluşturmayı basitleştirir, örn . [(Bpo -43466'da](https://bugs.python.org/issue?@action=redirect\&bpo=43466) Christian Heimes tarafından katkıda bulunulmuştur .)`./configure --with-openssl=/path/to/openssl --with-openssl-rpath=auto`

### C API Değişiklikleri&#x20;

#### PEP 652: Kararlı ABI'nin Korunması&#x20;

Genişletme modülleri veya gömme Python için Kararlı ABI (Uygulama İkili Arayüzü) artık açıkça tanımlanmıştır. [C API Kararlılığı](https://docs.python.org/3.10/c-api/stable.html#stable) , Kararlı ABI kullanımına yönelik en iyi uygulamalarla birlikte C API ve ABI kararlılık garantilerini açıklar.

(Petr Viktorin tarafından[**PEP 652**](https://www.python.org/dev/peps/pep-0652) ve[ bpo-43795](https://bugs.python.org/issue?@action=redirect\&bpo=43795) .)

#### Yeni Özellikler&#x20;

* Şimdi sonucunun [`PyNumber_Index()`](https://docs.python.org/3.10/c-api/number.html#c.PyNumber\_Index)her zaman kesin türü vardır [`int`](https://docs.python.org/3.10/library/functions.html#int). Önceden, sonuç bir alt sınıfın örneği olabilirdi `int`. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=40792) 40792'de Serhiy Storchaka tarafından katkıda bulunulmuştur .)
* [`orig_argv`](https://docs.python.org/3.10/c-api/init\_config.html#c.PyConfig.orig\_argv)Yapıya yeni bir üye ekleyin [`PyConfig`](https://docs.python.org/3.10/c-api/init\_config.html#c.PyConfig) : Python yürütülebilir dosyasına iletilen orijinal komut satırı argümanlarının listesi. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=23427) 23427'de Victor Stinner tarafından katkıda bulunulmuştur .)
* ve nesnelerinin özelliklerine erişmek için ve makroları eklendi [`PyDateTime_DATE_GET_TZINFO()`](https://docs.python.org/3.10/c-api/datetime.html#c.PyDateTime\_DATE\_GET\_TZINFO). [(Bpo -30155'te](https://bugs.python.org/issue?@action=redirect\&bpo=30155) Zackery Spytz tarafından katkıda bulunulmuştur .)[`PyDateTime_TIME_GET_TZINFO()`](https://docs.python.org/3.10/c-api/datetime.html#c.PyDateTime\_TIME\_GET\_TZINFO)`tzinfo`[`datetime.datetime`](https://docs.python.org/3.10/library/datetime.html#datetime.datetime)[`datetime.time`](https://docs.python.org/3.10/library/datetime.html#datetime.time)
* [`PyCodec_Unregister()`](https://docs.python.org/3.10/c-api/codec.html#c.PyCodec\_Unregister)Bir codec bileşeni arama işlevinin kaydını silmek için bir işlev ekleyin . [( Bpo-41842'de](https://bugs.python.org/issue?@action=redirect\&bpo=41842) Hai Shi tarafından katkıda bulunulmuştur .)
* İşlev , istisna [`PyIter_Send()`](https://docs.python.org/3.10/c-api/iter.html#c.PyIter\_Send)oluşturmadan yineleyiciye değer gönderilmesine izin vermek için eklendi . [( Bpo-41756'da](https://bugs.python.org/issue?@action=redirect\&bpo=41756)`StopIteration` Vladimir Matveev tarafından katkıda bulunulmuştur .)
* [`PyUnicode_AsUTF8AndSize()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_AsUTF8AndSize)Sınırlı C API'sine ekleyin . [( Bpo-41784'te](https://bugs.python.org/issue?@action=redirect\&bpo=41784) Alex Gaynor tarafından katkıda bulunulmuştur .)
* İşlev ekle [`PyModule_AddObjectRef()`](https://docs.python.org/3.10/c-api/module.html#c.PyModule\_AddObjectRef): benzer, [`PyModule_AddObject()`](https://docs.python.org/3.10/c-api/module.html#c.PyModule\_AddObject)ancak başarı değerine ilişkin bir referans çalma. [(Bpo -1635741'de](https://bugs.python.org/issue?@action=redirect\&bpo=1635741) Victor Stinner tarafından katkıda bulunulmuştur .)
* Bir nesnenin referans sayısını artırmak ve nesneyi döndürmek için ekleme [`Py_NewRef()`](https://docs.python.org/3.10/c-api/refcounting.html#c.Py\_NewRef)ve işlevler. [(Bpo -42262'de](https://bugs.python.org/issue?@action=redirect\&bpo=42262)[`Py_XNewRef()`](https://docs.python.org/3.10/c-api/refcounting.html#c.Py\_XNewRef) Victor Stinner tarafından katkıda bulunulmuştur .)
* ve işlevleri artık _temel_ argüman [`PyType_FromSpecWithBases()`](https://docs.python.org/3.10/c-api/type.html#c.PyType\_FromSpecWithBases)olarak tek bir sınıfı kabul eder. [(Bpo -42423'te](https://bugs.python.org/issue?@action=redirect\&bpo=42423) Serhiy Storchaka tarafından katkıda bulunulmuştur .)[`PyType_FromModuleAndSpec()`](https://docs.python.org/3.10/c-api/type.html#c.PyType\_FromModuleAndSpec)
* İşlev [`PyType_FromModuleAndSpec()`](https://docs.python.org/3.10/c-api/type.html#c.PyType\_FromModuleAndSpec)artık NULL `tp_doc` yuvayı kabul eder. [( Bpo-41832'de](https://bugs.python.org/issue?@action=redirect\&bpo=41832) Hai Shi tarafından katkıda bulunulmuştur .)
* İşlev , [statik türleri](https://docs.python.org/3.10/c-api/typeobj.html#static-types)[`PyType_GetSlot()`](https://docs.python.org/3.10/c-api/type.html#c.PyType\_GetSlot) kabul edebilir . [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=41073) 41073'te Hai Shi ve Petr Viktorin tarafından katkıda bulunulmuştur .)
* [`PySet_CheckExact()`](https://docs.python.org/3.10/c-api/set.html#c.PySet\_CheckExact)Bir nesnenin [`set`](https://docs.python.org/3.10/library/stdtypes.html#set)bir alt türün örneği olup olmadığını kontrol etmek için C-API'ye yeni bir işlev ekleyin . [( Bpo-43277'de](https://bugs.python.org/issue?@action=redirect\&bpo=43277) Pablo Galindo tarafından katkıda bulunulmuştur .)
* [`PyErr_SetInterruptEx()`](https://docs.python.org/3.10/c-api/exceptions.html#c.PyErr\_SetInterruptEx)Simüle etmek için bir sinyal numarasının iletilmesine izin veren ekleyin . [(Bpo -43356'da](https://bugs.python.org/issue?@action=redirect\&bpo=43356) Antoine Pitrou tarafından katkıda bulunulmuştur .)
*   [Python hata ayıklama modunda yerleşikse](https://docs.python.org/3.10/using/configure.html#debug-build) ( `Py_DEBUG`makro tanımlanmışsa) artık sınırlı C API'si desteklenmektedir . Sınırlı C API'sinde [`Py_INCREF()`](https://docs.python.org/3.10/c-api/refcounting.html#c.Py\_INCREF)ve işlevleri , Python hata ayıklama modunda yerleşikse ve makro Python 3.10 veya daha yenisini hedefliyorsa [`Py_DECREF()`](https://docs.python.org/3.10/c-api/refcounting.html#c.Py\_DECREF), üyeye doğrudan erişmek yerine artık opak işlev çağrıları olarak uygulanır . Python 3.8'den beri sürüm ve hata ayıklama modunda yapı aynı olduğu için hata ayıklama modunda sınırlı C API'sini desteklemek mümkün hale geldi (bkz. [bpo-36465](https://bugs.python.org/issue?@action=redirect\&bpo=36465) ).[`PyObject.ob_refcnt`](https://docs.python.org/3.10/c-api/typeobj.html#c.PyObject.ob\_refcnt)`Py_LIMITED_API`[`PyObject`](https://docs.python.org/3.10/c-api/structures.html#c.PyObject)

    [`--with-trace-refs`](https://docs.python.org/3.10/using/configure.html#cmdoption-with-trace-refs) Sınırlı C API, özel derlemede ( `Py_TRACE_REFS`makro) hala desteklenmemektedir . [(Bpo -43688'de](https://bugs.python.org/issue?@action=redirect\&bpo=43688) Victor Stinner tarafından katkıda bulunulmuştur .)
* Python'da olduğu gibi _x_ nesnesinin _y_ nesnesi olup olmadığını test etmek için işlevi ekleyin . Bir nesnenin sırasıyla singleton, singleton veya singleton olup olmadığını test etmek için , , fonksiyonlarını da ekleyin . [(Bpo -43753'te](https://bugs.python.org/issue?@action=redirect\&bpo=43753) Victor Stinner tarafından katkıda bulunulmuştur .)[`Py_Is(x, y)`](https://docs.python.org/3.10/c-api/structures.html#c.Py\_Is)`x is y`[`Py_IsNone()`](https://docs.python.org/3.10/c-api/structures.html#c.Py\_IsNone)[`Py_IsTrue()`](https://docs.python.org/3.10/c-api/structures.html#c.Py\_IsTrue)[`Py_IsFalse()`](https://docs.python.org/3.10/c-api/structures.html#c.Py\_IsFalse)`NoneTrueFalse`
* Çöp toplayıcıyı C kodundan kontrol etmek için yeni işlevler ekleyin: [`PyGC_Enable()`](https://docs.python.org/3.10/c-api/gcsupport.html#c.PyGC\_Enable), [`PyGC_Disable()`](https://docs.python.org/3.10/c-api/gcsupport.html#c.PyGC\_Disable), [`PyGC_IsEnabled()`](https://docs.python.org/3.10/c-api/gcsupport.html#c.PyGC\_IsEnabled). [`gc`](https://docs.python.org/3.10/library/gc.html#module-gc)Bu işlevler, modülü içe aktarmaya gerek kalmadan çöp toplayıcının durumunu C kodundan etkinleştirmeye, devre dışı bırakmaya ve sorgulamaya izin verir .
* `Py_TPFLAGS_DISALLOW_INSTANTIATION`Tür örnekleri oluşturmaya izin vermemek için yeni bir tür bayrağı ekleyin . [(Bpo -43916'da](https://bugs.python.org/issue?@action=redirect\&bpo=43916) Victor Stinner tarafından katkıda bulunulmuştur .)
* Değiştirilemez tür nesneleri oluşturmak için yeni bir tür bayrağı ekleyin `Py_TPFLAGS_IMMUTABLETYPE`: tür nitelikleri ayarlanamaz veya silinemez. [(Bpo -43908'de](https://bugs.python.org/issue?@action=redirect\&bpo=43908) Victor Stinner ve Erlend E. Aasland tarafından katkıda bulunulmuştur .)

#### Python 3.10'a taşıma&#x20;

* Makronun şimdi : , , , , , , ve kullanan formatları kullanmak için `PY_SSIZE_T_CLEAN`tanımlanması gerekir . Bkz. [Argümanları ayrıştırma ve değerleri oluşturma](https://docs.python.org/3.10/c-api/arg.html#arg-parsing) ve[`PyArg_ParseTuple()`](https://docs.python.org/3.10/c-api/arg.html#c.PyArg\_ParseTuple)[`Py_BuildValue()`](https://docs.python.org/3.10/c-api/arg.html#c.Py\_BuildValue)`#es#et#s#u#y#z#U#Z#`[**PEP 353**](https://www.python.org/dev/peps/pep-0353) . [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=40943) 40943'te Victor Stinner tarafından katkıda bulunulmuştur.)
*   [`Py_REFCNT()`](https://docs.python.org/3.10/c-api/structures.html#c.Py\_REFCNT)Satır içi statik işleve değiştirildiğinden, şununla değiştirilmelidir : bkz (Python 3.9'dan beri mevcuttur) . Geriye dönük uyumluluk için bu makro kullanılabilir:`Py_REFCNT(obj) = new_refcntPy_SET_REFCNT(obj, new_refcnt)`[`Py_SET_REFCNT()`](https://docs.python.org/3.10/c-api/structures.html#c.Py\_SET\_REFCNT)

    ```
    #if PY_VERSION_HEX < 0x030900A4
    #  define Py_SET_REFCNT(obj, refcnt) ((Py_REFCNT(obj) = (refcnt)), (void)0)
    #endif
    ```

    [(Bpo -39573'te](https://bugs.python.org/issue?@action=redirect\&bpo=39573) Victor Stinner tarafından katkıda bulunulmuştur .)
* [GIL](https://docs.python.org/3.10/glossary.html#term-GIL)[`PyDict_GetItem()`](https://docs.python.org/3.10/c-api/dict.html#c.PyDict\_GetItem) yapılmadan arama yapılmasına tarihsel nedenle izin verilmişti. Artık izin verilmiyor. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=40839) 40839'da Victor Stinner tarafından katkıda bulunulmuştur .)
* `PyUnicode_FromUnicode(NULL, size)`ve şimdi yükselt . Unicode nesnesini başlangıç ​​verileri olmadan tahsis etmek için kullanın . (Inada Naoki tarafından [bpo-36346'da katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=36346) .)`PyUnicode_FromStringAndSize(NULL, size)DeprecationWarning`[`PyUnicode_New()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_New)
* PyCapsule API'sinin özel `_PyUnicode_Name_CAPI`yapısı `unicodedata.ucnhash_CAPI`, dahili C API'sine taşındı. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=42157) 42157'de Victor Stinner tarafından katkıda bulunulmuştur .)
* [`Py_GetPath()`](https://docs.python.org/3.10/c-api/init.html#c.Py\_GetPath), [`Py_GetPrefix()`](https://docs.python.org/3.10/c-api/init.html#c.Py\_GetPrefix), [`Py_GetExecPrefix()`](https://docs.python.org/3.10/c-api/init.html#c.Py\_GetExecPrefix), ve işlevleri, daha önce çağrıldıysa (Python başlatılmadan önce) şimdi [`Py_GetProgramFullPath()`](https://docs.python.org/3.10/c-api/init.html#c.Py\_GetProgramFullPath)geri döner . [Python Yolu Yapılandırmasını](https://docs.python.org/3.10/c-api/init\_config.html#init-path-config) almak için yeni [Python Başlatma Yapılandırma](https://docs.python.org/3.10/c-api/init\_config.html#init-config) API'sini kullanın . [(Bpo -42260'da](https://bugs.python.org/issue?@action=redirect\&bpo=42260) Victor Stinner tarafından katkıda bulunulmuştur .)[`Py_GetPythonHome()`](https://docs.python.org/3.10/c-api/init.html#c.Py\_GetPythonHome)[`Py_GetProgramName()`](https://docs.python.org/3.10/c-api/init.html#c.Py\_GetProgramName)`NULL`[`Py_Initialize()`](https://docs.python.org/3.10/c-api/init.html#c.Py\_Initialize)
* [`PyList_SET_ITEM()`](https://docs.python.org/3.10/c-api/list.html#c.PyList\_SET\_ITEM), [`PyTuple_SET_ITEM()`](https://docs.python.org/3.10/c-api/tuple.html#c.PyTuple\_SET\_ITEM)ve [`PyCell_SET()`](https://docs.python.org/3.10/c-api/cell.html#c.PyCell\_SET)makrolar artık l değeri veya r değeri olarak kullanılamaz. Örneğin ve şimdi bir derleyici hatasıyla başarısız oluyor. Test gibi hataları engeller . [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=30459) 30459'da Zackery Spytz ve Victor Stinner tarafından katkıda bulunulmuştur .)`x = PyList_SET_ITEM(a, b, c)PyList_SET_ITEM(a, b, c) = xif (PyList_SET_ITEM (a, b, c) < 0) ...`
* Sınırlı olmayan API dosyaları `odictobject.h`, `parser_interface.h`, `picklebufobject.h`, `pyarena.h`, `pyctype.h`, `pydebug.h`ve `pyfpe.h`dizine `pytime.h`taşındı `Include/cpython` . Bu dosyalar zaten dahil edildikleri için doğrudan dahil edilmemelidir `Python.h`; bkz . [Dosyaları Dahil Et](https://docs.python.org/3.10/c-api/intro.html#api-includes) . Doğrudan dahil edilmişlerse, `Python.h`bunun yerine dahil etmeyi düşünün. [( Bpo-35134'te](https://bugs.python.org/issue?@action=redirect\&bpo=35134) Nicholas Sim tarafından katkıda bulunulmuştur .)
* `Py_TPFLAGS_IMMUTABLETYPE`Değişmez tür nesneleri oluşturmak için tür bayrağını kullanın . `Py_TPFLAGS_HEAPTYPE`Bir tür nesnesinin değişken olup olmadığına karar vermek için güvenmeyin ; `Py_TPFLAGS_IMMUTABLETYPE`bunun yerine ayarlanmış olup olmadığını kontrol edin . [(Bpo -43908'de](https://bugs.python.org/issue?@action=redirect\&bpo=43908) Victor Stinner ve Erlend E. Aasland tarafından katkıda bulunulmuştur .)
* Belgelenmemiş işlev `Py_FrozenMain`, sınırlı API'den kaldırıldı. İşlev, esas olarak Python'un özel yapıları için kullanışlıdır. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=26241) 26241'de Petr Viktorin tarafından katkıda bulunulmuştur .)

#### Deprecated

* İşlev `PyUnicode_InternImmortal()`artık kullanımdan kaldırılmıştır ve Python 3.12'de kaldırılacaktır: [`PyUnicode_InternInPlace()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_InternInPlace) bunun yerine kullanın. [(Bpo -41692'de](https://bugs.python.org/issue?@action=redirect\&bpo=41692) Victor Stinner tarafından katkıda bulunulmuştur .)

#### Removed

*   Dizeleri `Py_UNICODE_str*`manipüle eden işlevler kaldırıldı . `Py_UNICODE*`(Inada Naoki tarafından [bpo-41123'te katkıda bulunulmuştur](https://bugs.python.org/issue?@action=redirect\&bpo=41123) .)

    > * `Py_UNICODE_strlen`: kullanın [`PyUnicode_GetLength()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_GetLength)veya [`PyUnicode_GET_LENGTH`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_GET\_LENGTH)
    > * `Py_UNICODE_strcat`: kullanın [`PyUnicode_CopyCharacters()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_CopyCharacters)veya [`PyUnicode_FromFormat()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_FromFormat)
    > * `Py_UNICODE_strcpy`, `Py_UNICODE_strncpy`: kullanın [`PyUnicode_CopyCharacters()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_CopyCharacters)veya[`PyUnicode_Substring()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_Substring)
    > * `Py_UNICODE_strcmp`: kullanmak[`PyUnicode_Compare()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_Compare)
    > * `Py_UNICODE_strncmp`: kullanmak[`PyUnicode_Tailmatch()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_Tailmatch)
    > * `Py_UNICODE_strchr`, `Py_UNICODE_strrchr`: kullanın [`PyUnicode_FindChar()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_FindChar)
* Kaldırıldı `PyUnicode_GetMax()`. Lütfen yeni ([**PEP 393**](https://www.python.org/dev/peps/pep-0393) ) API'ler. (Inada Naoki tarafından[ bpo-41103'te](https://bugs.python.org/issue?@action=redirect\&bpo=41103) katkıda bulunulmuştur .)
* Kaldırıldı `PyLong_FromUnicode()`. Lütfen adresine taşıyın [`PyLong_FromUnicodeObject()`](https://docs.python.org/3.10/c-api/long.html#c.PyLong\_FromUnicodeObject). (Inada Naoki tarafından [bpo-41103'te](https://bugs.python.org/issue?@action=redirect\&bpo=41103) katkıda bulunulmuştur .)
* Kaldırıldı `PyUnicode_AsUnicodeCopy()`. Lütfen [`PyUnicode_AsUCS4Copy()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_AsUCS4Copy)veya kullanın ( [bpo-41103'te](https://bugs.python.org/issue?@action=redirect\&bpo=41103)[`PyUnicode_AsWideCharString()`](https://docs.python.org/3.10/c-api/unicode.html#c.PyUnicode\_AsWideCharString) Inada Naoki tarafından sağlanmıştır .)
* Kaldırılan `_Py_CheckRecursionLimit`değişken: yapı tarafından `ceval.recursion_limit`değiştirildi [`PyInterpreterState`](https://docs.python.org/3.10/c-api/init.html#c.PyInterpreterState). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=41834) 41834'te Victor Stinner tarafından katkıda bulunulmuştur .)
* Belgelenmemiş makrolar `Py_ALLOW_RECURSION`ve `Py_END_ALLOW_RECURSION`yapının `recursion_critical`alanı kaldırıldı [`PyInterpreterState`](https://docs.python.org/3.10/c-api/init.html#c.PyInterpreterState). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=41936) 41936'da Serhiy Storchaka tarafından katkıda bulunulmuştur .)
* `PyOS_InitInterrupts()`Belgelenmemiş işlev kaldırıldı . Python'u başlatmak, sinyal işleyicileri zaten dolaylı olarak yükler: bkz [`PyConfig.install_signal_handlers`](https://docs.python.org/3.10/c-api/init\_config.html#c.PyConfig.install\_signal\_handlers). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=41713) 41713'te Victor Stinner tarafından katkıda bulunulmuştur .)
* `PyAST_Validate()`İşlevi kaldırın . `mod_ty`Genel C API ile bir AST nesnesi (tipi) oluşturmak artık mümkün değildir . İşlev, sınırlı C API'sinden ([**PEP384**](https://www.python.org/dev/peps/pep-0384) ). [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43244) 43244'te Victor Stinner tarafından katkıda bulunulmuştur.)
*   `symtable.h`Başlık dosyasını ve belgelenmemiş işlevleri kaldırın :

    * `PyST_GetScope()`
    * `PySymtable_Build()`
    * `PySymtable_BuildObject()`
    * `PySymtable_Free()`
    * `Py_SymtableString()`
    * `Py_SymtableStringObject()`

    İşlev `Py_SymtableString()`, yanlışlıkla kararlı ABI'nin bir parçasıydı, ancak `symtable.h`başlık dosyası sınırlı C API'sinden hariç tutulduğundan kullanılamadı.

    Bunun yerine Python [`symtable`](https://docs.python.org/3.10/library/symtable.html#module-symtable)modülünü kullanın. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43244) 43244'te Victor Stinner tarafından katkıda bulunulmuştur .)
* [`PyOS_ReadlineFunctionPointer()`](https://docs.python.org/3.10/c-api/veryhigh.html#c.PyOS\_ReadlineFunctionPointer)Sınırlı C API başlıklarından ve `python3.dll`Windows'ta kararlı ABI sağlayan kitaplıktan kaldırın . İşlev bir `FILE*`argüman aldığından, ABI kararlılığı garanti edilemez. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43868) 43868'de Petr Viktorin tarafından katkıda bulunulmuştur .)
* `ast.h`, `asdl.h`ve `Python-ast.h`başlık dosyalarını kaldırın . Bu işlevler belgelenmemiş ve sınırlı C API'sinden hariç tutulmuştur. Bu başlık dosyaları tarafından tanımlanan adların çoğuna ön ek eklenmemiştir `Py`ve bu nedenle ad çakışmaları oluşturabilir. Örneğin, Windows başlığı tarafından kullanılan adla çakışan `Python-ast.h`bir makro tanımladı . Bunun yerine Python modülünü kullanın. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43244) 43244'te Victor Stinner tarafından katkıda bulunulmuştur .)`YieldYield<winbase.h>`[`ast`](https://docs.python.org/3.10/library/ast.html#module-ast)
*   Genel AST C API kaldırıldığından, tür kullanarak derleyici ve ayrıştırıcı işlevlerini kaldırın :`struct _mod`

    * `PyAST_Compile()`
    * `PyAST_CompileEx()`
    * `PyAST_CompileObject()`
    * `PyFuture_FromAST()`
    * `PyFuture_FromASTObject()`
    * `PyParser_ASTFromFile()`
    * `PyParser_ASTFromFileObject()`
    * `PyParser_ASTFromFilename()`
    * `PyParser_ASTFromString()`
    * `PyParser_ASTFromStringObject()`

    Bu işlevler belgelenmemiş ve sınırlı C API'sinden hariç tutulmuştur. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43244) 43244'te Victor Stinner tarafından katkıda bulunulmuştur .)
*   `pyarena.h`Başlık dosyasını işlevlerle kaldırın :

    * `PyArena_New()`
    * `PyArena_Free()`
    * `PyArena_Malloc()`
    * `PyArena_AddPyObject()`

    Bu işlevler belgelenmemiş, sınırlı C API'sinden hariç tutulmuştur ve yalnızca derleyici tarafından dahili olarak kullanılmıştır. [(Bpo -](https://bugs.python.org/issue?@action=redirect\&bpo=43244) 43244'te Victor Stinner tarafından katkıda bulunulmuştur .)
* Python'u optimize etmek için `PyThreadState.use_tracing`üye kaldırıldı. [( bpo-43760'da](https://bugs.python.org/issue?@action=redirect\&bpo=43760) Mark Shannon tarafından katkıda bulunulmuştur .)

### 3.10.7'de dikkate değer güvenlik özelliği&#x20;

2 (ikili), 4, 8 (sekizlik), 16 (onaltılık) veya 10 tabanı (ondalık) gibi 32 dışındaki tabanlar arasında [`int`](https://docs.python.org/3.10/library/functions.html#int)ve tabanlarda dönüştürme, artık dize biçimindeki basamak sayısı kaçınılması gereken bir sınırın üzerindeyse a değerini yükseltir Algoritmik karmaşıklık nedeniyle olası hizmet reddi saldırıları. Bu, [CVE-2020-10735](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-10735) için bir hafifletmedir . Bu sınır, ortam değişkeni, komut satırı bayrağı veya API'ler tarafından yapılandırılabilir veya devre dışı bırakılabilir . [Tamsayı dizesi dönüştürme uzunluğu sınırlaması](https://docs.python.org/3.10/library/stdtypes.html#int-max-str-digits) belgelerine bakın . Varsayılan sınır, dize biçiminde 4300 basamaktır.[`str`](https://docs.python.org/3.10/library/stdtypes.html#str)[`ValueError`](https://docs.python.org/3.10/library/exceptions.html#ValueError)[`sys`](https://docs.python.org/3.10/library/sys.html#module-sys)

### 3.10.8'de dikkate değer güvenlik özelliği&#x20;

Kullanımdan kaldırılan [`mailcap`](https://docs.python.org/3.10/library/mailcap.html#module-mailcap)modül artık güvenli olmayan metinleri (dosya adları, MIME türleri, parametreler) kabuk komutlarına enjekte etmeyi reddediyor. Bu tür bir metni kullanmak yerine, bir eşleşme bulunamamış gibi (veya test başarısızmış gibi test komutları için) uyaracak ve hareket edecektir. [(Gh -98966'da](https://github.com/python/cpython/issues/98966) Petr Viktorin tarafından katkıda bulunulmuştur .)
