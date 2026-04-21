# Clustering Produk Skincare Menggunakan Unsupervised Learning

**Elda Indah Sanda Langi | E1E124032 | Teknik Informatika — Universitas Halu Oleo**

Perbandingan K-Means, Agglomerative Clustering, dan Gaussian Mixture Model untuk
mengelompokkan 19.000+ produk skincare berdasarkan kandungan ingredients dan manfaat produk.

---

## Hasil Utama

| Algoritma | Best K | Silhouette Score | DBI |
|---|---|---|---|
| **K-Means** ✓ | 10 | 0.1695 | 1.8487 |
| Agglomerative | 9 | 0.1395 | 2.1113 |
| GMM | 9 | 0.1193 | 2.2666 |

K-Means (k=10) adalah algoritma terbaik berdasarkan Silhouette Score, DBI,
ARI stability (0.60), dan Final Score agregat (0.5489).

---

## Struktur Repository

```
clustering_produk_skincare/
├── Skincare.ipynb             ← File 1: EDA, preprocessing, clustering, evaluasi, inference
├── Analisis_Lanjutan.ipynb    ← File 2: Profiling, ARI, auto-label, final scoring
├── skincare.csv               ← Dataset (19.050 baris, 6 kolom)
├── .gitignore
└── README.md
```

### Folder yang dibuat otomatis di Google Drive saat runtime

```
Machine_Learning/Proyek/          ← BASE_DIR (sesuaikan dengan path Drive kamu)
├── skincare.csv                  ← Dataset (upload manual ke Drive)
├── outputS/                      ← Dibuat otomatis oleh Skincare.ipynb
│   ├── hasil_clustering_skincare_lengkap.xlsx
│   ├── evaluasi_metrik_clustering.xlsx
│   └── distribusi_ukuran_cluster_all_algos.xlsx
├── outputA/                      ← Dibuat otomatis oleh Analisis_Lanjutan.ipynb
│   ├── hasil_analisis_lanjutan.xlsx
│   ├── final_scoring_algoritma.xlsx
│   ├── profil_cluster_kmeans.xlsx
│   ├── stabilitas_ari.xlsx
│   └── cluster_names_kmeans.json ← Dibutuhkan oleh inference di File 1
├── models/                       ← Dibuat otomatis oleh Skincare.ipynb
│   ├── tfidf_ing.pkl
│   ├── mlb.pkl
│   ├── svd.pkl
│   └── kmeans_final.pkl
└── hasil_prediksi/               ← Dibuat otomatis, menyimpan output batch inference
    └── hasil_prediksi_batch_skincare.xlsx
```

---

## Persiapan Sebelum Menjalankan

### Langkah 1 — Upload dataset ke Google Drive
Upload file `skincare.csv` dari repository ini ke Google Drive.
Letakkan di folder yang akan kamu jadikan BASE_DIR.

### Langkah 2 — Sesuaikan path di Skincare.ipynb
Buka `Skincare.ipynb`, cari dua baris berikut dan ganti sesuai path Drive kamu:

```python
BASE_DIR  = "/content/drive/MyDrive/NAMA_FOLDER_KAMU/Proyek"
DATA_PATH = "/content/drive/MyDrive/NAMA_FOLDER_KAMU/Proyek/skincare.csv"
```

### Langkah 3 — Sesuaikan path di Analisis_Lanjutan.ipynb
Buka `Analisis_Lanjutan.ipynb`, cari dua baris berikut dan sesuaikan:

```python
base_path = "/content/drive/My Drive/NAMA_FOLDER_KAMU/Proyek/"
data_path = "/content/drive/My Drive/NAMA_FOLDER_KAMU/Proyek/outputS/"
```

> Catatan: penulisan "MyDrive" (tanpa spasi) di File 1 dan "My Drive" (dengan spasi)
> di File 2 adalah perbedaan yang disengaja sesuai cara Colab membaca path Drive.
> Jangan disamakan.

---

## Urutan Menjalankan

```
LANGKAH 1 — Jalankan Skincare.ipynb (Run All)
  Tahapan: EDA → Preprocessing → K-Means → Agglomerative → GMM → Evaluasi
  Output  : outputS/ (3 file Excel)
  ↓
  Tahapan: Inference (bagian terakhir di file yang sama)
  Catatan : Inference akan memuat cluster_names_kmeans.json dari outputA/.
            Jika file JSON belum ada (belum jalankan File 2), inference tetap
            berjalan dengan nama cluster default (Cluster 0, Cluster 1, dst.).
            Untuk nama cluster deskriptif, jalankan File 2 terlebih dahulu,
            lalu jalankan ulang sel inference di File 1.

LANGKAH 2 — Jalankan Analisis_Lanjutan.ipynb (Run All)
  Memuat : outputS/hasil_clustering_skincare_lengkap.xlsx (dari Langkah 1)
  Tahapan: Interpretasi → Auto-label → Profiling → Overlap → ARI → Scoring
  Output  : outputA/ (5 file termasuk cluster_names_kmeans.json)
```

> PENTING: File 2 hanya bisa dijalankan setelah File 1 selesai,
> karena File 2 membaca output Excel yang dihasilkan File 1.

---

## Syarat Inference

Inference menggunakan model K-Means yang sudah dilatih. Ada dua mode:
- **Prediksi manual**: satu produk → panggil `predict_manual(ingredients, afteruse_labels)`
- **Prediksi batch**: dari file CSV → panggil `predict_batch_from_file(path, col_ingredients, col_afteruse)`

Agar inference menghasilkan prediksi yang akurat, **kedua kolom berikut harus tersedia**:

| Kolom | Keterangan |
|---|---|
| `ingridients` | Teks bebas berisi daftar bahan produk |
| `afterUse` | Label manfaat, dipisah koma, dari 17 label valid berikut |

17 label valid afterUse:
`Acne Fighting`, `Acne Trigger`, `Anti-Aging`, `Brightening`, `Dark Spots`,
`Drying`, `Eczema`, `Good For Oily Skin`, `Hydrating`, `Irritating`,
`May Worsen Oily Skin`, `Reduces Irritation`, `Reduces Large Pores`,
`Redness Reducing`, `Rosacea`, `Scar Healing`, `Skin Texture`

> Jika kolom afterUse kosong atau tidak berisi label valid, model akan menggunakan
> label default dan hasil prediksi tidak akan akurat.

---

## Dependensi

```bash
pip install pandas numpy scikit-learn scipy nltk wordcloud matplotlib seaborn joblib openpyxl
```

Setelah install, jalankan sekali di Colab untuk mengunduh resource NLTK:
```python
import nltk
nltk.download('stopwords')
nltk.download('wordnet')
nltk.download('averaged_perceptron_tagger_eng')
```

---

## Dataset

Sumber: [Kaggle — 19000 Skincare Products Database](https://www.kaggle.com/datasets/kazireyazulhasan/19000-skincare-products-database-of-skinsort)  
Jumlah data: 19.050 baris | Fitur utama: `ingridients` dan `afterUse`
