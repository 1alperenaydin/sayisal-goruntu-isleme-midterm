# Klasik Görüntü İşleme Yöntemleriyle Mikroskobik Görüntülerde Hücre Segmentasyonu, Sayımı ve Zamansal Analizi

Üsküdar Üniversitesi Sayısal Görüntü İşleme dersi yüksek lisans ara sınav projesidir.

Bu projenin amacı, floresan mikroskopi görüntülerindeki hücreleri klasik görüntü işleme yöntemleriyle otomatik olarak saymak, tahminleri referans sayılarla karşılaştırmak ve hücre sayısının zaman içindeki değişimini incelemektir.

## Veri seti ve kare seçimi

Cell Tracking Challenge’ın Fluo-N2DH-GOWT1 eğitim arşivi kullanılmıştır. Defterin ilk hücresi arşivi indirip "veri-seti/" klasörüne açar. Resmî veri seti sayfası (https://celltrackingchallenge.net/2d-datasets/) · Veri seti (https://data.celltrackingchallenge.net/training-datasets/Fluo-N2DH-GOWT1.zip)

Kare seçiminde kullanılan rastgelelik tohumu (seed) 11043’tür. "01" serisinden 10 geliştirme ve 5 doğrulama karesi, "02" serisinden 15 test karesi seçilmiştir. Test kümesi, her biri beş ardışık kare içeren üç ayrı bloktan oluşur (35–39, 56–60 ve 77–81). Seçilen karelerin tamamı "selected_frames.csv" dosyasında listelenmiştir.

## Kurulum ve çalıştırma

Uygulama Python ile hazırlanmıştır. Kullanılan OpenCV, NumPy, pandas, Matplotlib ve scikit-image sürümleri "requirements.txt" dosyasında belirtilmiştir.

### Google Colab

1. Depodaki "main.ipynb" dosyasını Colab’da açın.
2. Depoda kayıtlı test tahminlerini değerlendirmek için "test_predictions_locked.csv" dosyasını indirin. Colab’ın solundaki Dosyalar panelini açın ve herhangi bir alt klasöre girmeden dosyayı yükleyin.
3. Çalışma zamanı → Tümünü çalıştır seçeneğini kullanın. Hücreler veri setinin indirilmesinden test değerlendirmesine kadar sırayla çalışır.

### Yerel Jupyter ortamı

1. Depoyu indirin ve terminalde depo klasörünü açın.
2. Jupyter’ın kullandığı Python ortamında "python -m pip install -r requirements.txt" komutunu çalıştırın.
3. "main.ipynb" dosyasını depo klasöründe açın ve tüm hücreleri sırayla çalıştırın.

Defter, çalışma klasöründe "test_predictions_locked.csv" varsa kayıtlı tahminleri okur. Dosya yoksa her iki yöntemin tahminlerini test referanslarını okumadan önce hesaplayıp kaydeder. Rapordaki test süreleri, depodaki "test_predictions_locked.csv" dosyasından hesaplanmıştır. Yeniden ölçülen süreler çalışma ortamına göre değişebilir.

## Hücre sayım yöntemi

Başlangıç yöntemi (baseline) her görüntü için Otsu eşiğini hesaplar ve oluşan ikili maskedeki bağlı bölgeleri 8-komşulukla sayar.

Nihai yöntem (E15) önce CLAHE ile yerel kontrastı düzenler (kontrast sınırı 2,0 ve 8×8 bölge), ardından 5×5 medyan filtre uygular. Filtrelenen görüntü, 10 geliştirme karesinden belirlenen sabit eşik (T=36) ile ikili maskeye dönüştürülür. Maskeye 5×5 elips biçimli yapılandırma elemanıyla önce açma, sonra kapama uygulanır. Son maskedeki bağlı bölgeler alan filtresi uygulanmadan 8-komşulukla sayılır.

## Deney ve sonuç dosyaları

| Dosya | İçerik |
|---|---|
| "selected_frames.csv" | Seçilen karelerin seri ve kare numaraları, dosya adları, veri grupları ve seed değeri |
| "experiments.csv" | E01–E16 deneylerinde kullanılan yöntemler, parametreler ve geliştirme kümesindeki ortalama mutlak hata (MAE) değerleri |
| "validation_results.csv" | Üç adayın doğrulama MAE ve RMSE değerleri, ortalama işlem süreleri ve nihai yöntem seçimi |
| "final_pipeline.json" | Nihai yöntemin işlem sırası, parametreleri ve sayım ayarları |
| "test_predictions_locked.csv" | 15 test karesi için baseline ve E15 tahminleri ile işlem süreleri |
| "test_results.csv" | Aynı tahmin ve sürelere ek olarak referans hücre sayıları ve her iki yöntemin kare başına mutlak hataları |

## Grafikler

Defter çalıştırıldığında yoğunluk histogramları, filtre ve segmentasyon karşılaştırmaları, morfolojik işlem maskeleri ve E15’in ara çıktıları ilgili hücrelerin altında gösterilir. Test bölümünde üç zaman bloğu için referans ve tahmin eğrileri ile üç örnek görüntü üzerinde E15’in belirlediği bölgeler yer alır. GitHub sürümünde görselleri üreten kod korunmuş, gömülü görüntü çıktıları temizlenmiştir.

## Defter sürümleri

Depo, ilk test değerlendirmesinden sonra yeniden oluşturulmuştur. İlk 12 bölümü içeren sürüme “Test öncesi kod, parametreler ve tahminler” commit’inden erişilebilir. Bölüm 13–15’i de içeren tam sürüm “Test sonrası kod, sonuçlar, analiz ve tartışma” commit’inde yer alır. Her iki sürüm de "main.ipynb" adını taşıdığı için depo ana sayfasında güncel tam sürüm görünür.
