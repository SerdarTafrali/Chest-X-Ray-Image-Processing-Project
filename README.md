# Chest X-ray Image Processing Project

Bu repo, [ChestX-ray8](https://nihcc.app.box.com/v/ChestXray-NIHCC) veri setinden örnek göğüs röntgeni (X-ray) görüntülerini kullanarak temel görüntü işleme ve segmentasyon adımlarını içeren bir proje içerir. Projede sırasıyla kırpma, kontrast iyileştirme, histogram eşitleme, gürültü azaltma, eşikleme (thresholding), morfolojik işlemler ve bağlı bileşen analizi (connected components) adımları uygulanmıştır.

---
## İçindekiler

1. [Genel Bilgi](#genel-bilgi)  
2. [Kullanılan Teknolojiler ve Kütüphaneler](#kullanılan-teknolojiler-ve-kütüphaneler)  
3. [Proje Adımları](#proje-adımları)  
    - [1. Kütüphanelerin İçe Aktarılması](#1-kütüphanelerin-içe-aktarılması)  
    - [2. Görüntü Yükleme ve Görselleştirme](#2-görüntü-yükleme-ve-görselleştirme)  
    - [3. Görüntü İşleme ve İyileştirme (Pre-processing)](#3-görüntü-işleme-ve-iyileştirme-pre-processing)  
    - [4. Thresholding (Eşikleme)](#4-thresholding-eşikleme)  
    - [5. Post-processing](#5-post-processing)  
    - [6. Sonuçlar](#6-sonuçlar)  
4. [Çıktılar](#çıktılar)

---

## Genel Bilgi

ChestX-ray8 veri seti, göğüs röntgeni görüntüleri üzerine çeşitli hastalıkların etiketlendiği kapsamlı bir veri kümesidir. Bu projenin amacı, veri setindeki röntgenlerin ön işleme aşamalarından geçirilerek akciğerlerin segmentasyonunu gerçekleştirmektir. Aşağıdaki basamaklar izlenmiştir:

- Kenarlardan **kırpma (crop)** yapılarak gereksiz bölgelerin atılması  
- **Kontrast iyileştirme** (stretching, histogram eşitleme, gamma düzeltme)  
- **Gürültü azaltma** (median ve Gaussian bulanıklaştırma)  
- **Thresholding** (Otsu, global eşik değerleri)  
- **Morfolojik işlemler** (opening, closing)  
- **Bağlı bileşen analizi** ve temel özniteliklerin çıkarımı  

Böylece elde edilen **ikili maske** (binary mask) ile orijinal kırpılmış görüntü çarpılarak sadece akciğer alanları ön plana çıkarılmıştır.

---

## Kullanılan Teknolojiler ve Kütüphaneler

- **Python 3**  
- **NumPy**: Sayısal işlemler ve dizi manipülasyonu  
- **Pandas**: Veri işleme ve DataFrame yapısı  
- **Matplotlib, Seaborn**: Veri görselleştirme  
- **OpenCV (cv2)**: Görüntü işleme, bağlı bileşen analizi  
- **scikit-image**: Histogram eşitleme, threshold işlemleri, morfolojik fonksiyonlar  
- **Google Colab**: Çevrimiçi kod çalıştırma ve sonuç görselleştirme ortamı

---

## Proje Adımları

### 1. Kütüphanelerin İçe Aktarılması

- `pandas, numpy, matplotlib, seaborn, cv2, os` gibi temel kütüphaneler projeye dahil edilir.  
- `scikit-image` içerisinden `exposure, filters, morphology` gibi işlevler kullanılır.  
- Görsellerin yüklenebilmesi için ilgili veri yolunun (`os.path.join`) ayarlanması önemlidir.

### 2. Görüntü Yükleme ve Görselleştirme

1. **Veri Setinin Hazırlanması**  
   - ChestX-ray8 veri setinin küçük bir örneği klonlanır (ör. GitHub repo).
   - Röntgen görüntülerinin (ör. `.png` formatında) bulunduğu klasör (`images_small/`) belirlenir.

2. **Rastgele Görüntüler Seçilmesi**  
   - DataFrame içindeki `Image` sütunundan 9 rastgele görüntü seçilir.  
   - Bu 9 görüntü bir grid (3×3) şeklinde görselleştirilir; röntgenlerin genel yapısı incelenir.

### 3. Görüntü İşleme ve İyileştirme (Pre-processing)

1. **Kırpma (Crop)**  
   - Her bir röntgen kenarlardan belirli bir `border` (örneğin 50 piksel) değeri kadar kırpılır. Böylece gereksiz kısımlar atılır, asıl ilgi alanı (akciğer bölgesi) korunmaya çalışılır.

2. **Kontrast Germe (Stretching) ve Histogram Eşitleme (Equalization)**  
   - Minimum ve maksimum piksel değerleri kullanılarak yoğunluklar `[0, 255]` aralığına gerilir (stretching).  
   - `exposure.equalize_hist` fonksiyonuyla piksellerin dağılımı dengelenir, karanlık kısımlar aydınlanır.  
   - **Gamma düzeltmesi** (ör. `gamma=1.2`) ile insan gözüne daha uygun bir parlaklık elde edilir.

3. **Gürültü Azaltma**  
   - **Median filtre** ve **Gaussian filtre** uygulanarak yüksek frekanslı gürültü azaltılır. Kod, median filtre sonucunu sonraki eşikleme aşamasında kullanmıştır.

### 4. Thresholding (Eşikleme)

- **Global Threshold (sabit 127)** ve **Otsu** yöntemi ile maske oluşturma denenir.  
- Röntgenlerin piksel dağılımına göre **Otsu** genellikle daha stabil sonuçlar vermektedir.

### 5. Post-processing

1. **Morfolojik İşlemler**  
   - **Opening (Açma)** ile küçük beyaz gürültüler, **Closing (Kapama)** ile maske içindeki boşluklar doldurulur.

2. **Bağlı Bileşen Analizi (Connected Components)**  
   - Eşiklenmiş ve morfolojik işlemlerden geçmiş maske üzerinde her bir nesneye ayrı label verilir.  
   - Label’ların alanı, centroid’i, ortalama yoğunluk değeri gibi öznitelikler hesaplanır.  
   - Genelde en büyük 1 veya 2 bileşen akciğerleri temsil ettiği için filtreleme adımında bu bileşen(ler) seçilir.

3. **Final Maske ve Orijinal Görüntü Çarpımı**  
   - Son olarak `cv2.bitwise_and` ile maske ve kırpılmış orijinal görüntü çarpılarak sadece akciğer bölgelerini içeren nihai sonuç elde edilir.

### 6. Sonuçlar

- Kırpılmış ve işlenmiş röntgenlerden akciğer alanları başarılı şekilde segment edilmiş olup, maske kullanılarak görüntünün geri kalanı sıfırlanmıştır.  
- Doku detaylarının daha rahat incelenebildiği, gürültü miktarının azaldığı ve segmentasyon kalitesinin arttığı gözlemlenmiştir.

---

## Çıktılar

Proje sonunda, her bir röntgen görüntüsü için:

1. **Orijinal Görsel ve Crop Edilmiş Versiyon**  
2. **Kontrast & Gamma Düzeltilmiş Görsel**  
3. **Gürültü Giderme Sonrası Görüntü**  
4. **Threshold (Otsu) Maskesi**  
5. **Morfolojik İşlemler Sonucu**  
6. **Bağlı Bileşen Analizi ile Seçilen Label**  
7. **Nihai Maske ve Orijinal (kırpılmış) Görüntünün Çarpımı**  

şeklinde bir özet grid halinde görselleştirilmiştir. Kod her görüntü üzerinde bu süreci yürütüp final çıktıları ekrana plot etmiştir.
