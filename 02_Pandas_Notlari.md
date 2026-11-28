
---

## 1. Temel Tanım ve Kullanım Amacı

* ### Temel Mantık: NumPy Varken Neden Pandas'a İhtiyacımız Var?

NumPy mükemmel bir hesap makinesidir ama tek bir şartı vardır: **"İçime koyduğun her şey aynı tip sayı olmalı ve sütun isimleri gibi süslerle uğraşamam."**

Oysa gerçek hayatta bir otonom aracın verisi şöyle görünür:

- `Zaman`: 14:02:01 (Tarih/Saat)
    
- `Hız`: 65.4 (Ondalıklı sayı)
    
- `Hava Durumu`: "Yağmurlu" (Metin)
    
- `Öndeki Engel`: Boş / Sensör okuyamadı (`NaN`)
    

İşte NumPy bu karışık tabloyu yönetemez. **Pandas**, Python'ın içindeki gelişmiş bir **Excel** gibidir. Farklı veri tiplerini tek bir tabloda toplar, her sütuna ve satıra isim verir, eksik/bozuk verileri saniyeler içinde temizler.

Kısacası: **Sayısal matris matematiği için NumPy, gerçek hayatın karmaşık tablolarını temizlemek ve analiz etmek için Pandas kullanırız.**---

## 2. Temel Fonksiyonlar ve Yapılar

#### 1. `Series` ve `DataFrame` Farkı (Pandas'ın İki Ana Yapısı)

Pandas'ta her şey bu iki yapıdan oluşur:

- **`Series` (Seri):** Tek bir sütundur. NumPy dizisinden farkı, sol tarafında satır etiketlerinin (indekslerin) olmasıdır. (Örn: Sadece hız ölçümlerinin alt alta dizildiği tek bir kolon).
    
- **`DataFrame` (Veri Çerçevesi):** Birden fazla `Series`'in yan yana gelmesiyle oluşan tam bir **tablodur**. Hem satır numaraları hem de sütun başlıkları vardır.

```python
import pandas as pd
import numpy as np

# 1. Series: Tek boyutlu, etiketli tek bir sütun
hizlar = pd.Series([45, 62, 58, 70], name="Hiz")
print("--- SERIES ---")
print(hizlar)

# 2. DataFrame: Sözlük (dict) yapısından 2 boyutlu tablo oluşturma
veri = {
    "Saniye": [1, 2, 3, 4, 5],
    "Hiz_kmh": [45, 62, np.nan, 75, 50], # np.nan bilerek koyduğumuz boş/kayıp veri
    "Mesafe_m": [40.5, 25.0, 18.2, 7.5, 14.0],
    "Hava": ["Gunesli", "Gunesli", "Yagmurlu", "Yagmurlu", "Sisli"]
}

df = pd.DataFrame(veri)
print("\n--- DATAFRAME (TABLO) ---")
print(df)

'''text
--- SERIES ---
0    45
1    62
2    58
3    70
Name: Hiz, dtype: int64

--- DATAFRAME (TABLO) ---
   Saniye  Hiz_kmh  Mesafe_m      Hava
0       1     45.0      40.5   Gunesli
1       2     62.0      25.0   Gunesli
2       3      NaN      18.2  Yagmurlu
3       4     75.0       7.5  Yagmurlu
4       5     50.0      14.0     Sisli

```

#### 2. Dışarıdan Veri Okuma (`read_csv`, `read_excel`)

Gerçek projelerde verileri elimizle yukarıdaki gibi yazmayız; sensörlerden veya internetten gelen `.csv` (virgülle ayrılmış değerler) ya da `.xlsx` (Excel) dosyalarını okuturuz. Pandas bu dosyaları tek satır kodla doğrudan bir `DataFrame` tablosuna çevirir.

