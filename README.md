# MNIST Veri Seti ile MLP Tabanlı El Yazısı Rakam Tanıma Projesi

Bu proje, MNIST el yazısı rakam veri setini kullanarak sıfırdan bir **Multi-Layer Perceptron (MLP)** modeli geliştirmeyi, modelin başarımını test etmeyi ve gerçek dünya verisi (öğrenci el yazısı) üzerindeki genelleştirme yeteneğini (Distribution Shift) analiz etmeyi amaçlamaktadır.

---

## 1. Model Mimarisi ve Hiperparametreler

Ödev kuralları gereği **CNN kullanılmamış**, model tamamen çok katmanlı algılayıcı (MLP) mimarisi üzerine inşa edilmiştir.

* **Girdi Katmanı (Input):** $28 \times 28 = 784$ Piksel (Flatten edilmiş tek boyutlu vektör)
* **Gizli Katmanlar (Hidden Layers):**
  * **Gizli Katman 1:** Linear(784 $\rightarrow$ 256) + ReLU Aktivasyon
  * **Gizli Katman 2:** Linear(256 $\rightarrow$ 128) + ReLU Aktivasyon
* **Çıktı Katmanı (Output):** Linear(128 $\rightarrow$ 10) (0–9 arasındaki 10 sınıf için)
* **Aktivasyon Fonksiyonu:** ReLU
* **Kayıp (Loss) Fonksiyonu:** CrossEntropyLoss
* **Optimizasyon (Optimizer):** Adam (Learning Rate = 0.001)
* **Batch Size:** 64
* **Epoch Sayısı:** 10
* **Veri Seti Ayrımı:** %80 Training (48.000), %20 Validation (12.000), 10.000 Test

---

## 2. Deney 1 Sonuçları ve Grafikler (MNIST)

Model, Google Colab (Tesla T4 GPU - CUDA) üzerinde 10 epoch boyunca eğitilmiş ve aşağıdaki performans değerleri elde edilmiştir:

* **Eğitim Süresi:** ~131.75 Saniye
* **Training Loss / Accuracy:** 0.0218 / **%99.23**
* **Validation Loss / Accuracy:** 0.1030 / **%97.59**
* **Test Accuracy (Nihai Sınav Notu):** **%97.75**

### Grafikler ve Yorum:
- **Loss Grafiği:** Eğitim ve Doğrulama kaybı (Loss) kararlı bir şekilde düşmüştür. Validation kaybının düzleşmesi modelin aşırı öğrenmeye (overfitting) girmediğini gösterir.
- **Accuracy Grafiği:** Model 10 epoch sonunda hem eğitim hem de doğrulama veri kümelerinde %97'nin üzerinde kararlı bir doğruluk oranına ulaşmıştır.

---

## 3. Gerçek Hayat El Yazısı Testi ve Dağılım Kayması (Distribution Shift)

Öğrenci el yazısıyla yazılan `0, 1, 2, 4, 5` rakamlarının görselleri kırpılmış, 28x28 boyutuna getirilmiş, renkleri MNIST standartlarına uygun şekilde siyah arka plan-beyaz yazı formatına (invert) çevrilmiş ve eğitilen modele verilmiştir.

### Değerlendirme ve Teknik Analiz:
Model MNIST resmi test setinde **%97.75** gibi son derece yüksek bir başarı elde etmesine rağmen, kendi el yazısı görsellerimde düşük doğruluk oranları göstermiştir. Bu durumun temel teknik nedenleri şunlardır:

1. **MLP Mimarisi ve Konum Hassasiyeti (Spatial Bias):** 
   MLP modelleri görüntüdeki uzamsal (spatial) ilişkileri koruyamaz; görüntüyü tek boyutlu 784 piksellik bir çizgiye dönüştürür. Evrişimli katmanlar (CNN) kullanılmadığı için, rakamın kare içerisindeki birkaç piksel sağa/sola kayması veya açısının değişmesi MLP'nin pikselleri yanlış eşleştirmesine yol açar.
2. **Çizgi Kalınlığı ve Kontrast (Stroke Thickness Shift):** 
   MNIST veri setindeki rakamlar kalın fırça darbelerine sahiptir. Kağıt-kalem ile çekilen fotoğraflar 28x28 piksel boyutuna küçültüldüğünde çizgiler oldukça ince kalmış ve model tarafından gürültü (noise) olarak algılanmıştır.
3. **Distribution Shift (Dağılım Kayması):** 
   Eğitim verisinin (MNIST) istatistiksel dağılımı ile test verisinin (gerçek dünya el yazısı) dağılımı arasındaki fark, derin öğrenme modellerinin genelleştirme (generalization) sınırlarını ve veri ön işlemenin önemini net bir şekilde ortaya koymuştur.
