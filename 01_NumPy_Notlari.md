
## 1. Temel Tanım ve Kullanım Amacı
- **NumPy Nedir?** "Numerical Python"ın kısaltmasıdır. Çok boyutlu diziler ve matrisler üzerinde hızlı matematiksel hesaplamalar yapmayı sağlayan temel bir Python kütüphanesidir.
    
- **Hangi Problemleri Çözer?** Standart Python listeleri farklı veri tiplerini bir arada tuttuğu için bellekte dağınık durur ve büyük verilerde çok yavaş çalışır. NumPy dizileri (`ndarray`) ise tek veri tipinde olup bellekte yan yana tutulur; arka planda C diliyle çalıştığı için döngü yazmadan (vektörel olarak) binlerce veriyi milisaniyeler içinde işler.
    
- **Neden Öğrenmemiz Gerekiyor?** Pandas, Matplotlib, OpenCV ve yapay zeka kütüphanelerinin tamamı arka planda veriyi NumPy dizisi olarak işler. Yapay zekaya girişin temel taşıdır.
---

## 2. Temel Fonksiyonlar ve Yapılar

# 1. ndarray Oluşturma

NumPy'daki veri yapısının adı `ndarray`'dir. Tek bir satır sayı varsa buna **1 Boyutlu Dizi (Vektör)** denir. Satır ve sütunlardan oluşuyorsa buna **2 Boyutlu Dizi (Matris)** denir. Üst üste binmiş matrisler varsa **3 Boyutlu Dizi (Tensör)** denir.

```python
import numpy as np

# Normal Python listesini np.array() içine vererek NumPy dizisine çeviriyoruz
hizlar = np.array([50, 52, 55, 53, 60])
print("Vektör:", hizlar)

# İç içe listeler vererek 2 boyutlu matris oluşturuyoruz (2 satır, 3 sütun)
matris = np.array([
    [10, 20, 30],
    [40, 50, 60]
])
print("Matris:\n", matris)

'''text 
1D Dizi: [30 45 60 75]
2D Matris:
 [[10.5 20.1]
 [15.2 18.4]]

```

# 2. Diziyi Tanıma ve Şekillendirme (`ndim`, `size`, `dtype`, `reshape`)

Yapay zeka modellerine veri verirken en çok alınan hata "boyut uyuşmazlığı"dır. Elimizdeki verinin röntgenini çekmek için bu 4 özelliği kullanırız:

- **`ndim` (Number of Dimensions):** Dizinin kaç boyutlu olduğunu söyler (1 mi, 2 mi, 3 mü?).
    
- **`size`:** İçinde toplam kaç tane sayı olduğunu söyler.
    
- **`dtype` (Data Type):** İçindeki sayıların tipini söyler (`int64` tam sayı, `float64` ondalıklı sayı).
    
- **`reshape` (Yeniden Şekillendirme):** Eleman sayısını değiştirmeden dizinin satır ve sütun düzenini değiştirir. _Kritik kural:_ Elinde 6 eleman varsa bunu `2x3` veya `3x2` yapabilirsin ama `2x4` (8 eleman) yapamazsın.

```python 
sayilar = np.array([10, 20, 30, 40, 50, 60])

print("Kaç boyutlu?:", sayilar.ndim)      # 1 çıkacak (tek satır)
print("Kaç eleman var?:", sayilar.size)   # 6 çıkacak
print("Veri tipi ne?:", sayilar.dtype)    # int64 çıkacak

# Şimdi bu 6 sayıyı 2 satır, 3 sütunluk bir tabloya dönüştürelim:
yeni_sekil = sayilar.reshape(2, 3)
print("Yeni şekil:\n", yeni_sekil)
print("Yeni boyut sayısı:", yeni_sekil.ndim) # Artık 2 boyutlu!

'''text
Boyut sayısı (ndim): 1
Toplam eleman sayısı (size): 6
Veri tipi (dtype): int64
Reshape sonrası matris:
 [[1 2 3]
 [4 5 6]]
```
# 3. Otomatik Sayı Dizileri Üretme (`arange` ve `linspace`)

Test yaparken veya grafik çizerken elimizle tek tek `[0, 1, 2, 3... 100]` yazmayız. NumPy'a kurallı diziler ürettiririz. İkisinin arasındaki fark çok nettir:

