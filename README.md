# Analisis Gempa Bumi di Indonesia (2008-2026)

> **Temuan utama:** Jumlah gempa yang tercatat melonjak hampir dua kali lipat pada 2025 (21.021 kejadian), tetapi lonjakan itu tidak muncul pada gempa magnitudo 4 ke atas, yang stabil di kisaran 1.400-2.800 per tahun pada tahun-tahun penuh (2009-2025). Kemungkinan besar lonjakan berasal dari gempa kecil yang lebih banyak tercatat, bukan dari gempa yang benar-benar bertambah.

![Dashboard Analisis Gempa](dashboard.png)

## Tujuan dan pertanyaan

Proyek ini menganalisis katalog gempa BMKG untuk menjawab tiga pertanyaan:

1. Wilayah mana yang paling sering mengalami gempa?
2. Apakah ada hubungan antara kedalaman dan kekuatan (magnitudo) gempa?
3. Apakah jumlah gempa tercatat naik, dan apakah itu karena gempanya bertambah atau karena pencatatannya berubah?

## Sumber data

- Dataset Kaggle **Earthquakes in Indonesia** (kekavigi): https://www.kaggle.com/datasets/kekavigi/earthquakes-in-indonesia
- Data asli berasal dari Katalog Gempa Awal (preliminary) BMKG: https://repogempa.bmkg.go.id/eventcatalog
- File yang dipakai: `katalog_gempa_v2.tsv` (176.384 baris, 37 kolom, mulai 1 November 2008). File lama `katalog_gempa.csv` tidak dipakai karena penulis dataset menandainya sebagai arsip sebelum migrasi sistem BMKG.
- Lisensi dataset: Open Database; isi data milik penulis aslinya (BMKG).

## Alat

Python (pandas) di Kaggle untuk pembersihan dan analisis, Power BI Desktop untuk dashboard.

## Langkah pembersihan

1. **Memilih kolom.** Dari 37 kolom hanya 8 dipakai: `eventID`, `datetime`, `latitude`, `longitude`, `magnitude`, `mag_type`, `depth`, `location`. Kolom mekanisme fokal (berakhiran FM dan kolom setelahnya) tidak dipakai karena sebagian besar kosong dan di luar cakupan analisis ini.
2. **Memeriksa nilai kosong.** `magnitude` dan `mag_type` sama-sama kosong pada 1.405 baris (sekitar 0,8%) dan baris itu sama persis, jadi dibuang dari analisis. `location` kosong pada 28 baris.
3. **Mengubah waktu.** Kolom `datetime` memiliki format campuran (ada yang dengan pecahan detik, ada yang tanpa), jadi dibaca dengan `format="ISO8601"` lalu diambil tahunnya.
4. **Menyaring wilayah.** Versi 2 dataset tidak lagi dipotong ke wilayah Indonesia, jadi disaring dengan kotak koordinat lintang -11 sampai 6 dan bujur 94 sampai 142. Hasilnya **142.286 baris**.
5. **Membuat kelas kedalaman:** dangkal (di bawah 70 km), menengah (70-300 km), dalam (di atas 300 km).

## Temuan

### 1. Jumlah gempa mentah per tahun menyesatkan

| Tahun | Semua gempa | Magnitudo 4 ke atas |
|---|---|---|
| 2008 (mulai November) | 239 | 96 |
| 2009 | 4.134 | 1.823 |
| 2010 | 7.646 | 2.616 |
| 2011 | 6.261 | 1.885 |
| 2012 | 8.510 | 2.217 |
| 2013 | 5.051 | 1.398 |
| 2014 | 7.209 | 1.819 |
| 2015 | 7.511 | 1.874 |
| 2016 | 7.991 | 1.752 |
| 2017 | 8.898 | 1.835 |
| 2018 | 12.517 | 2.113 |
| 2019 | 13.635 | 2.804 |
| 2020 | 9.911 | 2.393 |
| 2021 | 12.653 | 2.450 |
| 2022 | 12.438 | 2.245 |
| 2023 | 13.389 | 2.274 |
| 2024 | 9.097 | 1.716 |
| 2025 | **21.021** | 1.745 |
| 2026 (belum penuh) | 8.273 | 1.199 |

Pada 2025 jumlah semua gempa naik sekitar dua kali lipat dibanding 2024, sedangkan gempa magnitudo 4 ke atas hampir tidak berubah. Gempa besar hampir selalu tertangkap sensor di mana pun, sehingga kenaikan yang hanya muncul pada gempa kecil lebih mungkin mencerminkan perubahan pencatatan. Penyebab pastinya (penambahan sensor, perubahan pemrosesan, atau hal lain) belum dapat dipastikan dari data ini.

### 2. Jenis magnitudo berganti-ganti antar tahun

Data mencampur beberapa jenis magnitudo (`M`, `MLv`, `mb`, `Mw`, dan lainnya). Jenis `M` dominan pada 2010-2012, 2019-2022, 2024, dan 2026, sedangkan `MLv` dominan pada 2013-2018, 2023, dan 2025. Pada 2025, sekitar 17.600 dari 21.000 gempa bertipe `MLv`. Jenis magnitudo juga berkaitan dengan ukuran gempa: `MLv` rata-rata 3,3 dan `M` rata-rata 3,7, sedangkan `mb` rata-rata 4,9 dan jenis `Mw` 4,9 sampai 6,1. Jadi rata-rata magnitudo antar tahun dari data campuran ini tidak bisa dibandingkan begitu saja.

### 3. Gempa besar terkonsentrasi di Indonesia timur

Dari sekitar 36.250 gempa magnitudo 4 ke atas, 10 wilayah teratas menyumbang sekitar 60%.

