Tabii, işte projenin genel yapısı hakkında bir özet:

---

# Türkçe Metin İşleme ve Analiz Projesi

## Proje Tanıtımı

Bu proje, Türkçe metinler üzerinde çeşitli ön işleme ve analiz işlemleri gerçekleştirmek amacıyla geliştirilmiştir. Proje, sosyal medya paylaşımlarından oluşan bir veri seti üzerinde çalışır. Amaç, metinlerin normalizasyonu, stop word'lerin kaldırılması, kara liste kelimelerinin analizi ve makine öğrenmesi ile metin sınıflandırma gibi adımları içeren bir çalışma sunmaktır.

## Proje Adımları

1. **Veri İşleme Adımları:**
   - Metin normalizasyonu: Metinler küçük harfe dönüştürülmüş, noktalama işaretleri ve rakamlar temizlenmiş, fazla boşluklar kaldırılmıştır.
   - Stop words kaldırma: Türkçe stop words listesi kullanılarak metinlerden anlamsız kelimeler çıkarılmıştır.
   - Boş değerlerin temizlenmesi: Veri setinde bulunan boş veya eksik değerler temizlenmiştir.

2. **Kara Liste Kelime Analizi:**
   - Kara liste kelimeleri, belirli argo ve hakaret içeren kelimeleri tespit etmek için kullanılmıştır. Bu kelimelerin metinlerdeki kullanım sıklıkları hesaplanmış ve analiz edilmiştir.

3. **Makine Öğrenmesi ile Metin Sınıflandırma:**
   - Veri seti, negatif ve pozitif olarak etiketlenmiştir.
   - Veri seti, eğitim ve test verisi olarak ikiye ayrılmıştır.
   - TF-IDF yöntemi kullanılarak metinler sayısal verilere dönüştürülmüştür.
   - Destek Vektör Makineleri (SVM) modeli kullanılarak metin sınıflandırma modeli eğitilmiştir.
   - Modelin doğruluğu hesaplanmış ve sonuçlar raporlanmıştır.

4. **Sonuçlar:**
   - Eğitilen model, %85.34 doğruluk oranı ile metinleri doğru bir şekilde sınıflandırmıştır.
   - Kara liste kelimelerinin kullanım sıklıkları başarıyla analiz edilmiş ve görselleştirilmiştir.

5. **Katkıda Bulunanlar:**
   - Proje Sahibi: İsim Soyisim
   - Geliştirici: İsim Soyisim
   - Katkıda Bulunanlar: İsim Soyisim

---

Bu proje, Türkçe metin analizi ve işleme üzerine yapılan çalışmaları içermekte olup, metinlerin anlam düzeyinde analiz edilmesi ve makine öğrenimi modelleriyle sınıflandırılması üzerine odaklanmaktadır. Daha fazla bilgi veya proje ile ilgili sorularınız için iletişime geçebilirsiniz.
