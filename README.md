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
| Variabel                | Rentang Nilai (Bin) |    Skor |
| ----------------------- | ------------------- | ------: |
| **Base Score**          | -                   | **590** |
| Pendapatan              | < 3.500             |      -1 |
|                         | 3.500 – < 5.000     |       0 |
|                         | 5.000 – < 8.000     |       0 |
|                         | ≥ 8.000             |      +1 |
| Debt Ratio              | < 0,40              |      +3 |
|                         | 0,40 – < 0,55       |      -3 |
|                         | 0,55 – < 0,70       |     -10 |
|                         | 0,70 – < 2,95       |     -16 |
|                         | ≥ 2,95              |      +6 |
| Telat 90 Hari           | Tidak pernah (0)    |      +6 |
|                         | Pernah (≥1)         |     -36 |
| Kredit Properti         | 0                   |      -4 |
|                         | 1                   |      +5 |
|                         | 2                   |      +3 |
|                         | ≥3                  |      -5 |
| Usia                    | < 43 tahun          |      -5 |
|                         | 43 – < 58 tahun     |      -1 |
|                         | 58 – < 64 tahun     |      +5 |
|                         | ≥ 64 tahun          |     +12 |
| Telat 60–89 Hari        | Tidak pernah (0)    |      +3 |
|                         | Pernah (≥1)         |     -25 |
| Jumlah Tanggungan       | 0                   |      +1 |
|                         | 1                   |      -1 |
|                         | 2                   |      -1 |
|                         | ≥3                  |      -2 |
| Credit Utilization Rate | < 0,20              |     +22 |
|                         | 0,20 – < 0,50       |      +6 |
|                         | 0,50 – < 0,86       |     -11 |
|                         | ≥ 0,86              |     -24 |
| Telat 30–59 Hari        | Tidak pernah (0)    |      +8 |
|                         | 1 kali              |     -13 |
|                         | ≥2 kali             |     -28 |

**Perhitungan Credit Score**

```text
Credit Score = Base Score + Poin (Setiap Variabel)
```

Semakin tinggi skor yang diperoleh, semakin rendah risiko kredit yang diprediksi oleh model.

### 2. Kebijakan Cut-Off

| Segmen Keputusan | Rentang Skor | Aksi             |
|:-----------------|:------------:|:-----------------|
|      **Approve** |        > 610 | Setujui otomatis |
|       **Review** |    586 – 610 |  Analisis manual |
|       **Reject** |        < 586 |   Tolak otomatis |

### 3. Analisis Desil
Tabel distribusi skor vs bad rate per desil populasi untuk validasi discriminatory power model.

> **D1 = Skor Terendah (Risiko Kredit Tertinggi)**
> **D10 = Skor Tertinggi (Risiko Kredit Terendah)**

| Desil  | Nasabah   | Bad Accounts | Rentang Skor| Rata2 Skor    |   Bad Rate |
| :----: | --------: | -----------: | :---------: | ------------: | ---------: |
|   D1   |     4,596 |        1,638 |   449–567   |         535.7 | **35.64%** |
|   D2   |     4,623 |          543 |   568–586   |         578.3 |     11.75% |
|   D3   |     4,313 |          256 |   587–598   |         592.6 |      5.94% |
|   D4   |     4,520 |          191 |   599–610   |         605.0 |      4.23% |
|   D5   |     4,583 |          141 |   611–620   |         615.5 |      3.08% |
|   D6   |     5,042 |          101 |   621–627   |         624.2 |      2.00% |
|   D7   |     3,918 |           42 |   628–633   |         630.5 |      1.07% |
|   D8   |     4,613 |           48 |   634–638   |         635.8 |      1.04% |
|   D9   |     4,375 |           24 |   639–643   |         640.9 |      0.55% |
|   D10  |     4,417 |           24 |   644–653   |         647.9 |  **0.54%** |

### Key Insight

* **Bad Rate menurun secara konsisten seiring meningkatnya credit score**, menunjukkan bahwa scorecard mampu mengurutkan nasabah berdasarkan tingkat risiko kredit dengan baik.
* Nasabah pada **Desil 1 (D1)** memiliki **Bad Rate sebesar 35,64%**, sehingga merupakan kelompok dengan risiko gagal bayar tertinggi.
* Sebaliknya, nasabah pada **Desil 10 (D10)** hanya memiliki **Bad Rate sebesar 0,54%**, sehingga merupakan kelompok dengan risiko gagal bayar terendah.
* Pola penurunan Bad Rate yang bersifat **monoton** dari D1 hingga D10 menunjukkan bahwa model memiliki **kemampuan diskriminasi (discriminatory power)** yang baik dalam membedakan nasabah berisiko tinggi dan berisiko rendah.
* Hasil ini menunjukkan bahwa **semakin tinggi skor kredit yang diperoleh nasabah, semakin rendah probabilitas gagal bayar**, sehingga credit score yang dihasilkan layak digunakan sebagai dasar dalam proses penilaian risiko kredit.

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