- **`arange(başlangıç, bitiş, adım)`:** _"Bana 0'dan başla, 10'a kadar 2'şer 2'şer say"_ dersin. Adım miktarını sen belirlersin, bitiş sayısı dahil edilmez.
    
- **`linspace(başlangıç, bitiş, adet)`:** _"Bana 0 ile 1 arasını eşit aralıklarla 5 parçaya böl"_ dersin. Adımı NumPy kendi hesaplar, bitiş sayısı dahildir. (Özellikle grafiklerde eksen oluştururken çok kullanılır).

```python
# 0'dan başla, 20'ye kadar 5'er art (20 dahil değil)
adimli = np.arange(0, 20, 5)
print("arange:", adimli)  # [0, 5, 10, 15]

# 0 ile 100 arasını eşit aralıklarla 5 sayıya böl (100 dahil)
esit = np.linspace(0, 100, 5)
print("linspace:", esit)  # [0., 25., 50., 75., 100.]

'''text
arange sonucu: [0 2 4 6 8]
linspace sonucu: [0.   0.25 0.5  0.75 1.  ]
```

# 4. Vektörel Matematiksel İşlemler (`+`, `-`, `*`, `/`)

Normal Python listesinde `[10, 20, 30] * 2` yazarsan Python listeyi iki kez yan yana yapıştırıp `[10, 20, 30, 10, 20, 30]` yapar; matematiksel çarpma yapmaz. NumPy ise doğrudan içindeki sayıları çarpar. İki diziyi toplarken de 1. elemanla 1. elemanı, 2. ile 2.'yi karşılıklı toplar.

```python
mesafe = np.array([100, 200, 300]) # metre
zaman = np.array([5, 10, 15])      # saniye

# Hiç döngü kurmadan tüm yolculukların hızını tek satırda buluyoruz:
hiz = mesafe / zaman
print("Hızlar (m/s):", hiz)

# Tüm hızlara 5 m/s ekleyelim:
print("Artırılmış hızlar:", hiz + 5)

'''text
Hız (m/s): [20. 25. 30.]
Hız (km/saat): [ 72.  90. 108.]
```

# 5. Matris İşlemleri (`@` ve `.T`)

Lise matematiğindeki matris konusunu hatırla:

- İki matrisi normal `*` ile çarparsan, NumPy sadece aynı konumdaki sayıları birbiriyle çarpar.
    
- Gerçek lineer cebir **matris çarpımı** (satır ile sütunun çarpılıp toplanması) yapmak için **`@`** işareti (veya `np.dot()`) kullanılır. Yapay sinir ağlarının ve görüntü işlemenin kalbi budur.
    
- **`.T` (Transpoz):** Matrisin satırlarını sütun, sütunlarını satır yapar (matrisi yan çevirir).

```python
A = np.array([[1, 2],
              [3, 4]])

B = np.array([[2, 0],
              [1, 2]])

print("Normal eleman bazlı çarpım (*):\n", A * B)
print("Gerçek matris çarpımı (@):\n", A @ B)
print("A'nın yan çevrilmiş hali (Transpoz):\n", A.T)

'''text
Matris Çarpımı:
 [[18 26]
 [11 19]]
A Matrisinin Transpozu:
 [[2 1]
 [4 3]]

```


## Otonom Araçlarda Kullanımı 

Otonom bir araç yolda giderken etrafındaki her şeyi bizim gibi şekil ya da renk olarak değil, doğrudan dev sayı tabloları olarak görür. Ön kameradan gelen görüntüler aslında piksellerin renk değerlerinden oluşan üç boyutlu matrislerdir ve yol dışındaki gereksiz yerleri kesip atmak ya da sayıları küçültüp bilgisayarı rahatlatmak tamamen NumPy ile saniyenin binde biri sürede yapılır. Aynı şekilde tepedeki LiDAR sensöründen gelen binlerce lazer noktasının koordinatları da bir matriste tutulur; araç direksiyonu kırdığında etraftaki nesnelerin yeni yerini tek tek hesaplamak yerine tüm matrisi döndürme matrisiyle çarparak haritayı anında güncelleriz. Kısacası otobanda yüksek hızla giderken aniden yola fırlayan bir engeli fark edip saliseler içinde fren kararı verebilmek için klasik döngüler yerine NumPy'ın bu hızlı filtreleme gücüne mecburuz.

