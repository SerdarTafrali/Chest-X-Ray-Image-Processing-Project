# Chest X-ray Image Processing Project

Bu repo, [ChestX-ray8](https://nihcc.app.box.com/v/ChestXray-NIHCC) veri setinden örnek göğüs röntgeni (X-ray) görüntülerini kullanarak temel görüntü işleme ve segmentasyon adımlarını içeren bir proje içerir. Projede sırasıyla kırpma, kontrast iyileştirme, histogram eşitleme, gürültü azaltma, eşikleme (thresholding), morfolojik işlemler ve bağlı bileşen analizi (connected components) adımları uygulanmıştır.

## İçindekiler

- [Genel Bilgi](#genel-bilgi)
- [Kullanılan Teknolojiler ve Kütüphaneler](#kullanılan-teknolojiler-ve-kütüphaneler)
- [Kurulum](#kurulum)
- [Proje Adımları](#proje-adımları)
- [Nasıl Çalıştırılır?](#nasıl-çalıştırılır)
- [Çıktılar](#çıktılar)


---

## Genel Bilgi

Bu projede küçük bir ChestX-ray8 alt kümesi (mini veri seti) üzerinde aşağıdaki işlemleri adım adım uygulamaktayız:

1. **Veri Keşfi (Exploratory Data Analysis)**  
   - Veri setindeki temel istatistiklerin, sütunların ve örnek görüntülerin incelenmesi.

2. **Rastgele Görsellerin Görselleştirilmesi**  
   - Veri çerçevesinden rastgele 9 görüntü seçilerek, orijinal hallerinin gösterilmesi.

3. **Görüntü İşleme ve İyileştirme**  
   - **Kırpma (Crop)**: Kenarlardaki etiket vb. gürültüleri azaltmak için sabit sınırla kesme.
   - **Kontrast Germe & Histogram Eşitleme**: Düşük veya yüksek yoğunluklu bölgelerin görünürlüğünü artırmak.
   - **Gürültü Azaltma (Median/Gaussian Filter)**: Segmentasyon aşamalarında gürültünün etkisini düşürmek.

4. **Eşikleme (Thresholding)**  
   - **Global Threshold** ve **Otsu** yöntemleriyle pikselleri ikili maskeleme (binary mask) haline getirme.
   - Opsiyonel olarak çoklu eşik (multi-threshold) yöntemlerine genişletilebilir.

5. **Post-Processing**  
   - **Morfolojik Operatörler (Opening, Closing)**: Maske üzerinde küçük gürültüleri temizleme ve boşlukları doldurma.
   - **Connected Components**: Segmentasyondan sonra bağıl bileşen analizi ile akciğer veya diğer yapıların alan, centroid, bounding box vb. özniteliklerini çıkarma.
   - **Filtrelenmiş Label Seçimi**: En büyük alanı temsil eden (veya en büyük iki alanı temsil eden) label(ları) maske olarak belirleme.

6. **Maske Uygulaması**  
   - Son maske ile orijinal (veya kırpılmış) görüntüyü çarparak, akciğer bölgesini vurgulama.

---

## Kullanılan Teknolojiler ve Kütüphaneler

- **Python 3.7+** veya üstü
- **NumPy** (sayısal işlemler)
- **Pandas** (veri çerçevesi yönetimi)
- **Matplotlib, Seaborn** (görselleştirme)
- **OpenCV (cv2)** (görüntü işleme ve bağlı bileşen analizi fonksiyonları)
- **Scikit-image (skimage)** (ör. `exposure`, `filters`, `morphology`)
- **OS** (dosya yönetimi)
