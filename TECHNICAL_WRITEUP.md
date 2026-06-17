# RINGKASAN TEKNIS (TECHNICAL WRITEUP): Bvarta Bahari Route & Fleet Optimization

**Oleh:** Fadilla Zundina 
**Proyek:** Optimasi Rute, Alokasi Armada, dan Ekspansi Jaringan (Bvarta Bahari)

Dokumen ini merangkum metodologi analitik, arsitektur pemodelan, asumsi, batasan, serta rekomendasi masa depan yang mendasari keputusan strategis untuk restrukturisasi jaringan pelayaran Bvarta Bahari. Seluruh analisis telah dikerjakan menggunakan kerangka kerja Data Science terstandarisasi mulai dari *Exploratory Data Analysis* (EDA) hingga Pemodelan Regresi Spasial.

---

## 1. Pendekatan & Metodologi Pemodelan Integratif

Arsitektur analitik pada proyek ini dibangun menggunakan filosofi *Integrated Risk-Adjusted Forecasting*. Kami menyadari bahwa memprediksi permintaan tiket (demand) secara terisolasi tanpa memperhitungkan risiko pembatalan pelayaran akibat cuaca ekstrem akan menghasilkan ekspektasi finansial yang bias (over-optimistic). Oleh karena itu, kami mengembangkan dua lapis model yang bekerja secara sekuensial:

### A. Model 1: Prediksi Permintaan (LightGBM Regressor)
Tujuan dari model ini adalah memprakirakan volume penjualan tiket harian (*Load Factor*).
- **Algoritma:** LightGBM Regressor. Dipilih karena kapabilitasnya yang sangat superior dalam menangani fitur spasio-temporal dan non-linearitas (hari libur, efek *lag* mingguan).
- **Validasi Temporal Split:** Untuk mencegah kebocoran data (*data leakage*), dataset tidak dipisah secara acak (Random Split), melainkan dipotong murni berdasarkan kronologi waktu (Train: Juni 2022 - Mei 2024; Test: Juni 2024 - Mei 2025). 
- **Evaluasi & Baseline:** Kinerja model dievaluasi menggunakan metrik *Mean Absolute Error* (MAE) dan *Root Mean Squared Error* (RMSE) lalu dibandingkan dengan **Baseline Naive Lag-7** (model naif yang mengasumsikan demand hari ini sama dengan 7 hari lalu).
- **Kuantifikasi Ketidakpastian:** Prediksi model tidak dikeluarkan sebagai titik tunggal, melainkan dibekali *95% Confidence Interval* untuk memberikan rentang mitigasi (best-case & worst-case scenario) bagi departemen komersial.

### B. Model 2: Kuantifikasi Risiko Batal Layar (LightGBM Classifier)
Model ini berfungsi untuk mengidentifikasi probabilitas sebuah hari dinyatakan tidak aman untuk berlayar.
- **Integrasi Fitur Meteorologis:** Model menggabungkan data batas toleransi gelombang maksimum (*max operating wave*) dari tiap kapal dengan data curah hujan dan kecepatan angin aktual (`weather_rainfall_daily.csv`, `weather_wind_speed_daily.csv`).
- **Kinerja:** Model mencapai metrik klasifikasi sempurna dengan **ROC-AUC sebesar 99.02%**, membuktikan bahwa sinyal meteorologis sangat deterministik terhadap pembatalan.

### C. The "Risk-Adjusted Expected Revenue" Formula
Nilai ekspektasi *revenue* akhir dikalibrasi menggunakan persamaan:
`E(Revenue) = Forecast_Demand × Harga_Tiket × (1 - Probabilitas_Batal_Layar)`
Pendekatan integratif ini terbukti sangat ampuh meredam bias optimistis; eksperimen *backtesting* menunjukkan pendekatan ini **memotong *error* proyeksi revenue dari +11.5% (tanpa penyesuaian risiko) menjadi hanya -0.14%** (nyaris presisi sempurna dengan aktual).

---

## 2. Metodologi Optimasi & Alokasi Armada (Fleet Allocation)

Masalah penugasan 35 kapal ke berbagai rute bukanlah perkara sederhana, melainkan tantangan optimasi diskrit berskala besar. Alih-alih menggunakan *Mixed-Integer Linear Programming* (MILP) konvensional yang sering menjadi *black-box* dan sulit dinavigasi secara dinamis, kami merancang **Algoritma Greedy Heuristic berbasis Efisiensi Profit**.

