# Python'da Veri Türleri

Python Programlama Dili'nde veri türleri şunlardır: String, İnteger, bool, float, list,range, complex, tuple, dict, frozenset, set, binary, bytearray, memoryview.

yazıgilin veri türleri | String\
sayıgilin veri türleri | İnteger, Float , Complex\
dizigilin veri türleri | List, Range\
yeradıgilin veri türleri | Dict\
kümegilin veri türleri |Set, frozenset\
mantıkgilin veri türleri | Bool\
ikili sayıgilin veri türleri | Binary, Bytearray, Memoryview

1. String(metin) nedir? Türkçe karşlığı metin olan veri türü'nün pythondaki Kulanım şekli aşağıdaki örnektedir.





2.İnteger(tam sayı):

3.Float:Kullanım olarak float() ondalık sayısal ifade demek.

4.Bool:kullanım olarak bool() True(Doğru) veya False(Yanlış) ifade demek.

5.Complex:kullanım olarak complex(1,2j) 1. gercek sayımız 2.hayali sayımız.negatif sayılardan sonra matematikçiler diyolarki reel sayılarda hiç bir sayının karesi -1 olmaz ozaman hayali bir sayı yapalım bu i olsun i=√-1 .Aşağıdaki örnekte bunu daha detaylı inceliyelim.

```python
#Verinin Türünü Öğrenmek İçin
'''
veri türlerini ekrana yazdırmak için.
'''
x = 15
print(type(x))

```

```python
#Metinsel Veri


'''
# Tırnakların içerisine yazdıgınız herşey string(metinsel) ifade olarak çalıştırılır.
# Python string ifadesi şu şekilde kullanılıyor.
'''
veri = 'Babannemi yedim'
```

```python
#Sayısal Veri

'''
# Python integer ifadesi şu şekilde kullanılıyor.
# Girdiginiz sayılar tam sayıları ifade eder ve tanımlar.

''' 
sayısal_veri = int(255)

'''
#Python float ifadesi şu şekilde kullanılıyor.
#Girginiz sayısal ifadeleri ondalıklı yani mesela 10.0 12.5 145.20 gibi sayıların tanımlanması içindir.

''' 
ondalıklı_veri = float(43.5)


'''
#Python bool ifadesi şu şekilde kullanılıyor.
#Girdiğiniz ifadelere True yada False olarak sonuç döndürür.
#Mesela 10 5 ten büyük değilmi eğer büyükse true değilse false döndürür ki 10 5 ten büyük oluyo sanırım ozaman true döner.
'''
print(bool(10 > 5))

'''
#Complex  ifadesi şu şekilde kullanılıyor.
#Karmaşık(complex) sayı verilen x ve b sayısının (x+bj) şeklinde yazılmasıdır
buradaki j harfi hayali saydır. √-1 = j.
'''
x = complex(12+4j)



```

```python
#Dizeler
'''
#List
'''
'''
#Tuple
'''
'''
#Range
'''
```

```python
'''
Dictionary
'''
```