```python
# Test edebilmek için önce elimizdeki tabloyu Colab'a CSV dosyası olarak kaydedelim:
df.to_csv("arac_log.csv", index=False)

# Şimdi sanki dışarıdan dosya gelmiş gibi okuyalım:
tablo = pd.read_csv("arac_log.csv")

# .head(3) tablonun sadece ilk 3 satırını görmemizi sağlar (binlerce satır varsa hayat kurtarır)
print("CSV'den okunan ilk 3 satır:\n", tablo.head(3))

# Not: Excel dosyası olsaydı aynı mantıkla şöyle okuyacaktık:
# excel_tablo = pd.read_excel("arac_log.xlsx")

'''text
CSV'den okunan ilk 3 satır:
    Saniye  Hiz_kmh  Mesafe_m      Hava
0       1     45.0      40.5   Gunesli
1       2     62.0      25.0   Gunesli
2       3      NaN      18.2  Yagmurlu

```
#### 3. Veri Filtreleme ve Seçme: `loc` ve `iloc` Farkı

Bu ikisi Pandas'ta en çok karıştırılan ama mantığı çok basit olan iki komuttur. İkisi de tablodan cımbızla belirli satır ve sütunları çekip almaya yarar:

- **`loc` (Location - İsim/Etiket Bazlı):** Sütunların **isimlerini** ve mantıksal koşulları kullanırsın. _"Bana `Mesafe_m` sütunu 20'den küçük olan satırların `Saniye` ve `Hava` bilgilerini getir"_ dediğinde `loc` kullanırsın.
    
- **`iloc` (Integer Location - Sayısal Sıra Bazlı):** İsimlere bakmaz, sadece **sıra numarasına (0, 1, 2...)** bakar. _"Bana tablonun 1. satırından 3. satırına kadar olan kısmının ilk 2 sütununu getir"_ dediğinde `iloc` kullanırsın.

```python
# loc: Etiket ve koşul ile seçim (Mesafesi 20 metreden az olan tehlikeli anlar)
tehlikeli_anlar = tablo.loc[tablo["Mesafe_m"] < 20, ["Saniye", "Mesafe_m", "Hava"]]
print("--- loc İLE SEÇİM ---")
print(tehlikeli_anlar)

# iloc: Sadece indeks numarası ile seçim (1. ve 2. satırlar, 0. ve 1. sütunlar)
# Not: iloc'ta bitiş indeksi (3 ve 2) dahil edilmez!
sirayla_secim = tablo.iloc[1:3, 0:2]
print("\n--- iloc İLE SEÇİM ---")
print(sirayla_secim)

'''text
--- loc İLE SEÇİM ---
   Saniye  Mesafe_m      Hava
2       3      18.2  Yagmurlu
3       4       7.5  Yagmurlu
4       5      14.0     Sisli

--- iloc İLE SEÇİM ---
   Saniye  Hiz_kmh
1       2     62.0
2       3      NaN
```

#### 4. Kayıp (Eksik) Veri Yönetimi (`dropna`, `fillna`)

Otonom araçta yağmur yağarken kamera anlık körleşebilir veya sensör kablosu temassızlık yapıp o saniyeyi boş (`NaN` - Not a Number) kaydedebilir. Yapay zeka modelleri içinde `NaN` olan tablolarla çalışamaz, hata verir. Önümüzde iki seçenek vardır:

- **`dropna()`:** Eksik veri barındıran o satırı komple çöpe atar. (Verin çok fazlaysa ve 1-2 satır silmek zarar vermeyecekse kullanılır).
    
- **`fillna()`:** Satırı silmeye kıyamıyorsan, o boşluğun içini mantıklı bir sayıyla (örneğin o sütunun ortalamasıyla veya `0` ile) doldurur.

```python
# 1. Yol: Eksik verinin olduğu 3. saniyeyi tamamen silme
silinmis_tablo = tablo.dropna()
print("dropna sonrası kalan satır sayısı:", len(silinmis_tablo))

# 2. Yol: Eksik hız değerini, diğer hızların ortalaması ile doldurma
doldurulmus_tablo = tablo.copy() # Orijinal tabloyu bozmamak için kopyaladık
ortalama_hiz = doldurulmus_tablo["Hiz_kmh"].mean()
doldurulmus_tablo["Hiz_kmh"] = doldurulmus_tablo["Hiz_kmh"].fillna(ortalama_hiz)

print("\nfillna sonrası doldurulmuş tablo:\n", doldurulmus_tablo)

'''text
dropna sonrası kalan satır sayısı: 4

fillna sonrası doldurulmuş tablo:
    Saniye  Hiz_kmh  Mesafe_m      Hava
0       1     45.0      40.5   Gunesli
1       2     62.0      25.0   Gunesli
2       3     58.0      18.2  Yagmurlu
3       4     75.0       7.5  Yagmurlu
4       5     50.0      14.0     Sisli

```

