# Technical Assessment — Senior Data Scientist
## Studi Kasus: Evaluasi & Optimasi Rute Laut "Bvarta Bahari"

---

### 1. Konteks Bisnis

**Bvarta Bahari** adalah operator moda transportasi laut (penumpang & kendaraan) yang
melayani jalur **Utara Pulau** di wilayah **Jawa – Bali – Sumatera** dan laut di sekitarnya.
Selama ini perencanaan rute dilakukan **secara manual** berdasarkan pengalaman operasional.
Manajemen kini ingin **mengevaluasi seluruh jaringan rute berbasis data** dan membuka
peluang ekspansi yang terukur.

Anda diminta berperan sebagai data scientist yang ditugaskan untuk menjawab pertanyaan
strategis mereka. Ini adalah masalah **terbuka** — tidak ada satu "jawaban benar". Kami
ingin melihat **cara Anda membingkai masalah, memilih metode, menangani keterbatasan data,
dan mengkomunikasikan rekomendasi**, jauh lebih dari sekadar hasil akhir.

---

### 2. Pertanyaan yang Harus Dijawab

1. **Evaluasi rute eksisting.** Apakah rute-rute yang berjalan saat ini masih bisa
   dioptimasi? Identifikasi rute yang berkinerja baik dan yang bermasalah, lalu berikan
   rekomendasi konkret (mis. penyesuaian frekuensi, penugasan ulang kapal, penyesuaian
   harga, penggabungan/penghentian rute).
2. **Rekomendasi rute baru.** Usulkan rute baru yang masuk akal secara permintaan dan
   operasional, lengkap dengan **estimasi biaya operasional** dan **estimasi revenue**.
3. **Penugasan armada.** Rekomendasikan kapal mana (dari 35 armada) yang sebaiknya melayani
   tiap rute — eksisting maupun baru.
4. **Pemodelan prediktif (inti penilaian).** Bangun **minimal satu model prediktif yang
   substantif** dan jadikan ia **dasar** salah satu rekomendasi di atas — bukan analisis
   deskriptif tambahan. Tunjukkan disiplin pemodelan: pilihan metode yang sesuai karakter
   sinyal, **evaluasi yang benar** (train/validation/test atau backtesting, metrik yang
   relevan, baseline pembanding), analisis error, dan **kuantifikasi ketidakpastian**.
   Mengerjakan lebih dari satu model adalah nilai plus. Pilih dari (atau kombinasikan):
   - **Forecast permintaan** (load factor / penjualan tiket) — dipakai untuk estimasi revenue.
   - **Prediksi pasang surut** dari `tides_hourly.csv` — dipakai untuk *jendela layar aman*.
   - **Prediksi/klasifikasi hari tidak-layar** dari pola cuaca — untuk kuantifikasi risiko &
     penugasan armada.
   - *(opsional)* **Model mobilitas** (gravity/statistik) untuk mengestimasi permintaan rute baru.

   Kami menilai **kedalaman & kejujuran pemodelan** sebagai komponen terberat: metode yang
   cocok, validasi yang disiplin, dan jujur soal sejauh mana sesuatu *bisa* diprediksi.

---

### 3. Batasan (Constraints) yang Wajib Dihormati

- **Rute Wajib harus selalu ada.** Beberapa rute berstatus `wajib` (sesuai peraturan
  Kemenhub). Rute ini **tidak boleh dihapus**, meskipun secara finansial merugi. Anda boleh
  (dan sebaiknya) mengoptimasi *bagaimana* rute itu dijalankan.
- **Ini rute LAUT.** Jarak antar-pelabuhan **bukan garis lurus** — kapal harus mengikuti
  alur laut dan menghindari daratan. Pikirkan bagaimana Anda mengestimasi jarak tempuh laut
  untuk rute yang belum ada datanya.
- **Armada terbatas.** Total 35 kapal dengan tipe, kapasitas, kecepatan, dan biaya berbeda.
  Satu kapal tidak bisa berada di dua tempat pada waktu bersamaan. Perhatikan status armada.
