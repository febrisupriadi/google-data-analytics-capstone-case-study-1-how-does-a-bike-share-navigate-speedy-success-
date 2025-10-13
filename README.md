[Read in ENGLISH](README_EN.md)


Studi Kasus Bike-Share Cyclistic

Proyek Capstone Google Data Analytics

# 🚲 Cyclistic Bike-Share Case Study  
**Case Study 1: How Does a Bike-Share Navigate Speedy Success?**

---

## 🇮🇩 Versi Bahasa Indonesia (Utama)

### 1. 🏁 Pendahuluan
Proyek ini merupakan bagian dari *Google Data Analytics Capstone Case Study*. Studi kasus ini berfokus pada **Cyclistic**, perusahaan penyewaan sepeda fiksi di Chicago.  
Tujuan analisis adalah memahami **perbedaan perilaku antara pengguna kasual dan anggota tahunan**, serta memberikan rekomendasi strategi pemasaran untuk meningkatkan jumlah anggota tahunan.

---

### 2. 🎯 Tujuan & Business Task
**Pertanyaan bisnis utama:**
> Bagaimana *annual members* dan *casual riders* menggunakan sepeda Cyclistic secara berbeda?

**Tujuan akhir:**
Menghasilkan analisis berbasis data dan rekomendasi yang dapat membantu tim marketing mengubah pengguna kasual menjadi anggota tahunan.

**Deliverables:**
1. Pernyataan tugas bisnis  
2. Deskripsi sumber data  
3. Dokumentasi pembersihan data  
4. Ringkasan hasil analisis  
5. Visualisasi pendukung  
6. Tiga rekomendasi utama berbasis temuan

---

### 3. 👥 Stakeholders
| Nama / Tim | Peran | Kepentingan |
|-------------|--------|--------------|
| **Lily Moreno** (Director of Marketing) | Manajer proyek | Menentukan arah strategi pemasaran dan menyetujui rekomendasi |
| **Cyclistic Marketing Analytics Team** | Tim analis data (termasuk penulis proyek ini) | Melakukan pengumpulan, pembersihan, dan analisis data |
| **Cyclistic Executive Team** | Pengambil keputusan | Mengevaluasi dan menyetujui strategi berdasarkan hasil analisis |

---

