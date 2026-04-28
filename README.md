# Sistem Rekomendasi Dosen Pembimbing Skripsi Berbasis NLP
### Program Studi Teknik Informatika — Universitas Lampung

> Sistem rekomendasi dosen pembimbing menggunakan **TF-IDF + Cosine Similarity** (baseline) dan **Sentence-BERT / IndoBERT** (metode utama) berdasarkan kemiripan topik skripsi mahasiswa dengan riwayat publikasi dosen.

---

## Daftar Isi
- [Status Proyek](#-status-proyek)
- [Deskripsi Singkat](#-deskripsi-singkat)
- [To-Do List](#-to-do-list)
- [Struktur Folder](#-struktur-folder)
- [Dataset](#-dataset)
- [Metode](#-metode)
- [Evaluasi](#-evaluasi)
- [Tech Stack](#-tech-stack)
- [Referensi](#-referensi)

---

## Status Proyek

| Fase | Deskripsi | Status |
|------|-----------|--------|
| 1 | Pengumpulan data skripsi (Digilib Unila) | ⬜ Belum |
| 2 | Pengumpulan data publikasi dosen (SINTA/Scholar) | ⬜ Belum |
| 3 | Preprocessing teks (cleaning, stopword, stemming) | ⬜ Belum |
| 4 | Implementasi TF-IDF + Cosine Similarity | ⬜ Belum |
| 5 | Implementasi IndoBERT / Sentence-BERT embedding | ⬜ Belum |
| 6 | Evaluasi & perbandingan metode (Top-K Accuracy) | ⬜ Belum |
| 7 | Pembangunan prototype aplikasi web | ⬜ Belum |
| 8 | Penulisan laporan / skripsi | ⬜ Belum |

---

## Deskripsi Singkat

Mahasiswa sering kesulitan menentukan dosen pembimbing yang sesuai topik skripsinya. Sistem ini menerima input berupa **judul skripsi**, lalu mengembalikan **Top-K dosen** yang paling relevan berdasarkan kemiripan semantik dengan riwayat penelitian dosen.

```
[Input: Judul Skripsi]
        ↓
[Preprocessing Teks]
        ↓
 ┌──────────────────────────┐
 │  TF-IDF + Cosine Sim.   │  ← Baseline
 └──────────────────────────┘
 ┌──────────────────────────┐
 │  IndoBERT / SBERT Embed │  ← Metode Utama
 └──────────────────────────┘
        ↓
[Top-1 / Top-3 / Top-5 Rekomendasi Dosen]
```

---

## To-Do List

### Fase 1 — Pengumpulan Data
- [ ] Scraping / download data judul skripsi dari [Digilib Unila](https://digilib.unila.ac.id) (filter: Teknik Informatika)
- [ ] Catat kolom: `judul_skripsi`, `nama_dosen_pembimbing`, `tahun`
- [ ] Kumpulkan daftar dosen aktif Teknik Informatika Unila
- [ ] Scraping publikasi dosen dari [SINTA](https://sinta.kemdikbud.go.id) dan/atau Google Scholar
- [ ] Catat kolom: `nama_dosen`, `judul_publikasi`, `abstrak`, `tahun`
- [ ] Simpan dalam format `.csv` di folder `data/raw/`

### Fase 2 — Preprocessing
- [ ] Case folding (lowercase)
- [ ] Hapus karakter non-alfanumerik & angka
- [ ] Tokenisasi
- [ ] Stopword removal (gunakan library [PySastrawi](https://github.com/har07/PySastrawi) atau custom stopword)
- [ ] Stemming Bahasa Indonesia (Sastrawi Stemmer)
- [ ] Gabungkan judul + abstrak dosen menjadi satu "dokumen profil dosen"
- [ ] Simpan hasil ke `data/processed/`

### Fase 3 — Implementasi Metode Baseline (TF-IDF)
- [ ] Bangun TF-IDF vectorizer dari seluruh profil dosen
- [ ] Hitung Cosine Similarity antara query (judul mahasiswa) dan setiap profil dosen
- [ ] Urutkan dan ambil Top-K dosen dengan skor tertinggi
- [ ] Simpan model TF-IDF di `models/tfidf_model.pkl`

### Fase 4 — Implementasi Metode Utama (IndoBERT / SBERT)
- [ ] Load pre-trained model:
  - IndoBERT: `indobenchmark/indobert-base-p1` (via HuggingFace)
  - atau Sentence-BERT: `firqaaa/indo-sentence-bert-base`
- [ ] Encode seluruh profil dosen menjadi sentence embedding
- [ ] Simpan embedding dosen di `models/embeddings_dosen.npy`
- [ ] Pada saat query: encode judul mahasiswa → hitung Cosine Similarity → Top-K
- [ ] Simpan script di `src/recommend_bert.py`

### Fase 5 — Evaluasi
- [ ] Siapkan data uji: pasangan `(judul_skripsi, dosen_pembimbing_asli)`
- [ ] Hitung **Top-1 Accuracy** (apakah dosen asli ada di posisi 1?)
- [ ] Hitung **Top-3 Accuracy** (apakah dosen asli ada di 3 besar?)
- [ ] Hitung **Top-5 Accuracy** (apakah dosen asli ada di 5 besar?)
- [ ] Buat tabel perbandingan TF-IDF vs IndoBERT vs SBERT
- [ ] Simpan hasil di `results/evaluation.csv`

### Fase 6 — Prototype Aplikasi Web
- [ ] Setup framework (Flask / Streamlit — pilih salah satu)
- [ ] Buat halaman input judul skripsi
- [ ] Tampilkan hasil rekomendasi Top-5 beserta skor kemiripan
- [ ] (Opsional) Tambahkan filter tahun / bidang keahlian
- [ ] Deploy lokal untuk demo

### Fase 7 — Penulisan Skripsi
- [ ] Bab 1 — Pendahuluan (latar belakang, rumusan masalah, tujuan)
- [ ] Bab 2 — Tinjauan Pustaka (NLP, TF-IDF, BERT, Recommender System)
- [ ] Bab 3 — Metodologi (desain sistem, alur penelitian)
- [ ] Bab 4 — Hasil dan Pembahasan
- [ ] Bab 5 — Kesimpulan dan Saran
- [ ] Lampiran (kode, dataset, screenshot sistem)

---

## Struktur Folder

```
skripsi-rekomendasi-dosen/
│
├── data/
│   ├── raw/
│   │   ├── skripsi_unila.csv         # Data mentah skripsi mahasiswa
│   │   └── publikasi_dosen.csv       # Data mentah publikasi dosen
│   └── processed/
│       ├── skripsi_clean.csv         # Setelah preprocessing
│       └── profil_dosen_clean.csv    # Profil dosen setelah preprocessing
│
├── notebooks/
│   ├── 01_explorasi_data.ipynb       # EDA dataset
│   ├── 02_preprocessing.ipynb        # Proses cleaning & stemming
│   ├── 03_tfidf_baseline.ipynb       # Eksperimen TF-IDF
│   ├── 04_bert_embedding.ipynb       # Eksperimen IndoBERT / SBERT
│   └── 05_evaluasi.ipynb             # Perbandingan metode
│
├── src/
│   ├── preprocessing.py              # Fungsi preprocessing teks
│   ├── recommend_tfidf.py            # Modul rekomendasi TF-IDF
│   ├── recommend_bert.py             # Modul rekomendasi BERT
│   └── evaluate.py                   # Fungsi evaluasi Top-K Accuracy
│
├── models/
│   ├── tfidf_model.pkl               # Model TF-IDF tersimpan
│   └── embeddings_dosen.npy          # Embedding vektor profil dosen
│
├── app/
│   ├── app.py                        # Aplikasi web (Flask/Streamlit)
│   └── templates/                    # HTML templates (jika Flask)
│
├── results/
│   ├── evaluation.csv                # Hasil evaluasi per metode
│   └── sample_output.txt             # Contoh output rekomendasi
│
├── docs/
│   └── ide_penelitian_skripsi.docx   # Dokumen proposal ide penelitian
│
├── requirements.txt                  # Daftar library Python
└── README.md                         # File ini
```

---

## Dataset

| Dataset | Sumber | Format | Keterangan |
|---------|--------|--------|------------|
| Skripsi Mahasiswa | [Digilib Unila](https://digilib.unila.ac.id) | `.csv` | Judul + Nama Pembimbing |
| Publikasi Dosen | [SINTA](https://sinta.kemdikbud.go.id) / Google Scholar | `.csv` | Judul + Abstrak |

> **Catatan:** Dataset difokuskan hanya pada Program Studi **Teknik Informatika** Universitas Lampung.

---

## ⚙Metode

### 1. TF-IDF + Cosine Similarity *(Baseline)*
```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

vectorizer = TfidfVectorizer()
tfidf_matrix = vectorizer.fit_transform(profil_dosen)
query_vec    = vectorizer.transform([judul_mahasiswa])
scores       = cosine_similarity(query_vec, tfidf_matrix)
```

### 2. IndoBERT / Sentence-BERT Embedding *(Metode Utama)*
```python
from transformers import AutoTokenizer, AutoModel
import torch

# Model IndoBERT (HuggingFace)
model_name = "indobenchmark/indobert-base-p1"
tokenizer  = AutoTokenizer.from_pretrained(model_name)
model      = AutoModel.from_pretrained(model_name)

# Encode teks → embedding vektor
def get_embedding(text):
    inputs = tokenizer(text, return_tensors="pt", truncation=True, max_length=128)
    with torch.no_grad():
        outputs = model(**inputs)
    return outputs.last_hidden_state[:, 0, :].squeeze().numpy()
```

---

## Evaluasi

| Metrik | Deskripsi |
|--------|-----------|
| **Top-1 Accuracy** | Dosen pembimbing asli berada di posisi rekomendasi ke-1 |
| **Top-3 Accuracy** | Dosen pembimbing asli berada di antara 3 rekomendasi teratas |
| **Top-5 Accuracy** | Dosen pembimbing asli berada di antara 5 rekomendasi teratas |

Ground truth diambil dari pasangan `(judul_skripsi → dosen_pembimbing_asli)` di dataset Digilib Unila.

---

## Tech Stack

```
Python 3.9+
├── pandas, numpy              # Manipulasi data
├── scikit-learn               # TF-IDF, Cosine Similarity
├── PySastrawi                 # Stemming Bahasa Indonesia
├── transformers (HuggingFace) # IndoBERT / Sentence-BERT
├── torch                      # PyTorch (backend BERT)
├── Flask atau Streamlit       # Prototype aplikasi web
└── jupyter                    # Notebook eksperimen
```

Install semua dependensi:
```bash
pip install -r requirements.txt
```

---

## Referensi

1. Devlin et al. (2019). **BERT**. NAACL-HLT 2019. [doi:10.18653/v1/N19-1423](https://doi.org/10.18653/v1/N19-1423)
2. Reimers & Gurevych (2019). **Sentence-BERT**. EMNLP-IJCNLP 2019. [doi:10.18653/v1/D19-1410](https://doi.org/10.18653/v1/D19-1410)
3. Wilie et al. (2020). **IndoNLU**. AACL-IJCNLP 2020. [doi:10.18653/v1/2020.aacl-main.85](https://doi.org/10.18653/v1/2020.aacl-main.85)
4. Koto et al. (2020). **IndoLEM & IndoBERT**. COLING 2020. [ACL Anthology](https://aclanthology.org/2020.coling-main.66)
5. Ramos (2003). **Using TF-IDF to Determine Word Relevance**. ICML Workshop.
6. Adomavicius & Tuzhilin (2005). **Next Generation of Recommender Systems**. IEEE TKDE. [doi:10.1109/TKDE.2005.99](https://doi.org/10.1109/TKDE.2005.99)
7. Salton & Buckley (1988). **Term-Weighting Approaches**. Info Processing & Management.
8. Kenter & de Rijke (2015). **Short Text Similarity with Word Embeddings**. CIKM 2015.
9. Khomsah & Aribowo (2020). **Model Text-Preprocessing Bahasa Indonesia**. Jurnal RESTI. [doi:10.29207/resti.v4i4.2035](https://doi.org/10.29207/resti.v4i4.2035)

---

> **Dibuat untuk keperluan skripsi S1 — Teknik Informatika, Universitas Lampung.**
> Silakan update status checkbox seiring progress pengerjaan.
