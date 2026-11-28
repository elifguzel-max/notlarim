
---

## 1. Temel Tanım ve Kullanım Amacı

### Temel Mantık: Sayılar Varken Neden Matplotlib'e İhtiyacımız Var?

İnsan beyni binlerce satırlık sayı tablosuna (Pandas `DataFrame`) baktığında içindeki gizli hataları veya trendleri göremez.

- Örneğin bir otonom aracın 10 dakikalık test sürüşünde hız sensörü 1 saniyeliğine bozulup hızı **450 km/saat** olarak kaydetmiş olabilir. Tabloda binlerce satır arasında bu hatayı gözle kaçırırsın.
    
- Ama veriyi **Matplotlib** ile çizgi grafiğine döktüğünde, düz giden çizginin bir anda tavana fırladığını milisaniyede fark edersin.
    

Kısacası: **NumPy ile hesaplarız, Pandas ile düzenleriz, Matplotlib ile veriye gözümüzle bakarız.** 
## 2. Temel Fonksiyonlar ve Yapılar

#### 1. `Figure` ve `Axes` Mantığı (Tuval ve Resim Çerçevesi)

Matplotlib'de en çok karıştırılan ama anlaşıldığında her şeyi çok kolaylaştıran iki kavram vardır. Bunu bir ressamın masası gibi düşün:

- **`Figure` (Tuval / Kağıt):** Üzerine çizim yaptığımız boş beyaz sayfanın tamamıdır. Tek başına içinde grafik yoktur, sadece dış çerçevedir.
    
- **`Axes` (Eksen / Grafik Alanı):** O kağıdın üzerine çizdiğimiz **gerçek grafiğin kendisidir** (x ve y eksenlerinin, çizgilerin, başlığın olduğu alan). Bir `Figure` kağıdının içine yan yana 2 veya 4 tane `Axes` (grafik) çizebilirsin.

```python
import matplotlib.pyplot as plt
import numpy as np

# 1 adet Figure (tuval) ve içine 1 adet Axes (grafik alanı) oluşturuyoruz
fig, ax = plt.subplots(figsize=(6, 4))

# Çizimi doğrudan 'ax' (grafik alanı) üzerine yapıyoruz
ax.plot([1, 2, 3, 4], [10, 25, 20, 40])
ax.set_title("Figure ve Axes Mantığı")

plt.show()

'''text
![[Pasted image 20261126212603.png]]
```

#### 2. Temel Grafik Türleri (`plot`, `scatter`, `bar`, `hist`)

Her veri tipi için aynı grafik kullanılmaz. Doğru veriye doğru grafiği seçmeliyiz:

- **`plot()` (Çizgi Grafiği):** Zaman içindeki değişimi göstermek için kullanılır. _(Örn: Saniyeler ilerledikçe aracın hızı nasıl değişti?)_
    
- **`scatter()` (Saçılım / Nokta Grafiği):** İki sayısal değişken arasında bir ilişki olup olmadığını görmek için kullanılır, noktaları çizgiyle birleştirmez. _(Örn: Hız arttıkça fren mesafesi nasıl dağılıyor?)_
    
- **`bar()` (Sütun Grafiği):** Kategorik (metin bazlı) verileri birbiriyle kıyaslamak için kullanılır. _(Örn: Güneşli, Yağmurlu ve Sisli havalardaki ortalama hız karşılaştırması)._
    
- **`hist()` (Histogram):** Tek bir sayısal verinin **dağılımını** (hangi aralıkta kaç tane veri yığıldığını) gösterir. _(Örn: Araç test boyunca en çok hangi hız aralığında gitti?)_

```python
# --- 1. Çizgi Grafiği (plot) ---
zaman = [1, 2, 3, 4, 5]
hiz = [20, 45, 60, 55, 70]

plt.figure(figsize=(5, 3))
plt.plot(zaman, hiz, marker="o", color="blue")
plt.title("Zaman - Hız Değişimi (Line Plot)")
plt.show()

# --- 2. Saçılım Grafiği (scatter) ---
hiz_veri = [30, 50, 70, 90, 110]
fren_mesafesi = [8, 18, 35, 58, 85]

plt.figure(figsize=(5, 3))
plt.scatter(hiz_veri, fren_mesafesi, color="red")
plt.title("Hız ve Fren Mesafesi İlişkisi (Scatter)")
plt.show()

# --- 3. Sütun Grafiği (bar) ---
havalar = ["Güneşli", "Yağmurlu", "Sisli"]
ort_hizlar = [85, 60, 45]

plt.figure(figsize=(5, 3))
plt.bar(havalar, ort_hizlar, color=["orange", "skyblue", "gray"])
plt.title("Hava Durumuna Göre Ortalama Hız (Bar)")
plt.show()

# --- 4. Histogram (hist) ---
# Ortalama 60 km/h civarında 200 adet rastgele hız ölçümü üretelim
rastgele_hizlar = np.random.normal(60, 10, 200)

plt.figure(figsize=(5, 3))
plt.hist(rastgele_hizlar, bins=15, color="green", edgecolor="black")
plt.title("Hız Dağılımı (Histogram)")
plt.show()

'''text
![[Pasted image 20261126212748.png]]

```

