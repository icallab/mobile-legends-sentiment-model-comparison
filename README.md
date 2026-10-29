Repositori ini berisi implementasi penelitian **"Analisis Komparatif
Model LSTM, Word2Vec, FastText, dan Attention dalam Analisis Sentimen
Ulasan Pengguna Game Mobile Legends"**. Penelitian berfokus pada
klasifikasi sentimen ulasan pengguna Mobile Legends berbahasa Indonesia
ke dalam dua kelas, yaitu **positif** dan **negatif**.

Ulasan game sering memuat bahasa informal, slang, singkatan, dan kata
yang tidak selalu muncul dalam kosakata pelatihan. Penelitian ini
membandingkan beberapa representasi kata dan konfigurasi model deep
learning untuk mengetahui kombinasi yang memberikan keseimbangan terbaik
antara performa klasifikasi dan biaya komputasi.

> **Temuan utama:** skenario 19, yaitu **FastText + LSTM
> unidirectional + Dropout tanpa Attention**, memperoleh akurasi
> **95,37%** pada eksperimen yang dilaporkan dalam tesis.

## Tujuan Penelitian

-   Membandingkan representasi kata tanpa embedding, Word2Vec, dan
    FastText.
-   Mengevaluasi kontribusi Attention, Dropout, dan pemrosesan dua arah
    (BiLSTM).
-   Membandingkan 24 kombinasi eksperimen menggunakan metrik
    klasifikasi.
-   Mempertimbangkan waktu training dan testing selain kualitas
    prediksi.
-   Mengidentifikasi konfigurasi yang sesuai untuk ulasan pendek
    berbahasa Indonesia.

## Hasil Utama

Hasil berikut diambil dari naskah tesis. Waktu komputasi dapat berubah
tergantung perangkat keras, lingkungan software, dan konfigurasi
eksekusi.

  Metrik                             Hasil skenario terbaik
  -------------------------------- ------------------------
  Nomor skenario                                         19
  Embedding                                        FastText
  Arsitektur                            LSTM unidirectional
  Dropout tambahan                                       Ya
  Attention                                           Tidak
  Akurasi                                        **95,37%**
  Precision                                      **95,40%**
  Recall                                         **95,37%**
  F1-score                                       **95,36%**
  Waktu training yang dilaporkan               706,03 detik
  Waktu testing yang dilaporkan                  2,24 detik

Dataset penelitian terdiri dari **10.250 ulasan** yang telah diproses
dan diberi label secara manual ke dalam dua kelas. Pembagian data
menggunakan 80% data training dan 20% data testing dengan `stratify` dan
`random_state=42` pada notebook.

### Interpretasi

-   **FastText** menjadi representasi kata yang paling efektif pada
    eksperimen ini. Representasi berbasis subword membantu menangani
    variasi kata dan kosakata yang tidak dikenal.
-   **Dropout** membantu regularisasi dan mendukung generalisasi model.
-   **Attention dan BiLSTM** tidak menghasilkan peningkatan yang
    sebanding dengan tambahan kompleksitas komputasi pada dataset ulasan
    yang relatif pendek.
-   Model yang lebih kompleks tidak otomatis memberikan hasil lebih
    baik. Pemilihan embedding dan kesesuaian model terhadap
    karakteristik data juga penting.

Angka hasil di atas adalah hasil yang dilaporkan dalam tesis, bukan
jaminan bahwa notebook akan menghasilkan angka identik pada setiap
eksekusi.

## Metodologi

Alur penelitian secara ringkas:

1.  **Memuat dataset** ulasan dan label sentimen.
2.  **Preprocessing teks**: mengubah teks menjadi huruf kecil, menghapus
    emoji, mention, hashtag, URL, tanda baca, angka, dan spasi berlebih,
    kemudian melakukan stemming bahasa Indonesia dengan Sastrawi.
3.  **Tokenisasi** menggunakan Keras Tokenizer dengan batas kosakata
    10.000 kata dan token OOV.
4.  **Padding sekuens** hingga panjang maksimum 100 token.
5.  **Encoding label** menjadi nilai numerik.
6.  **Pembagian data** training/testing sebesar 80:20 secara stratified.
7.  **Penyusunan embedding**: tanpa embedding yang dilatih bersama
    model, Word2Vec, atau FastText.
8.  **Pelatihan dan evaluasi** 24 skenario kombinasi.
9.  **Evaluasi model** menggunakan accuracy, precision, recall,
    F1-score, confusion matrix, learning curve, serta waktu
    training/testing.
10. **Analisis komparatif** untuk memilih konfigurasi yang paling
    seimbang.

### Preprocessing

Fungsi preprocessing pada notebook melakukan operasi berikut:

-   Lowercasing
-   Penghapusan emoji
-   Penghapusan mention (`@...`)
-   Penghapusan hashtag (`#...`)
-   Penghapusan URL
-   Penghapusan tanda baca dan angka
-   Normalisasi spasi
-   Stemming menggunakan **Sastrawi**

Preprocessing mengubah data teks mentah menjadi teks yang lebih seragam
sebelum tokenisasi. Hasil akhirnya tetap bergantung pada karakteristik
dataset dan aturan preprocessing yang digunakan.

### Variabel Eksperimen

Tiga jenis embedding diuji:

-   `none` --- embedding yang dipelajari sebagai bagian dari model.
-   `word2vec` --- embedding Word2Vec.
-   `fasttext` --- embedding FastText.

Setiap jenis embedding dikombinasikan dengan tiga pilihan biner:

-   Attention: aktif / tidak aktif
-   Dropout tambahan: aktif / tidak aktif
-   Bidirectional: aktif / tidak aktif

