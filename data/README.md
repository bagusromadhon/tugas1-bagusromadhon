# Data Tugas 1

## Dataset yang Dipilih

| Item | Isi |
|---|---|
| Nama dataset | NOAA Global Hourly – Indonesia 2023–2024 |
| Penyedia | National Centers for Environmental Information (NCEI), NOAA |
| Sumber utama | https://www.ncei.noaa.gov/data/global-hourly/ |
| Direktori data | https://www.ncei.noaa.gov/data/global-hourly/access/ |
| Ketentuan penggunaan | Data dapat diakses secara publik melalui NOAA/NCEI. Sumber data wajib dicantumkan dalam dokumentasi dan hasil analisis. |
| Ukuran data mentah | 523,96 MiB (549.411.069 byte) |
| Jumlah baris | 1.273.803 baris |
| Jumlah file | 228 file CSV |
| Periode data | 2023–2024 |
| Cakupan wilayah | Stasiun meteorologi di Indonesia |
| Unit analisis | Satu observasi cuaca pada satu stasiun dan waktu pengamatan |

## Deskripsi Dataset

Pada Milestone 1, saya menggunakan dataset NOAA Global Hourly atau Integrated Surface Database (ISD) untuk melihat pengamatan cuaca dari stasiun meteorologi di Indonesia. Data yang saya ambil mencakup tahun 2023 dan 2024.

Variabel yang tersedia antara lain:

- identitas dan nama stasiun;
- tanggal dan waktu pengamatan;
- koordinat stasiun;
- elevasi;
- arah dan kecepatan angin;
- jarak pandang;
- suhu udara;
- titik embun;
- tekanan udara;
- kode kualitas pengamatan;
- atribut cuaca tambahan yang ketersediaannya bergantung pada stasiun.

## Cakupan Data

| Tahun | Jumlah baris |
|---:|---:|
| 2023 | 605.051 |
| 2024 | 668.752 |
| **Total** | **1.273.803** |

Jumlah file per tahun adalah 114 file. Setiap file merepresentasikan
data satu stasiun pada satu tahun pengamatan.

## Struktur Penyimpanan Lokal

```text
data/
├── README.md
├── isd-history.csv
└── raw/
    └── noaa_global_hourly/
        ├── 2023/
        │   └── *.csv
        └── 2024/
            └── *.csv
```

## Proses Pengambilan dan Profiling Milestone 1

Saya mengambil metadata stasiun dari https://www.ncei.noaa.gov/pub/data/noaa/isd-history.csv, lalu memilih stasiun berkode negara `ID` yang aktif pada tahun pengamatan. Dari daftar tersebut, saya mengunduh CSV tahun 2024 dan 2023 melalui alamat `https://www.ncei.noaa.gov/data/global-hourly/access/{tahun}/{USAF}{WBAN}.csv`.

Proses unduhan menghasilkan 114 file untuk setiap tahun, sehingga totalnya 228 file. Setelah itu, saya memakai Polars untuk mengecek ukuran data, jumlah baris, skema kolom, rentang waktu, nilai kosong, duplikasi, dan jenis laporan. Hasilnya menunjukkan ada 1.273.803 baris dengan ukuran sekitar 523,96 MiB, sehingga memenuhi syarat ukuran dataset tugas.

Saya juga menemukan 114 ID stasiun tetapi hanya 113 nama stasiun unik. Temuan ini menjadi salah satu hal yang saya periksa lebih lanjut pada tahap cleaning. Data mentah tetap disimpan agar hasil analisis bisa dibandingkan dengan sumber awal.

Untuk mengulangi proses ini, jalankan environment sesuai README utama, lalu jalankan seluruh cell `notebooks/01_data_profiling.ipynb` dari atas ke bawah. Notebook mengunduh file yang belum tersedia dan menampilkan kegagalan unduhan jika ada.

Profiling hanya membaca CSV dalam subfolder `2023` dan `2024`. File di akar `data/raw/noaa_global_hourly/` tidak disertakan. Data mentah tidak diunggah ke GitHub; unduhan memerlukan koneksi internet. Isi sumber NOAA dapat berubah sehingga hasil unduhan baru perlu diverifikasi kembali.

## Batasan Profiling

Pada profiling awal, saya tidak menemukan null bawaan pada 14 kolom inti. Namun, hasil ini belum berarti semua pengukuran lengkap karena NOAA memakai angka khusus seperti `+9999` dan `99999` untuk menandai data yang tidak tersedia. Karena itu, pemisahan nilai dan kode kualitas dilanjutkan pada Milestone 2. Pemeriksaan duplikasi pada tahap ini juga hanya membandingkan 14 kolom inti.

