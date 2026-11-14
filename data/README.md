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

Dataset berasal dari NOAA Global Hourly atau Integrated Surface
Database (ISD). Dataset berisi pengamatan cuaca per jam dari stasiun
meteorologi Indonesia.

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

## Mengunduh dan Memverifikasi Data

1. Jalankan environment sesuai README utama dan buka notebook `notebooks/01_data_profiling.ipynb`.
2. Jalankan seluruh cell secara berurutan. Cell awal mengunduh metadata stasiun dari https://www.ncei.noaa.gov/pub/data/noaa/isd-history.csv.
3. Notebook memilih stasiun berkode negara `ID` yang aktif pada tahun pengamatan, lalu mengunduh CSV tahun 2024 dan 2023 dari `https://www.ncei.noaa.gov/data/global-hourly/access/{tahun}/{USAF}{WBAN}.csv`.
4. File yang sudah tersedia dilewati. Periksa daftar kegagalan unduhan; unduhan lengkap pada pengambilan ini menghasilkan 114 file per tahun.
5. Jalankan profiling untuk memeriksa ukuran, jumlah baris, skema, rentang waktu, null, duplikasi, dan jenis laporan.

Profiling hanya membaca CSV dalam subfolder `2023` dan `2024`. File di akar `data/raw/noaa_global_hourly/` tidak disertakan. Data mentah tidak diunggah ke GitHub; unduhan memerlukan koneksi internet. Isi sumber NOAA dapat berubah sehingga hasil unduhan baru perlu diverifikasi kembali.

## Batasan Profiling

Nilai null bawaan tidak mencakup kode sentinel NOAA seperti `+9999` dan `99999`. Nilai dan kode kualitas akan diuraikan pada Milestone 2. Pemeriksaan duplikasi awal membandingkan 14 kolom inti, bukan seluruh atribut CSV. Data mentah dipertahankan tanpa perubahan.

## Data Bersih Milestone 2

Jalankan `notebooks/02_data_cleaning.ipynb` setelah notebook profiling. Hasilnya disimpan sebagai `data/processed/noaa_indonesia_2023_2024.parquet` dan tidak dimasukkan ke Git. Notebook menampilkan jumlah data mentah, sentinel, duplikasi persis, jumlah nilai yang menjadi null, serta profil DuckDB `SUMMARIZE`. Pada data lokal yang diuji, hasil bersih berisi 1.273.803 laporan dari 114 stasiun. Duplikasi stasiun dan waktu yang memiliki jenis laporan berbeda tetap dipertahankan.

Definisi dan kode kualitas merujuk pada [dokumentasi format ISD NOAA](https://www.ncei.noaa.gov/pub/data/noaa/isd-format-document.pdf). Batas rentang fisik yang dipakai notebook adalah aturan kewajaran analisis, sehingga perlu ditinjau untuk setiap pertanyaan penelitian.