### 4. 💾 Sumber Data & Evaluasi ROCCC
**Sumber:** [Divvy Trip Data](https://divvy-tripdata.s3.amazonaws.com)  
**Lisensi:** [Divvy Data License Agreement](https://www.divvybikes.com/data-license-agreement)  
**File yang digunakan:**
- `Divvy_Trips_2019_Q1.csv`  
- `Divvy_Trips_2020_Q1.csv`

**Evaluasi ROCCC:**

| Kriteria | Evaluasi | Catatan |
|-----------|-----------|----------|
| Reliable | ✅ Ya | Data berasal dari sumber resmi Divvy |
| Original | ✅ Ya | Data primer, tidak dimodifikasi pihak ketiga |
| Comprehensive | ✅ Ya | Memuat variabel waktu, lokasi, dan tipe pengguna |
| Current | ✅ Ya | Mewakili periode analisis yang relevan |
| Cited | ✅ Ya | Dikutip sesuai lisensi publik Divvy |

---

### 5. 🧹 Pembersihan Data (Process Phase)

**Menggunakan Microsoft Excel**
Data digabungkan dari dua file (Q1 2019 & Q1 2020) dan dibersihkan menggunakan Microsoft Excel.  
Langkah-langkah pembersihan tercatat sebagai berikut:

| No | Langkah | Tujuan | Alat / Formula | Hasil / Catatan |
|----|----------|--------|----------------|-----------------|
| 1 | Impor kedua file CSV | Menggabungkan data lintas periode | Excel “Get Data” → “Append Queries” | Berhasil digabung |
| 2 | Samakan nama & tipe kolom | Menjaga konsistensi data | Manual check / Power Query | Kolom seragam |
| 3 | Hapus duplikasi `ride_id` | Hindari hitungan ganda | Remove Duplicates | 87 baris dihapus |
| 4 | Hapus nilai kosong `started_at`, `ended_at` | Data tak lengkap tidak valid | Filter / Remove Empty | 0.2% baris dihapus |
| 5 | Buat kolom `ride_length = ended_at - started_at` | Menghitung durasi perjalanan | Excel formula | Kolom baru berhasil |
| 6 | Buat kolom `day_of_week = WEEKDAY(started_at,1)` | Pola penggunaan mingguan | Excel formula | Kolom berhasil dibuat |
| 7 | Filter `ride_length <= 0` dan `> 24 jam` | Menghapus data error/outlier | Conditional filter | 25 baris dihapus |
| 8 | Simpan dataset bersih | Menyimpan hasil final | Save As `.csv` | Siap dianalisis |

**Menggunakan RStudio (Posit)**
#### 🔄 Penggabungan Data di R (Append1)

File `Divvy_Trips_2019_Q1_Clean.csv` dan `Divvy_Trips_2020_Q1_Clean.csv` digabungkan menggunakan R (`bind_rows()`) untuk menghasilkan dataset gabungan `Append1` dengan total **791.956 baris**.

####**Langkah-langkah di RStudio/Posit**###
```{r}
# 1️⃣ Instalasi Package
install.packages("dplyr")
install.packages("lubridate")
install.packages("readr")

# 2️⃣ Load library (wajib setiap sesi baru)
library(dplyr)
library(lubridate)
library(readr)

# 3️⃣ Upload Data dari hasil Power Query Excel ke Project Folder di Posit  (folder project : case-study-1 )
# 4️⃣ Import Data 
divvy_2019-Q1 <- read_csv("case-study-1/Divvy_Trips_2019_Q1_Clean.csv")
divvy_2020_Q1 <- read_csv("case-study-1/Divvy_Trips_2020_Q1_Clean.csv")
```

Diketemukan fakta bahwa R membaca seluruh isi baris sebagai satu string panjang, karena tidak mengenali ; sebagai pemisah kolom
Solusinya menggunakan read_delim() dari readr dengan delim = ";"

```{r}
divvy_2019_Q1 <- read_delim("case-study-1/Divvy_Trips_2019_Q1_Clean.csv", delim = ";")

#hasil Rows: 365069 Columns: 8 
#── Column specification ──────────────────────────────────���──────────────────────────────────────
#Delimiter: ";"
#chr  (3): start_station_name, end_station_name, member_casual
#dbl  (3): ride_id, start_station_id, end_station_id
#dttm (2): started_at, ended_at

divvy_2020_Q1 <- read_delim("case-study-1/Divvy_Trips_2020_Q1_Clean.csv", delim = ";")

#hasil Rows: 426887 Columns: 8                                                                          
#── Column specification ──────────────────────────────────���──────────────────────────────────────
#Delimiter: ";"
#chr  (4): ride_id, start_station_name, end_station_name, member_casual
#dbl  (2): start_station_id, end_station_id
#dttm (2): started_at, ended_at
```

Sebelum digabungkan:
- Tipe data `ride_id` diseragamkan ke `character` karena perbedaan format antara 2019 dan 2020.
- Kolom `ride_length` dihitung sebagai selisih waktu (`ended_at - started_at`) dalam menit.
- Ditemukan **25 baris dengan durasi negatif**, disimpan di `issues/Append1_negative_ride_length.csv`.
- Dataset bersih dan siap analisis disimpan sebagai `Append1_final.csv` dengan tambahan kolom `day_of_week`.



---

### 6. 📊 Analisis Data (Analyze Phase)

### 🔍 Awal Tahap Analisis (Analyze Phase - Step 1)

Setelah data dibersihkan, kedua file (`Divvy_Trips_2019_Q1_Clean.csv` dan `Divvy_Trips_2020_Q1_Clean.csv`) digabungkan dan diberi kolom tambahan untuk mendukung analisis perilaku pengguna.

| No | Langkah | Tujuan | Formula / Tools | Hasil |
|----|----------|--------|----------------|--------|
| 1 | Menggabungkan kedua dataset (2019_Q1 & 2020_Q1) | Membuat satu set data lengkap | Excel Power Query [Append] | Dataset gabungan `Cyclistic_Combined_2019-Q1&2020-Q1.xlsx` |
| 2 | Tambahkan kolom `ride_length` | Menghitung durasi setiap perjalanan dalam menit | `=(ended_at - started_at)` | Kolom baru berhasil dibuat dengan format HH:MM:SS menggunakan Format>Cells>Time>37:30:55|
| 3 | Tambahkan kolom `day_of_week` | Mengidentifikasi hari penggunaan | `=WEEKDAY(started_at,1)` | Kolom baru berhasil dibuat |
| 4 | Simpan hasil akhir | Menyimpan data siap analisis | “Save As” → `/data/cleaned/Cyclistic_Combined_2019-Q1&2020-Q1_Ready.csv` | File siap untuk analisis deskriptif |

**Status:**  
✅ Struktur kolom konsisten  
✅ Duplikasi dihapus  
✅ Data gabungan siap dianalisis pada tahap berikutnya (pivot dan visualisasi)


----- // lihat dan koreksi lagi
Analisis dilakukan dengan tabel pivot dan statistik deskriptif di Excel.

| No | Langkah | Tujuan | Hasil |
|----|----------|--------|-------|
| 1 | Hitung rata-rata `ride_length` per tipe pengguna | Bandingkan pola penggunaan | Casual: ±25 menit; Member: ±13 menit |
| 2 | Hitung jumlah perjalanan per hari | Lihat tren waktu | Casual naik di akhir pekan; Member di hari kerja |
| 3 | Buat grafik batang & garis | Visualisasi perbedaan pola | Pola berbeda jelas terlihat |
| 4 | Hitung total trip & durasi maksimum | Menilai intensitas penggunaan | Member melakukan lebih banyak trip total |
| 5 | Buat ringkasan insight | Dasar untuk rekomendasi | Data siap untuk tahap *Share* dan *Act* |

**Temuan utama:**
- Casual riders → rekreasi, akhir pekan, durasi panjang  
- Annual members → komuter, hari kerja, durasi pendek  

---

### 7. 📈 Visualisasi & Insight (Share Phase)

Visualisasi dibuat di Excel dan Tableau untuk memperjelas temuan utama:

**Contoh visualisasi yang dibuat:**
- Grafik perbandingan rata-rata durasi perjalanan (Members vs Casuals)  
- Grafik jumlah perjalanan per hari dalam seminggu  
- Heatmap intensitas penggunaan berdasarkan waktu  

**Insight naratif:**
> Casual riders lebih suka menggunakan sepeda untuk rekreasi akhir pekan, sementara annual members menggunakan untuk perjalanan rutin ke kantor. Hal ini menunjukkan peluang untuk promosi yang disesuaikan dengan perilaku pengguna.

---

### 8. 🧠 Rekomendasi Strategis (Act Phase)
Berdasarkan hasil analisis, tiga rekomendasi utama adalah:

1. **Kampanye konversi berbasis waktu:**  
   Tawarkan promosi atau diskon ke pengguna kasual yang sering bersepeda di hari kerja.

2. **Program loyalitas mingguan:**  
   Berikan insentif bagi pengguna kasual yang melakukan perjalanan berulang agar mencoba keanggotaan tahunan.

3. **Kampanye digital berbasis perilaku:**  
   Gunakan media sosial dan email marketing yang menyoroti manfaat keanggotaan bagi pengguna rekreasi.

---

### 9. 🗂️ Struktur Folder Proyek

Struktur direktori proyek ini disusun untuk menjaga keteraturan antara data mentah, hasil pembersihan, notebook analisis, serta visualisasi hasil.

```bash
📦 google-data-analytics-capstone-case-study-1-how-does-a-bike-share-navigate-speedy-success-
 ┣ 📂 data
 ┃ ┣ 📂 raw
 ┃ ┃ ┣ Divvy_Trips_2019_Q1.csv
 ┃ ┃ ┗ Divvy_Trips_2020_Q1.csv
 ┃ ┗ 📂 processed
 ┃   ┗ cyclistic_combined_cleaned.csv
 ┣ 📂 visuals
 ┃ ┣ avg_ride_length_chart.png
 ┃ ┗ weekly_usage_pattern.png
 ┣ 📂 notebooks
 ┃ ┗ analysis_steps.xlsx
 ┗ 📜 README.md
 ┗ 📜 README_EN.md

```

---
***Penjelasan Struktur Folder***

| Folder           | Isi                                  | Keterangan                                         |
| ---------------- | ------------------------------------ | -------------------------------------------------- |
| `/data/raw/`     | File CSV mentah dari Divvy Trip Data | Data asli tanpa perubahan                          |
| `/data/processed/` | File hasil pembersihan dan penggabungan data | Siap dianalisis                                    |
| `/notebooks/`    | File Excel atau skrip analisis       | Proses analisis deskriptif dan pivot               |
| `/visuals/`      | Gambar hasil visualisasi data        | Grafik dan diagram                                 |
| `README.md`      | Dokumentasi utama proyek             | Berisi deskripsi lengkap proyek dan hasil analisis |

---

✅ *This README documents all project phases: Ask → Prepare → Process → Analyze → Share → Act.*

_Last updated: October 13th, 2025_


