# SubSmart Analytics 🔍
### Kim Gidecek, Kim Kalacak, Kim Gereksiz Ödüyor? — Telekom Verisinden Akıllı İş Kararları Üretmek

[![Python](https://img.shields.io/badge/Python-3.x-blue)](https://python.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)](https://jupyter.org)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

---

## 📌 Proje Hakkında

**SubSmart**, insanların kullanmadıkları servislere para ödemeye devam ettiği "Subscription Fatigue" (Abonelik Yorgunluğu) problemini çözmek için tasarlanmış bir FinTech abonelik yönetim asistanıdır.

Bu proje, SubSmart'ın veri madenciliği motorunu inşa etmektedir. Motor, bir telekom şirketinin müşteri verisini analiz ederek şu 4 soruyu yanıtlar:

| Soru | Yöntem |
|------|--------|
| Bu müşteri kim? | K-Means Kümeleme |
| Hangi servisleri birlikte alıyor? | Apriori Birliktelik Analizi |
| Yakında aboneliği iptal edecek mi? | Random Forest (Churn Tahmini) |
| Kullanmadığı servise para ödüyor mu? | Isolation Forest (Anomali Tespiti) |

---

## 🚀 Kullanılan Teknolojiler

```
Python 3.x
├── pandas, numpy          # Veri işleme
├── scikit-learn           # K-Means, Random Forest, Isolation Forest
├── mlxtend                # Apriori, Association Rules
├── shap                   # Model yorumlanabilirliği (XAI)
├── matplotlib, seaborn    # Görselleştirme
└── networkx               # Birliktelik ağı grafiği
```

---

## 📊 Veri Seti

**IBM Telco Customer Churn** (Kaggle)
- 7.043 satır, 21 sütun → temizlik sonrası 7.032 satır
- Hedef değişken: `Churn` (%73 kaldı / %27 iptal)
- Özellikler: `tenure`, `MonthlyCharges`, `TotalCharges`, `Contract`, `InternetService`, 15+ servis sütunu

---

## 🔧 Pipeline Mimarisi

```
IBM Telco Verisi
      │
      ▼
Veri Ön İşleme
(TotalCharges temizliği, Encoding, StandardScaler)
      │
      ├──► K-Means Kümeleme ──────────────┐
      │    (3 segment: Sadık / Standart /  │ cluster_id
      │     Yeni & Riskli)                 │
      │                                    │
      ├──► Apriori Birliktelik Analizi ────┤ combo_features
      │    (Lift > 1.5 kurallar)           │
      │                                    │
      │    ┌──────────────────────────────┘
      │    │ Zenginleştirilmiş Feature Set
      ▼    ▼
   Random Forest ──► Churn Tahmini
   (ROC-AUC: 0.818)
      │
      ▼
   Isolation Forest ──► Zombi Abonelik Tespiti
   (352 anomali tespit edildi)
      │
      ▼
   Simpson Paradoksu Analizi
   SHAP Model Yorumlanabilirliği
   Gerçek Zamanlı Simülasyon
```

---

## 📈 Sonuçlar

### Model Performansı

| Metrik | Baseline DT | Pipeline RF |
|--------|-------------|-------------|
| ROC-AUC | 0.646 | **0.818** |
| F1-Score | 0.483 | **0.553** |
| Avg. Precision | 0.366 | **0.604** |

### Müşteri Segmentleri

| Segment | Müşteri Sayısı | Churn Oranı |
|---------|---------------|-------------|
| Sadık Müşteriler | 2.282 | %15.6 |
| Standart / Düşük Bütçeli | 2.137 | %12.4 |
| Yeni & Riskli | 2.613 | **%47.7** |

### Anomali Tespiti
- **352 Zombi Abonelik** tespit edildi (%5)
- Zombi müşterilerin churn oranı sadece %10.5 — gitmiyorlar ama gereksiz ödüyorlar

### Simpson Paradoksu
- Fiber optik genel: **%41.9** churn → "riskli görünüyor"
- Fiber + 2 yıllık sözleşme: **%7.2** churn → aslında en sadık grup
- Gizli değişken: Sözleşme tipi

---

## 🗂️ Proje Yapısı

```
SubSmart-Analytics/
│
├── Data_Preprocessing.ipynb    # Ana notebook (tüm pipeline)
├── WA_Fn-UseC_-Telco-Customer-Churn.csv  # Veri seti
├── README.md                   # Bu dosya
└── requirements.txt            # Gerekli kütüphaneler
```

---

## ⚙️ Kurulum

```bash
# Repoyu klonla
git clone https://github.com/kullanici-adi/SubSmart-Analytics.git
cd SubSmart-Analytics

# Gerekli kütüphaneleri yükle
pip install -r requirements.txt

# Jupyter Notebook'u başlat
jupyter notebook Data_Preprocessing.ipynb
```

---

## 📦 requirements.txt

```
pandas
numpy
matplotlib
seaborn
scikit-learn
mlxtend
shap
networkx
jupyter
```

---

## 🎯 Özellikler

- ✅ **Entegre Pipeline**: K-Means ve Apriori çıktıları Random Forest'a girdi olarak besleniyor
- ✅ **Simpson Paradoksu Analizi**: Yanıltıcı genel istatistiklerin altındaki gerçeği ortaya çıkarıyor
- ✅ **SHAP Yorumlanabilirlik**: Model kararlarının arkasındaki gerekçeleri görselleştiriyor
- ✅ **Gerçek Zamanlı Simülasyon**: Yeni müşteri için anlık churn riski + segment + zombi uyarısı
- ✅ **İnteraktif Dashboard**: HTML tabanlı slider simülasyonu (Jupyter içinde çalışır)

---

## 📚 Kaynaklar

1. Han, J., Pei, J., & Tong, H. (2022). *Data Mining: Concepts and Techniques* (4th ed.). Morgan Kaufmann.
2. Agrawal, R., & Srikant, R. (1994). Fast Algorithms for Mining Association Rules. *VLDB '94*, 487–499.
3. Breiman, L. (2001). Random Forests. *Machine Learning, 45*(1), 5–32.
4. Lundberg, S. M., & Lee, S.-I. (2017). A Unified Approach to Interpreting Model Predictions. *NeurIPS, 30*.
5. Liu, F. T., Ting, K. M., & Zhou, Z.-H. (2008). Isolation Forest. *ICDM '08*, 413–422.

---

## 👩‍💻 Geliştirici

**Zeynep Sare Evcil**
Yapay Zeka Mühendisliği
Veri Madenciliği Dersi Projesi | Bahar 2026