## Data Bersih Milestone 2

Pada Milestone 2, saya melanjutkan hasil profiling dengan membersihkan data melalui `notebooks/02_data_cleaning.ipynb`. Prosesnya memakai Polars, mulai dari memisahkan nilai cuaca dan kode kualitas, mengubah tipe data, sampai mengecek nilai kosong, duplikasi, dan angka yang berada di luar rentang yang dipakai.

Nilai sentinel dan pengukuran yang tidak lolos pengecekan dibuat menjadi `null`. Saya tetap menyimpan laporan berbeda dari stasiun dan waktu yang sama karena jenis laporannya bisa berbeda. Pemeriksaan duplikasi persis dilakukan pada 14 kolom inti.

Hasil cleaning masih berisi **1.273.803 laporan dari 114 stasiun**. Tidak ada baris yang terbuang pada data ini; perubahan terutama terjadi pada nilai pengukuran yang bermasalah. Sebagai contoh, 211 pengukuran suhu masih masuk rentang angka, tetapi tidak lolos aturan kualitas yang dipilih. Data kosong paling banyak terdapat pada ketinggian awan sebesar 63,07% dan tekanan udara sebesar 49,56%.

Saya menyimpan hasilnya ke `data/processed/noaa_indonesia_2023_2024.parquet`, lalu memeriksa kembali isinya menggunakan DuckDB `SUMMARIZE`. Jumlah laporan, jumlah stasiun, rentang waktu, dan nilai kosong juga dicek. File Parquet tetap disimpan secara lokal dan tidak ikut dimasukkan ke Git.

Aturan pembacaan nilai dan kode kualitas mengacu pada [dokumentasi format ISD NOAA](https://www.ncei.noaa.gov/pub/data/noaa/isd-format-document.pdf). Batas rentang nilai pada notebook dipakai sebagai pemeriksaan kewajaran awal. Data mentah tetap disimpan agar hasil cleaning bisa ditelusuri dan diperiksa kembali.

## Analisis Milestone 3

Pada Milestone 3, saya memakai Parquet hasil cleaning untuk melihat cakupan laporan, pola suhu bulanan, pola suhu menurut jam, dan perbedaan antarstasiun. Analisisnya ada di `notebooks/03_eda_and_insights.ipynb`.

Saya menggabungkan laporan pada stasiun dan waktu yang sama sebelum menghitung rata-rata per jam, per hari, lalu per bulan. Untuk membandingkan bulan dan tahun, saya memakai stasiun dengan minimal 20 hari bersuhu valid pada setiap bulan selama 2023–2024. Hasilnya ada 50 stasiun yang memenuhi syarat dari 114 stasiun awal. Setiap stasiun dan setiap bulan diberi bobot sama pada perbandingan tahunan.

Pada kelompok tersebut, suhu bulanan tertinggi muncul pada November 2023 (28,36°C) dan terendah pada Februari 2023 (27,05°C). Rata-rata selisih suhu 2024 terhadap 2023 sebesar +0,23°C, dengan 42 dari 50 stasiun memiliki selisih positif. Hasil ini hanya menggambarkan kelompok stasiun yang dianalisis dan belum menunjukkan tren iklim jangka panjang.

Notebook menghasilkan tujuh grafik interaktif Plotly: jumlah laporan bulanan, persentase data kosong, suhu bulanan, heatmap suhu tiap stasiun, pola suhu menurut jam UTC, sebaran suhu berdasarkan koordinat, serta selisih suhu 2024 terhadap 2023 per stasiun. Saya tetap menggunakan UTC sesuai dokumentasi NOAA; jam pada grafik belum dikonversi menjadi WIB, WITA, atau WIT.

Untuk menjalankan ulang, buka notebook 03 setelah notebook 02 selesai dan jalankan seluruh cell secara berurutan. Grafik tersimpan di `output/figures/` sebagai HTML dan dapat dibuka melalui browser. File `plotly.min.js` di folder tersebut perlu tetap bersama file HTML agar grafik bisa dibuka tanpa internet. Folder output dikecualikan dari Git; grafik dapat dibuat ulang dari notebook.

Kelengkapan jam pengamatan masih berbeda antarstasiun. Rata-rata harian memakai jam yang tersedia, jadi hasil tetap berpotensi dipengaruhi pengamatan yang hilang. Sebaran stasiun juga belum mewakili seluruh wilayah Indonesia secara merata.