#### 3. Grafik Okunabilirliği: Başlık, Etiket ve Izgara (`title`, `xlabel`, `ylabel`, `grid`, `legend`)

Üzerinde eksen isimleri veya başlık olmayan bir grafik hiçbir anlam ifade etmez (60 ne? Saniye mi, metre mi, km/saat mi belli olmaz). Grafiği profesyonel hale getiren detaylar şunlardır:

- **`title()`:** Grafiğin ana başlığı.
    
- **`xlabel()` / `ylabel()`:** Yatay (X) ve dikey (Y) eksenlerin ne anlama geldiği.
    
- **`grid(True)`:** Arka plana kareli defter gibi kılavuz çizgileri ekler; noktaların hangi değere denk geldiğini okumayı kolaylaştırır.
    
- **`legend()`:** Aynı grafikte birden fazla çizgi varsa, hangi rengin neyi temsil ettiğini gösteren bilgi kutucuğudur.

```python
saniye = [0, 1, 2, 3, 4, 5]
arac_hizi = [0, 25, 50, 65, 60, 40]
hiz_limiti = [50, 50, 50, 50, 50, 50]

plt.figure(figsize=(7, 4))
plt.plot(saniye, arac_hizi, label="Araç Hızı", color="blue", linewidth=2)
plt.plot(saniye, hiz_limiti, label="Yol Hız Limiti", color="red", linestyle="--")

plt.title("Şehir İçi Hız Takip Analizi")
plt.xlabel("Zaman (Saniye)")
plt.ylabel("Hız (km/saat)")
plt.grid(True)    # Kılavuz çizgilerini açar
plt.legend()      # Sağ/sol üstte etiket kutusunu gösterir
plt.show()

'''text
![[Pasted image 20261126212850.png]]

```

#### 4. Çoklu Grafik Mantığı (`subplot` / `subplots`)

Bazen tek bir ekranda hem aracın hızını hem de öndeki engele olan mesafesini alt alta veya yan yana kıyaslamak isteriz. Bunun için `plt.subplots(satır_sayısı, sütun_sayısı)` fonksiyonu kullanılır.

- Örneğin `plt.subplots(2, 1)` dersen: **2 satır, 1 sütun** yani alt alta iki grafik alanı (`ax[0]` ve `ax[1]`) oluşturur.

```python
zaman = [1, 2, 3, 4, 5]
hiz = [40, 55, 70, 50, 20]
engel_mesafe = [50, 35, 18, 10, 6]

# 2 satır, 1 sütunluk çoklu grafik alanı oluşturuyoruz
fig, ax = plt.subplots(2, 1, figsize=(7, 6))

# Üstteki 1. Grafik: ax[0]
ax[0].plot(zaman, hiz, color="blue", marker="o")
ax[0].set_title("Zaman - Hız Değişimi")
ax[0].set_ylabel("Hız (km/h)")
ax[0].grid(True)

# Alttaki 2. Grafik: ax[1]
ax[1].plot(zaman, engel_mesafe, color="red", marker="s")
ax[1].set_title("Öndeki Engele Olan Mesafe")
ax[1].set_xlabel("Zaman (Saniye)")
ax[1].set_ylabel("Mesafe (Metre)")
ax[1].grid(True)

plt.tight_layout() # İki grafiğin yazılarının birbirine girmesini engeller
plt.show()

'''text
![[Pasted image 20261126212951.png]]
```

## Otonom Araçlarda Kullanımı 

Elimizde on binlerce satırlık hız ve sensör tablosu olduğunda tek tek sayılara bakarak aracın virajda savrulduğunu ya da sensörün bir anlığına bozulduğunu fark etmek neredeyse imkansız. Matplotlib tam bu noktada devreye girip tüm o karmaşık sayıları gözümüzle görebileceğimiz grafiklere döküyor. Zaman-hız çizgi grafiklerine bakarak aracın yolcuları sarsacak kadar sert fren yapıp yapmadığını anlıyor, LiDAR verilerini nokta grafiğine dökerek aracın etrafındaki engellerin kuşbakışı krokisini çıkarabiliyoruz. Hatta aynı ekranda alt alta çizdiğimiz çoklu grafikler sayesinde öndeki engel yaklaştığı anda fren basıncının kaç salise sonra devreye girdiğini ve aracın ne kadar sürede durduğunu tek bakışta teşhis edebiliyoruz.