Dengan demikian, jumlah kombinasi adalah `3 × 2 × 2 × 2 = 24 skenario`.

### Konfigurasi Notebook

  Parameter               Nilai dalam notebook
  ----------------------- ---------------------------------
  `MAX_NUM_WORDS`         10.000
  `MAX_SEQUENCE_LENGTH`   100
  `EMBEDDING_DIM`         300 untuk embedding pretrained
  `EPOCHS`                20
  `BATCH_SIZE`            64
  `LSTM_UNITS`            64
  `DROPOUT_RATE`          0,4
  `LEARNING_RATE`         0,001
  `PATIENCE`              3
  Test size               20%
  `random_state`          42
  Loss                    Binary cross-entropy
  Optimizer               Adam
  Output                  Sigmoid untuk klasifikasi biner

Catatan: notebook mendefinisikan callback `EarlyStopping`, tetapi pada
sel pelatihan yang tersedia callback tersebut belum diteruskan ke
`model.fit()`. Karena itu, jangan menganggap early stopping benar-benar
aktif tanpa memperbarui kode.

## Teknologi yang Digunakan

-   **Python** --- bahasa pemrograman
-   **Jupyter Notebook** --- eksperimen dan dokumentasi analisis
-   **Pandas, NumPy** --- manipulasi data
-   **Matplotlib, Seaborn** --- visualisasi hasil
-   **TensorFlow / Keras** --- pembangunan dan pelatihan model deep
    learning
-   **NLTK / Keras Tokenizer** --- pemrosesan teks dan tokenisasi
-   **Sastrawi** --- stemming bahasa Indonesia
-   **Gensim** --- pemuatan embedding Word2Vec
-   **FastText** --- embedding pretrained bahasa Indonesia
-   **Scikit-learn** --- pembagian data dan evaluasi klasifikasi
-   **Joblib / JSON** --- dukungan penyimpanan artefak dan konfigurasi

## Struktur Proyek

Struktur yang disarankan untuk repositori GitHub:

``` text
mobile-legends-sentiment-analysis/
├── README.md
├── notebooks/
│   └── Untitled22.ipynb
├── data/
│   ├── README.md
│   ├── dataset_undersampled.csv       # tidak disertakan jika berlisensi/berukuran besar
│   └── dataset_cleaned.csv            # dihasilkan saat notebook dijalankan
├── outputs/
│   ├── all_results.csv                 # hasil eksperimen 24 skenario
│   ├── all_metrics.csv
│   ├── all_metrics_detailed.csv
│   ├── best_model_classification_report_scenario_*.json
│   ├── saved_models/
│   ├── training_histories/
│   └── plots/
├── docs/
│   ├── thesis.pdf                      # opsional; pertimbangkan privasi dan hak distribusi
│   └── figures/
│       ├── accuracy_by_scenario.png
│       ├── confusion_matrix.png
│       └── learning_curve.png
└── requirements.txt
```

Struktur di atas adalah **struktur rekomendasi untuk GitHub**, bukan
klaim bahwa seluruh folder dan file output tersebut sudah tersedia di
notebook yang diunggah. Notebook saat ini menggunakan path relatif
seperti `dataset_undersampled.csv`, `saved_models/`,
`training_histories/`, dan `plots/`. Sesuaikan path jika notebook
dipindahkan ke dalam folder `notebooks/`.

## Instalasi dan Penggunaan

### 1. Clone repositori

``` bash
git clone https://github.com/USERNAME/REPOSITORY.git
cd REPOSITORY
```

Ganti `USERNAME` dan `REPOSITORY` dengan alamat repositori GitHub milik
Anda.

### 2. Buat virtual environment (disarankan)

Windows:

``` bash
python -m venv .venv
.venv\Scripts\activate
```

macOS / Linux:

``` bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Instal dependensi

Jika tersedia file `requirements.txt`:

``` bash
pip install -r requirements.txt
```

Notebook menggunakan paket yang antara lain mencakup:

``` bash
pip install pandas numpy matplotlib seaborn scikit-learn tensorflow gensim Sastrawi emoji jupyter
```

FastText dan model pretrained dapat membutuhkan instalasi/konfigurasi
tambahan. Periksa dokumentasi paket dan kompatibilitas versi dengan
sistem Anda sebelum menjalankan seluruh eksperimen.

### 4. Siapkan dataset

Letakkan file `dataset_undersampled.csv` pada direktori kerja notebook.
Notebook mengharapkan kolom berikut:

-   `content` --- teks ulasan
-   `label` --- label sentimen

File dataset asli tidak disertakan dalam lampiran yang tersedia saat
README ini disusun. Pastikan Anda memiliki dataset yang sesuai sebelum
menjalankan notebook. Jika data tidak dapat dibagikan, sediakan petunjuk
akses atau contoh data sintetis tanpa informasi pribadi.

### 5. Jalankan notebook

``` bash
jupyter notebook
```

Buka `Untitled22.ipynb`, lalu jalankan sel secara berurutan. Proses
mengunduh embedding pretrained dan melatih 24 skenario dapat membutuhkan
waktu, memori, serta koneksi internet yang memadai.

### 6. Artefak yang dihasilkan

Berdasarkan kode notebook, eksekusi dapat menghasilkan:

-   `dataset_cleaned.csv`
-   `all_results.csv`
-   `all_metrics.csv`
-   `all_metrics_detailed.csv`
-   Model untuk tiap skenario di folder `saved_models/`
-   Riwayat training dan confusion matrix di folder
    `training_histories/`
-   Grafik learning curve di folder `plots/`
-   Laporan klasifikasi model terbaik dalam format JSON
