# Data Tugas 1

## Dataset yang Dipilih

| Item | Isi |
|---|---|
| Nama dataset | NOAA Global Hourly – Indonesia 2023–2024 |
| Penyedia | National Centers for Environmental Information (NCEI), NOAA |
| Sumber utama | https://www.ncei.noaa.gov/data/global-hourly/ |
| Direktori data | https://www.ncei.noaa.gov/data/global-hourly/access/ |
| Ketentuan penggunaan | Data dapat diakses secara publik melalui NOAA/NCEI. Sumber data wajib dicantumkan dalam dokumentasi dan hasil analisis. |
| Ukuran data mentah | 523,96 MB |
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