- **Kelayakan operasional.** Pertimbangkan faktor laut: kedalaman pelabuhan vs draft kapal
  (terkait pasang surut), serta kondisi cuaca (gelombang/angin) yang dapat menyebabkan hari
  tidak berlayar.

---

### 4. Deliverables

Kirimkan tiga hal:

1. **Kode / notebook** (Python diutamakan; format bebas, mis. Jupyter/Colab) — reproducible,
   terstruktur, dengan penjelasan langkah analisis. Sertakan instruksi cara menjalankannya.
2. **Ringkasan teknis (writeup), maks. ±3 halaman** berisi: pendekatan & metodologi,
   asumsi yang Anda ambil, **keterbatasan analisis**, dan apa yang akan Anda lakukan jika
   punya waktu/data lebih. Khusus untuk model: sertakan **cara validasi, metrik, baseline,
   analisis error, dan ketidakpastian**, serta bagaimana model itu dipakai dalam keputusan.
3. **Output untuk klien** — temuan utama dalam bentuk yang bisa dipahami stakeholder
   non-teknis (tabel rekomendasi, peta, dan/atau visual). Anda akan diminta
   mempresentasikan ini dalam sesi wawancara (30 menit).

> Format ketiga deliverable di atas **tetap** (agar antar-kandidat dapat dibandingkan dengan
> adil). Namun **metode, kedalaman analisis, dan bagian mana yang Anda dahulukan sepenuhnya
> pilihan Anda.**

---

### 5. Cara Mengerjakan & Estimasi Waktu

- Data tersedia di folder `data/` (lihat `DATA_DICTIONARY.md`). Data sengaja dibuat
  menyerupai kondisi nyata — mungkin tidak sempurna.
- Kerjakan di environment Anda sendiri (lokal/Colab); bebas memakai library apa pun.
- **Ini take-home. Batas pengumpulan: 7 hari.**
- **Prioritaskan cakupan pengerjaan dan strategi delivery sesuai waktu yang tersedia.**
- Kualitas reasoning > kuantitas output. Untuk bagian yang tidak sempat dikerjakan, cukup
  uraikan pendekatan yang akan Anda ambil.
- *Petunjuk teknis:* untuk **jarak laut** rute baru, Anda dapat mengkalibrasi faktor koreksi
  dari rute eksisting (yang sudah memiliki jarak laut sebenarnya) terhadap jarak garis lurus
  — tidak memerlukan data alur pelayaran khusus.

---

### 6. Apa yang Kami Nilai

| Dimensi | Yang kami cari |
|---|---|
| **Problem framing** | Menerjemahkan pertanyaan bisnis jadi pendekatan analitis yang masuk akal |
| **Pemodelan & validasi** | Metode sesuai karakter sinyal; evaluasi benar (split/backtest, metrik, baseline); sadar ketidakpastian & overfitting |
| **Metodologi & trade-off** | Pilihan metode beralasan; sadar kelebihan/kekurangannya |
| **Penanganan data** | Menyadari & menangani data kotor, hilang, atau tidak konsisten secara sadar |
| **Penalaran spasial & domain** | Memahami bahwa ini rute laut dengan constraint nyata (cuaca, draft, jarak laut) |
| **Estimasi & kuantifikasi** | Estimasi biaya/revenue dibangun dengan logika yang transparan |
| **Komunikasi** | Writeup & presentasi jelas, termasuk asumsi & keterbatasan |

> Untuk peran ini, **Pemodelan & validasi** dan **Metodologi & trade-off** berbobot paling
> besar. Penalaran bisnis penting sebagai konteks dan untuk menghubungkan model ke keputusan —
> tetapi analisis deskriptif yang kuat **tanpa** pemodelan yang solid belum memenuhi level
> yang kami cari.

Selamat mengerjakan. Jika ada asumsi yang perlu Anda ambil karena data tidak lengkap,
**ambil saja dan tuliskan asumsinya** — itu bagian dari penilaian.