| Wilayah | Jumlah gempa 4+ |
|---|---|
| Banda Sea | 5.278 |
| Northern Molucca Sea | 2.535 |
| Minahassa Peninsula, Sulawesi | 2.231 |
| Talaud Islands | 2.079 |
| Southern Sumatra | 2.047 |
| Halmahera | 1.912 |
| Northern Sumatra | 1.556 |
| Irian Jaya Region | 1.555 |
| Java | 1.411 |
| South of Java | 1.302 |

Wilayah di Indonesia timur (Banda, Maluku, Sulawesi Utara, Talaud, Halmahera) mendominasi. Sumatra bagian utara dan selatan jika digabung (sekitar 3.600) berada di urutan ketiga.

### 4. Kedalaman tidak berkaitan dengan kekuatan

| Kelas kedalaman | Jumlah gempa 4+ | Rata-rata magnitudo |
|---|---|---|
| Dangkal (di bawah 70 km) | 24.416 (sekitar 67%) | 4,49 |
| Menengah (70-300 km) | 10.219 (sekitar 28%) | 4,51 |
| Dalam (di atas 300 km) | 1.619 (sekitar 4,5%) | 4,49 |

Sekitar dua pertiga gempa 4+ terjadi di kedalaman dangkal. Rata-rata magnitudo hampir sama di ketiga kelas. Perlu diingat bahwa karena data dibatasi pada magnitudo 4 ke atas, rata-ratanya memang cenderung mengumpul di sekitar 4,5, sehingga perbandingan ini tidak terlalu informatif.

## Kesimpulan

- Jumlah gempa mentah per tahun tidak layak dipakai untuk menyimpulkan bahwa gempa bertambah. Lonjakan 2025 hanya terlihat pada gempa kecil, sedangkan gempa magnitudo 4 ke atas stabil di kisaran 1.400-2.800 per tahun.
- Perbandingan antar tahun sebaiknya memakai batas magnitudo, bukan semua kejadian.
- Gempa magnitudo 4 ke atas terkonsentrasi di Indonesia timur, dengan Banda Sea sebagai wilayah teratas.
- Pada gempa magnitudo 4 ke atas, tidak terlihat hubungan antara kedalaman dan kekuatan gempa.

## Keterbatasan

- **Data preliminary.** Penulis dataset menyebut solusi gempa terbaru masih dapat direvisi, dan 2008 (mulai November) serta 2026 bukan tahun penuh.
- **Jenis magnitudo bercampur** (`M`, `MLv`, `mb`, `Mw`, dan lainnya), sehingga rata-rata magnitudo antar tahun tidak sepenuhnya sebanding.
- **Penyaringan wilayah memakai kotak koordinat, bukan batas negara.** Kotak itu juga mencakup perairan dan daratan di sekitar Indonesia, misalnya sebagian Filipina selatan, Papua Nugini bagian barat, dan Timor-Leste.
- **Dua sumber hitungan berbeda pada grafik tren.** Kolom "semua gempa" dihitung dari data sebelum penyaringan wilayah dan magnitudo kosong (176.384 baris), sedangkan "magnitudo 4 ke atas" dari data yang sudah disaring. Selisihnya tidak mengubah kesimpulan, tetapi angkanya berasal dari dua dasar hitung.
- **Luas wilayah pada kolom `location` tidak sama.** Banda Sea sangat luas, sehingga sebagian angkanya mencerminkan luas wilayah, bukan hanya tingkat aktivitas.
- **Penyebab lonjakan 2025 belum terbukti.** Dugaan perubahan pencatatan didasarkan pada pola data dan belum dikonfirmasi oleh BMKG.
- **Jumlah gempa bukan tingkat bahaya.** Banyak gempa terjadi di laut dalam yang jauh dari permukiman.

## Saran dan langkah lanjutan

**Untuk yang memakai data ini**
- Pakai batas magnitudo (misalnya 4 ke atas) saat membandingkan jumlah gempa antar tahun, dan sebutkan batas itu di setiap grafik.
- Jangan membandingkan magnitudo antar jenis (`M`, `MLv`, `mb`, `Mw`) tanpa penyetaraan. Kalau perlu, analisis per jenis atau fokus ke jenis tertentu saja.
- Buang atau tandai tahun yang tidak penuh (2008 dan 2026) pada analisis tren.

**Untuk memperkuat analisis ini**
1. Cari tahu penyebab lonjakan 2025: hubungi atau periksa dokumentasi BMKG tentang perubahan sensor dan pemrosesan katalog, serta bandingkan dengan katalog USGS untuk periode yang sama.
2. Tentukan magnitudo kelengkapan (batas minimum yang konsisten tercatat) per periode secara lebih formal, bukan hanya memilih angka 4.
3. Ganti kotak koordinat dengan batas wilayah yang lebih tepat (misalnya poligon Indonesia) atau saring berdasarkan kolom lokasi.
4. Lengkapi pertanyaan pertama dengan analisis perubahan pola wilayah per tahun (tabel tahun x wilayah untuk beberapa wilayah teratas).
5. Selidiki tahun dengan nilai tinggi pada gempa 4+ (misalnya 2019) untuk melihat wilayah dan kejadian apa yang mendorongnya.

## Isi repositori

- `analisis_gempa.ipynb`: notebook analisis (Python)
- `gempa.pbix`: file dashboard Power BI
- `dashboard.png` dan `dashboard.pdf`: tampilan dashboard

## Cara membuka dashboard

File `.pbix` memerlukan **Power BI Desktop** (hanya untuk Windows). Jika tidak memilikinya, lihat `dashboard.png` atau `dashboard.pdf`. Notebook dijalankan di Kaggle dengan menambahkan dataset di atas sebagai input.

## Dibuat oleh

Zikri
