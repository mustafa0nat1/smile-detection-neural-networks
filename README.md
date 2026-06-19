# 🎯 SMILES Veri Seti ile Gülümseme Tespiti: MLP ve CNN Karşılaştırmalı Analizi



## 📊 Veri Seti ve Ön İşleme (Data Preprocessing)

[cite_start]Projede **SMILES** veri seti kullanılmıştır[cite: 312]. Görüntülerin işlenmesi ve ağa hazır hale getirilmesi için şu adımlar uygulanmıştır:
* [cite_start]**Veri Hacmi:** Veri setinde toplam 13.165 adet görüntü yer almaktadır[cite: 300, 313]. [cite_start]Sınıf dağılımında gülümsemeyen görüntüler, gülümseyenlerin yaklaşık 2.5 katı olduğundan dengesiz bir yapı söz konusudur[cite: 313, 314].
* [cite_start]**Boyutlandırma & Renk Dönüşümü:** Hafıza kullanımını 3 kat azaltmak ve eğitimi hızlandırmak amacıyla görüntüler gri tonlamaya çevrilmiş ve 64x64 piksel boyutuna küçültülmüştür[cite: 315, 316, 317].
* [cite_start]**Normalizasyon:** Gradyan akışını kararlı kılmak için piksel değerleri 0-1 aralığına ölçeklenmiştir[cite: 300, 319].
* [cite_start]**Veri Bölümlendirme:** Veri seti, sınıf dağılımı korunarak (stratified) %80 eğitim ve %20 test olacak şekilde ayrılmıştır[cite: 301, 320].

---

## 🧠 Model Mimarileri ve Optimizasyon Adımları

[cite_start]Her iki mimari için de öncelikle sığ/basit bir ilk versiyon (baseline) oluşturulmuş, ardından 4'er farklı hiperparametre bağımsız olarak test edilerek en iyi konfigürasyonlara ulaşılmıştır[cite: 298, 304, 624].

### 1. Katmanlı Algılayıcı (MLP) Modeli
* [cite_start]**İlk Versiyon (Baseline):** 4096 giriş nörönu, 8 nöronlu tek bir gizli katman (ReLU aktivasyonlu) ve 1 çıkış nöronundan oluşmaktadır[cite: 328].
* [cite_start]**Uygulanan Optimizasyonlar[cite: 304]:**
  * [cite_start]Gizli katmandaki nöron sayısı (16, 64, 128) test edilmiş ve 128 nöronun en iyi kapasiteyi sunduğu görülmüştür[cite: 405, 406].
  * [cite_start]Öğrenme oranı (0.01, 0.001, 0.0001) incelenmiş, Adam optimizatörünün varsayılan değeri olan 0.001 öne çıkmıştır[cite: 412, 416].
  * Aktivasyon fonksiyonlarında ReLU, Tanh ve Sigmoid karşılaştırılmış; [cite_start]ReLU dengeli yapısıyla seçilmiştir[cite: 423, 424].
  * [cite_start]Aşırı öğrenmeyi (overfitting) engellemek için Dropout oranları (0.0, 0.2, 0.5) kıyaslanmış ve 0.2 optimum bulunmuştur[cite: 429, 432, 433].

### 2. Evrişimli Sinir Ağı (CNN) Modeli
* [cite_start]**İlk Versiyon (Baseline):** 1 adet Conv2d katmanı (8 filtre, 3x3 kernel, padding=1), ReLU aktivasyonu, MaxPool2d (2x2) ve tek bir tam bağlı katmandan (Linear) oluşan sığ bir mimaridir[cite: 445, 458, 459, 461, 462, 463].
* [cite_start]**Uygulanan Optimizasyonlar[cite: 304]:**
  * [cite_start]Özellik çıkarım kalitesini artırmak için filtre sayısı (8, 16, 32, 64) test edilmiş ve 32 filtre en dengeli sonucu vermiştir[cite: 500, 514, 515].
  * [cite_start]Öğrenme oranı optimizasyonunda 0.001 yakınsama hızıyla en başarılı değer olmuştur[cite: 520, 523, 524].
  * [cite_start]Düzenlileştirme adına Dropout (0.0, 0.2, 0.5) test edilmiş, daha kararlı öğrenme davranışı için nihai modelde 0.2 seçilmiştir[cite: 526, 531, 533, 534].
  * Optimizasyon algoritmalarında Adam ve SGD karşılaştırılmış; [cite_start]Adam adaptif öğrenme oranı sayesinde 20 epoch bütçesinde SGD'yi net bir şekilde geride bırakmıştır[cite: 542, 544, 545, 546].

---

## 📈 Performans Karşılaştırmaları ve Sonuçlar

Yapılan tüm deneysel çalışmaların özeti ve nihai modellerin (M14 ve M27) başarı oranları aşağıdaki tabloda sunulmuştur:

| Model ID | Mimari | Uygulanan Modifikasyon / Değer | Train Loss | Test Loss | Train Accuracy | Test Accuracy |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **M1** | MLP | [cite_start]Başlangıç Modeli (8 Nöron, lr=0.001) [cite: 406] | [cite_start]0.3338 [cite: 406] | [cite_start]0.3782 [cite: 406] | [cite_start]86.23% [cite: 406] | [cite_start]82.87% [cite: 406] |
| **M14** | MLP | [cite_start]128 Nöron + ReLU + lr=0.001 + Dropout=0.2 [cite: 575] | - | - | [cite_start]85.81% [cite: 575] | [cite_start]85.11% [cite: 575] |
| **M15** | CNN | [cite_start]Başlangıç Modeli (8 Filtre, lr=0.001) [cite: 515, 575] | [cite_start]0.2620 [cite: 575] | [cite_start]0.3012 [cite: 575] | [cite_start]89.43% [cite: 515, 575] | [cite_start]87.50% [cite: 515, 575] |
| **M27** | CNN | [cite_start]**32 Filtre + Dropout=0.2 + lr=0.001 + Adam** [cite: 575] | - | - | [cite_start]~93% [cite: 575] | [cite_start]**~89.59%** [cite: 575, 607] |

### 🔍 Temel Çıkarımlar
1. [cite_start]**Mimari Üstünlük:** CNN'in optimize edilmemiş ilk versiyonu bile (%87.50), en iyi optimize edilmiş MLP modelinden (%85.11) daha yüksek test doğruluğu vermiştir[cite: 609, 627]. [cite_start]Bu durum, görüntü işleme görevlerinde doğru mimari seçiminin hiperparametre optimizasyonundan daha kritik olduğunu kanıtlamaktadır[cite: 609, 628].
2. [cite_start]**Mekânsal Yapı ve Parametre Tasarrufu:** CNN mimarisinin başarısı; görüntülerin 2 boyutlu uzaysal ilişkilerini korumasından, parametre paylaşımından ve translation invariance (konuma duyarsızlık) yeteneğinden kaynaklanmaktadır[cite: 579, 580, 608, 629]. [cite_start]CNN, MLP'ye kıyasla çok daha az parametreyle daha yüksek doğruluk yakalamıştır[cite: 620, 630].
3. [cite_start]**Alan Kayması (Domain Shift):** Geliştirilen en başarılı model kendi fotoğraflarımız üzerinde test edildiğinde doğruluk oranı %50 seviyelerine gerilemiştir[cite: 633]. [cite_start]Bu durum laboratuvar verisi ile gerçek dünya verisi arasındaki *domain shift* olgusunu doğrulamakta ve gelecekte veri artırma (data augmentation) ile transfer learning tekniklerinin kullanılmasının gerekliliğini göstermektedir[cite: 634, 635].

---