#### 5. Özetleme, Gruplama ve Sıralama (`describe`, `groupby`, `sort_values`)

Binlerce satırlık bir tabloya gözle bakıp yorum yapamayız. Pandas bize verinin özetini çıkarır:

- **`describe()`:** Tablodaki tüm sayısal sütunların ortalamasını, en küçük değerini (`min`), en büyük değerini (`max`) ve standart sapmasını tek komutta döker.
    
- **`groupby()`:** Veriyi kategorilere göre gruplar. _(Örn: "Güneşli havadaki ortalama hızım ne, Yağmurlu havadaki ortalama hızım ne?")_
    
- **`sort_values()`:** Tabloyu istediğin sütuna göre küçükten büyüğe veya büyükten küçüğe sıralar.


```python
# 1. Tek komutta tüm istatistiksel özet
print("--- İSTATİSTİKSEL ÖZET ---")
print(doldurulmus_tablo.describe())

# 2. Hava durumuna göre gruplayıp ortalama hızları karşılaştırma
print("\n--- HAVA DURUMUNA GÖRE ORTALAMA HIZ ---")
print(doldurulmus_tablo.groupby("Hava")["Hiz_kmh"].mean())

# 3. Aracın engele en çok yaklaştığı anları en üstte görmek için küçükten büyüğe sıralama
en_yakin_engeller = doldurulmus_tablo.sort_values(by="Mesafe_m", ascending=True)
print("\n--- EN YAKIN ENGELE GÖRE SIRALI TABLO ---")
print(en_yakin_engeller)

'''text
--- İSTATİSTİKSEL ÖZET ---
         Saniye    Hiz_kmh   Mesafe_m
count  5.000000   5.000000   5.000000
mean   3.000000  58.000000  21.040000
std    1.581139  11.597414  12.602896
min    1.000000  45.000000   7.500000
25%    2.000000  50.000000  14.000000
50%    3.000000  58.000000  18.200000
75%    4.000000  62.000000  25.000000
max    5.000000  75.000000  40.500000

--- HAVA DURUMUNA GÖRE ORTALAMA HIZ ---
Hava
Gunesli     53.5
Sisli       50.0
Yagmurlu    66.5
Name: Hiz_kmh, dtype: float64

--- EN YAKIN ENGELE GÖRE SIRALI TABLO ---
   Saniye  Hiz_kmh  Mesafe_m      Hava
3       4     75.0       7.5  Yagmurlu
4       5     50.0      14.0     Sisli
2       3     58.0      18.2  Yagmurlu
1       2     62.0      25.0   Gunesli
0       1     45.0      40.5   Gunesli

```

## Otonom Araçlarda Kullanımı

NumPy o anki fiziksel hesaplamaları yaparken Pandas da aracın hafızası ve kara kutusu gibi çalışır. Test sürüşleri boyunca aracın hızı, fren basıncı, direksiyon açısı ve o anki hava durumu gibi birbiriyle tamamen farklı tipteki veriler saniyede onlarca kez kaydedilir ve hepsi Pandas sayesinde tek bir düzenli tabloda toplanır. Gerçek hayatta yağmur yağdığında ya da sensör anlık temassızlık yaptığında bu tabloda boş veya mantıksız değerler oluşabiliyor; biz de modeli bozmamak için boşlukları ortalama değerlerle dolduruyor, hatalı kayıtları temizliyoruz. Ayrıca verileri yağmurlu ya da güneşli hava diye gruplayarak aracın hangi şartlarda daha sık ani fren yaptığını kolayca kıyaslayıp güvenliğini test edebiliyoruz.



