---
layout: editorial
---

# Sözdizimi Kuralları

Python'un sözdizimi kuralları[ PEP 8](../python-ile-tanisin/pep-8-bilgisi.md) standartına bağlıdır. Python'da kodlar soldan sağa ve yukarıdan aşağı okunarak çalışır. Okumadı iseniz [PEP 8](../python-ile-tanisin/pep-8-bilgisi.md) yazımızı okuyarak ayrıntılı bilgi edinebilirsiniz.

## Python'da Değişken Kuralları

Değişkenlerde belirlenmiş kurallar şöyledir:

1. Değişken adı koyulur iken boşluk kullanılmaz.
2. Değişken adı koyulur iken sayı ile başlamaz.
3. Değişken adı koyulur iken küçük büyük damgalar ayırt edicidir ve eşit görülemez.
4. Python'da girintiler önemli bir yere sahiptir ve eşit olmaktadırlar.

&#x20;Python'da  geçici olarak veri saklamak ve istenen yerde kullanmak için değişkenler kullanılır.

### Değişken Adı Koyma Ve Değer Ataması

{% code overflow="wrap" lineNumbers="true" %}
```python
# Değişken Oluşturma
// Aşağıda değer ifadesine eşittir operatörü ile 7 sayısını tanımlıyoruz. ve hangi şekilerde tanımlayabiliriz.
deger = 'Volkan' 
deger_3 = 7
Deger = 4
Deger_3 = 3
deger3 = 2
Deger3 = 6
deger = 15
```
{% endcode %}

#### <mark style="color:red;">Değişken Adı Koymanın Yanlış Örnekleri</mark>

```python
_deger = 75 # değişken adının başı "_" olamaz
3deger = 2 # değişken adının başı herhangi bir sayı olamaz.
deger 3 = 32 # değişken adında boşluk olamaz.
 deger = 72 # değişken adı girinti hizâsından sapmış olamaz.
```

{% hint style="warning" %}
Python'da girintiler önemlidir. Hem okunabilirlik açısından hem de kurallar açısından.
{% endhint %}

```python
#örnek
veri = 5
if veri > 3:
    print('veri büyüktür')
    
# Girintisiz oldugunda vereceği hata
veri = 5
if veri > 3:
print('veri büyüktür')
# Kodu run ettiğimizde hatanın kaçıncı satırda döndüğü ve neden hata verdiği yazıyor.

'''c:/Users/admin/Desktop/tyk/syntax.py
  File "c:\Users\admin\Desktop\tyk\syntax.py", line 3
    print('veri büyüktür')
    ^
IndentationError: expected an indented block after 'if' statement on line 2 
'''
```
