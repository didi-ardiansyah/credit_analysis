# 📊 Credit Risk Scorecard Modeling
> Membangun Credit Scorecard berbasis industri perbankan untuk memprediksi risiko gagal bayar nasabah

---

## 🎯 Latar Belakang

Lembaga keuangan menghadapi risiko kredit setiap kali menyetujui pinjaman. Proyek ini membangun sistem **credit scoring** menggunakan pendekatan standar industri perbankan — Weight of Evidence (WoE) + Logistic Regression — yang menghasilkan **credit scorecard** berupa poin per karakteristik nasabah, mirip dengan sistem yang digunakan bank dan multifinance di Indonesia.

---

## ❓ Pertanyaan Bisnis

1. Faktor apa yang paling berpengaruh terhadap risiko gagal bayar?
2. Pada skor berapa sebaiknya lembaga keuangan menetapkan batas *cut-off* approval?
3. Seberapa handal model ini diukur dari standar industri perbankan (KS Statistic, Gini)?

---

## 📁 Dataset

| Detail | Keterangan |
|---|---|
| **Nama** | Give Me Some Credit |
| **Sumber** | [Kaggle Competition](https://www.kaggle.com/c/GiveMeSomeCredit) |
| **Ukuran** | 150.000 baris × 11 kolom |
| **Target** | `SeriousDlqin2yrs` — gagal bayar 90+ hari dalam 2 tahun |
| **Bad Rate** | ~6.7% (data tidak seimbang / imbalanced) |

---

## 🔄 Alur Metodologi

```
Data Mentah
    │
    ▼
EDA & Data Quality Check
(missing values, outlier, distribusi, bad rate)
    │
    ▼
Preprocessing
(imputasi median, capping winsorizing, train/test split 70:30)
    │
    ▼
WoE & Information Value
(binning otomatis, seleksi variabel IV ≥ 0.02, transformasi WoE)
    │
    ▼
Logistic Regression
(training dengan data ber-WoE, validasi koefisien)
    │
    ▼
Credit Scorecard
(konversi model → poin per bin, base score 600, PDO 20)
    │
    ▼
Evaluasi Model
(AUC-ROC, Gini Coefficient, KS Statistic)
    │
    ▼
Rekomendasi Bisnis
(analisis desil, cut-off policy, simulasi dampak)
```

---

## 🛠️ Tools & Library

| Kategori | Library |
|---|---|
| Data Wrangling | `pandas`, `numpy` |
| WoE & Scorecard | `scorecardpy` |
| Modeling | `scikit-learn` |
| Visualisasi | `matplotlib`, `seaborn` |

---

## 📈 Hasil & Evaluasi Model

| Metrik | Training | Testing | Threshold Industri |
|---|---|---|---|
| **AUC-ROC** | 0.8573 | 0.8561 | > 0.70 ✅ |
| **Gini Coefficient** | 0.7147 | 0.7123 | > 0.40 ✅ |
| **KS Statistic** | 0.5576 | 0.5583 | > 0.30 ✅ |

---

## 🏦 Output Utama

### 1. Credit Scorecard
Tabel poin per variabel per bin — dapat langsung digunakan sebagai panduan keputusan kredit.

```
Contoh ilustrasi:
  Usia 25–35 tahun          → +15 poin
  Usia > 50 tahun           → +22 poin
  Debt Ratio > 0.5          → −18 poin
  Pernah telat 1x (30–59h)  → −25 poin
```

### 2. Kebijakan Cut-Off

| Segmen Keputusan | Rentang Skor | Aksi |
|---|---|---|
| **Approve** | > 610 | Setujui otomatis |
| **Review** | 586 – 610 | Analisis manual |
| **Reject** | < 586 | Tolak otomatis |

### 3. Analisis Desil
Tabel distribusi skor vs bad rate per desil populasi untuk validasi discriminatory power model.

---

## 💡 Insight Kunci

- **WoE/IV** lebih unggul dari one-hot encoding untuk credit scoring karena menangkap hubungan non-linear antara variabel dan target secara otomatis
- **Logistic Regression** dipilih karena *explainability* — wajib untuk kepatuhan regulasi OJK
- **KS Statistic** dan **Gini** lebih relevan dari *accuracy* untuk data kredit yang sangat tidak seimbang
- Cut-off optimal harus mempertimbangkan **risk appetite** bisnis, bukan hanya optimasi matematis

---

## 📂 Struktur File

```
credit_analysis/
│
├── credit_risk_analysis.ipynb   ← Notebook utama
├── cs-training.csv               ← Dataset (download dari Kaggle)
└── README.md                     ← Dokumen ini
```

---

## 🚀 Cara Menjalankan

```bash
# 1. Clone repository
git clone https://github.com/didi-ardiansyah/credit_analysis.git
cd credit_risk_analysis

# 2. Install dependencies
pip install pandas numpy scikit-learn matplotlib seaborn scorecardpy

# 3. Download dataset
# → https://www.kaggle.com/c/GiveMeSomeCredit/data
# → Simpan 'cs-training.csv' di folder ini

# 4. Jalankan notebook
notebook credit_risk_analysis.ipynb
```

---

## 📚 Referensi

- Siddiqi, N. (2006). *Credit Risk Scorecards*. Wiley.
- OJK. Peraturan terkait manajemen risiko kredit perbankan Indonesia.
- Kaggle. *Give Me Some Credit* Competition Dataset.

---

*Proyek ini merupakan implementasi mandiri metodologi credit scoring berbasis standar industri perbankan.*
