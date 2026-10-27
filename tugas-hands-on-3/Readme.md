# Laporan Tugas Hands-On 3: Pandas Lanjutan & Preprocessing Data

**Mata Kuliah:** Artificial Intelligence / Data Science  
**Studi Kasus:** Case 09 - Student Performance (`case9_student_performance.csv`)  
**Sektor:** EdTech (Pendidikan)  
**Jenis Tugas:** Klasifikasi Biner (`status_kelulusan`)  

---

## 📌 Ringkasan Studi Kasus

Dokumen ini berisi ringkasan pengerjaan pembersihan data (*data cleaning*), analisis eksplorasi (*EDA*), dan penyiapan *pipeline preprocessing* tanpa *data leakage* pada dataset **Student Performance**. Dataset ini terdiri dari 800 baris data siswa yang mencakup informasi demografi, latar belakang orang tua, kebiasaan belajar, persentase kehadiran, nilai akademik mata pelajaran, serta status kelulusan.

---

## 📊 Kualitas Data (Sebelum vs Sesudah)

Proses pembersihan data dilakukan dengan urutan baku: **Duplikat → Missing Value → Outlier → Tipe Data**.

| Metrik Kualitas | Sebelum Pembersihan | Sesudah Pembersihan | Penjelasan Tindakan |
| :--- | :---: | :---: | :--- |
| **`n_baris`** | 800 | 800 | Jumlah baris tetap dijaga untuk mempertahankan informasi. |
| **`n_duplikat`** | *0* | 0 | Duplikat diidentifikasi dan dihapus berdasarkan `student_id`. |
| **`n_missing`** | 511 | 0 | Imputasi kategorik dengan modus, serta nilai akademik/numerik dengan median per grup `jenis_sekolah` + fallback median global. |
| **`n_outlier`** | >0 | 0 | *Outlier* ekstrem pada jam belajar, kehadiran, dan waktu tempuh di-capping/clipping menggunakan metode 1.5x IQR per grup. |

---

## 💡 3 Temuan Strategis yang Dapat Ditindaklanjuti

Berdasarkan hasil analisis agregasi `groupby` dan *pivot table* pada dataset yang telah dibersihkan:

1. **Pengaruh Akses Internet & Education Support:**
   * **Temuan:** Siswa yang memiliki akses internet di rumah dan orang tua berpendidikan tinggi memiliki rata-rata nilai akademik (Matematika, IPA, Bahasa Inggris) yang jauh lebih tinggi.
   * **Rekomendasi:** Pihak sekolah/pemerintah perlu memberikan fasilitas subsidi internet/bantuan perangkat bagi siswa dari keluarga kurang mampu untuk menekan kesenjangan belajar.
2. **Efektivitas Les Privat terhadap Jam Belajar:**
   * **Temuan:** Siswa yang mengikuti les privat memiliki alokasi jam belajar mandiri per minggu yang lebih konsisten serta memiliki tingkat kelulusan (`status_kelulusan = Lulus`) yang signifikan lebih tinggi dibanding yang hanya mengandalkan jam sekolah.
   * **Rekomendasi:** Sekolah disarankan menyediakan program bimbingan belajar (Bimbel) tambahan gratis di luar jam sekolah bagi siswa yang tidak mampu membayar les privat.
3. **Dampak Kehadiran & Waktu Tempuh Perjalanan:**
   * **Temuan:** Tingkat kehadiran di bawah 80% dan waktu tempuh perjalanan >45 menit memiliki korelasi negatif kuat terhadap nilai rata-rata siswa dan meningkatkan risiko tidak lulus.
   * **Rekomendasi:** Manajemen sekolah dapat menyediakan transportasi/jemputan sekolah serta menerapkan sistem peringatan dini (*early warning system*) jika kehadiran siswa mulai berada di bawah kriteria minimum.

---

## 🛠️ Alur Preprocessing Model (Bebas Data Leakage)

Untuk menyiapkan data menuju tahap pemodelan *Machine Learning*:
1. Data dipisah terlebih dahulu menjadi **Train Set (80%)** dan **Test Set (20%)** menggunakan `train_test_split` dengan parameter `stratify=y` untuk menjaga proporsi `status_kelulusan`.
2. Konstruksi **`ColumnTransformer`**:
   * **Fitur Numerik:** Diimputasi dengan `SimpleImputer(strategy='median')` dan diskalakan dengan `StandardScaler()`.
   * **Fitur Kategorik:** Diimputasi dengan `SimpleImputer(strategy='most_frequent')` dan diubah dengan `OneHotEncoder(handle_unknown='ignore')`.
3. Seluruh imputer dan encoder **hanya di-fit pada Data Train** (`fit_transform`) dan kemudian di-`transform` pada Data Test untuk mencegah terjadinya kebocoran data (*data leakage*).

---

## 📂 Struktur Direktori Tugas

Berikut adalah susunan berkas dan folder hasil pengumpulan sesuai dengan standar yang ditentukan:

```text
tugas-hands-on-3/
├── README.md                  # Ringkasan kasus, temuan, dan petunjuk eksekusi (file ini)
├── notebook.ipynb             # Jupyter Notebook lengkap yang berjalan tanpa error (Restart & Run All)
├── laporan_kualitas.csv       # Tabel perbandingan kualitas data sebelum vs sesudah
├── data/
│   └── case9_student_performance.csv  # Dataset asli (mentah)
└── output/
    ├── data_bersih.csv        # Dataset hasil pembersihan (clean data)
    └── kamus_data.csv         # Kamus data memuat nama kolom, tipe data, missing, & n_unik