- **Sistem Prioritas (Routing Policy):** Algoritma diinstruksikan untuk memprioritaskan penyelesaian rute berstatus `wajib` terlebih dahulu demi mematuhi mandat regulasi pemerintah (meskipun rute tersebut merugi). Setelah rute wajib terpenuhi, algoritma baru mengalokasikan kapal ke rute komersial (`rancangan`) secara *descending* berdasarkan besaran demand mingguan.
- **Kepatuhan Kendala (Constraint Satisfaction):**
  1. *Operational Fatigue*: Kapal dilarang keras melampaui jam operasional mingguan yang dipatok ketat pada **134 jam/minggu** (telah dikurangi 20% *buffer* untuk perbaikan/inspeksi). Mekanisme ini mencegah *double-booking* yang mustahil secara fisik. Rute besar dengan demand raksasa (misal: R01) dipecahkan penugasannya ke *multi-ship allocation* (paralel 3 kapal berbeda).
  2. *Safety Draft Constraint*: Sistem menolak kapal yang memiliki `draft_m` lebih dalam dari *Effective Max Draft* pelabuhan. Angka *effective draft* ini bukan cuma kedalaman statis, melainkan sudah dikurangi dengan anomali surut terendah (pasang surut minimum) dari data oseanografi `tides_hourly.csv`.
- **Dampak Finansial:** Algoritma mensubstitusi kapal boros dengan kapal berefisiensi tinggi secara presisi, lalu memotong rute komersial yang irasional secara ekonomi (status *Suspended*). Manuver bedah rute ini memicu ledakan efisiensi struktural: **Total Profit Bulanan naik drastis dari Rp 39.7 Miliar menjadi Rp 54.7 Miliar** (Kenaikan Laba Bersih sebesar Rp 15 Miliar per bulan).

---

## 3. Asumsi-Asumsi Analisis

1. **Harga Tiket Tertimbang (Weighted Ticket Price):** Karena tidak ada rincian pembelian per kelas penumpang pada data riil harian, diestimasi bahwa penjualan tiket mengikuti distribusi probabilitas historis industri: *70% Ekonomi, 20% Bisnis, 10% Kabin* (untuk kapal multi-kelas) atau *80% Ekonomi, 20% Bisnis* (untuk kapal dua kelas).
2. **Faktor Koreksi Jarak Laut:** Jarak spasial (*Haversine*) tidak merepresentasikan rute zigzag pelayaran laut asli. Kami mendeterminasi faktor koreksi empiris sebesar **1.2744** (Berdasarkan regresi linear jarak lurus vs jarak terjadwal). Angka ini dipakai wajib untuk memproyeksi kebutuhan *Nautical Miles* di rute ekspansi baru agar perhitungan OPEX bensin riil tidak meleset (under-budget).
3. **Penyusutan OPEX Proporsional:** Biaya bahan bakar dan biaya pemeliharaan pelabuhan bulanan dihitung berskala proporsional terhadap frekuensi *trip* dan konsumsi bahan bakar kapal (`Liters Per Hour`). Sementara, biaya ABK (Crew Cost) diasumsikan didistribusikan berdasarkan persentase jam operasional yang terpakai oleh kapal tersebut terhadap total jam kerjanya (134 jam).

---

## 4. Keterbatasan Analisis & Mitigasi Penanganan Data

- **Kualitas Data:** Terdapat anomali *missing values* (~0.95%) pada fitur `load_factor` dan ketidakkonsistenan tipe data kalender.
- **Mitigasi:** Ketimbang menggunakan *Mean Imputation* yang merusak distribusi ekor (tail distribution) pada data runtun waktu (time-series), kami menjatuhkan (*drop*) *missing values* secara eksplisit selama tahap pelatihan algoritma LightGBM Regressor agar model belajar murni dari sinyal organik.
- **Kendala Proyeksi Harga Rute Baru:** Tidak adanya data pasar yang *exact* untuk rute baru memaksa kami menggunakan rata-rata harga per NM (Nautical Mile) tingkat korporat. Ke depannya, harga ini bisa berpotensi melenceng akibat peta daya beli lokal pelabuhan yang belum dimodelkan.

---

## 5. Future Works (Rekomendasi Tahap Lanjut)

Jika diberikan ruang waktu penelitian yang lebih longgar dan aliran data sensor riil yang terus mengalir, model ini akan ditingkatkan melalui:
1. **Integer Linear Programming (MILP):** Peralihan dari *Greedy Heuristic* menuju algoritma penyelesaian persamaan diferensial matematis (seperti `Gurobi` atau `PuLP`) untuk menjamin optimasi komputasional pada *Global Maximum* mutlak, bukan sekadar optimalisasi proksimal lokal.
2. **IoT Sensor & Dynamic Draft Estimation:** Integrasi real-time telemetry pasang-surut dan IoT sarat beban kapal (*dynamic draft displacement*) dari tiap pelabuhan untuk mengaktifkan batas operasional draft yang hidup menit-ke-menit (tidak sekadar merujuk *baseline* pasang surut minimum bulanan yang ekstrem).
3. **Dynamic Pricing Mechanism:** Membangun *Reinforcement Learning Agent* yang mampu merekayasa ulang Harga Tiket secara *on-the-fly* berdasarkan tren *load factor* 7-hari ke belakang dan cuaca esok hari untuk mencapai maksimalisasi ekstraksi *revenue*.
