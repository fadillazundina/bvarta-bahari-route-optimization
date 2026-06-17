# Data Dictionary — Bvarta Bahari

Semua file ada di folder `data/`. Periode data: **1 Juni 2022 – 31 Mei 2025** (3 tahun).
Mata uang: **IDR**. Jarak: **nautical mile (nm)**. Kecepatan: **knot**.

> Data sintetis yang dibuat menyerupai kondisi nyata. Perhatikan: ada beberapa
> ketidakkonsistenan yang disengaja (format, nilai hilang) seperti pada sistem produksi.

---

### `ports.csv` / `ports.geojson` — Daftar Pelabuhan (19 pelabuhan)
| kolom | tipe | keterangan |
|---|---|---|
| `port_id` | str | kode unik pelabuhan (kunci join utama) |
| `port_name` | str | nama pelabuhan |
| `island`, `province`, `region` | str | lokasi administratif |
| `sea_area` | str | wilayah perairan (kunci join ke data cuaca laut) |
| `lat`, `lon` | float | koordinat (WGS84). GeoJSON memakai CRS84 (lon,lat) |
| `port_class` | str | Utama / Pengumpul / Pengumpan |
| `max_draft_m` | float | kedalaman alur maksimum saat kondisi normal (meter) |

### `fleet.csv` — Armada (35 kapal)
| kolom | tipe | keterangan |
|---|---|---|
| `ship_id` | str | kode unik kapal |
| `ship_name`, `ship_type` | str | RoRo Ferry / Fast Ferry / Passenger Ship / Cargo-RoPax |
| `year_built` | int | tahun pembuatan |
| `pax_capacity` | int | kapasitas penumpang |
| `vehicle_capacity` | int | kapasitas kendaraan (0 jika tidak relevan) |
| `cargo_capacity_ton` | int | kapasitas kargo (ton) |
| `service_speed_knots` | float | kecepatan layanan |
| `draft_m` | float | draft kapal (meter) — bandingkan dgn `max_draft_m` pelabuhan |
| `max_operating_wave_m` | float | batas tinggi gelombang aman untuk beroperasi (m) |
| `fuel_consumption_lph` | int | konsumsi bahan bakar (liter/jam) pada kecepatan layanan |
| `daily_crew_cost_idr` | int | biaya kru per hari |
| `status` | str | active / standby / maintenance |

### `routes_existing.csv` — Rute Eksisting (16 rute)
| kolom | tipe | keterangan |
|---|---|---|
| `route_id` | str | kode unik rute |
| `origin_port_id`, `dest_port_id` | str | pelabuhan asal & tujuan (→ `ports`) |
| `route_type` | str | **`wajib`** (tidak boleh dihapus) atau `rancangan` |
| `assigned_ship_id` | str | kapal yang ditugaskan saat ini (→ `fleet`) |
| `frequency_per_week` | int | jumlah keberangkatan per minggu |
| `distance_nm` | float | jarak tempuh laut aktual (sudah memperhitungkan alur) |
| `scheduled_duration_hr` | float | durasi terjadwal per perjalanan (jam) |

### `route_prices.csv` — Harga Tiket
| kolom | tipe | keterangan |
|---|---|---|
| `route_id` | str | → `routes_existing` |
| `ticket_class` | str | Ekonomi / Bisnis / Kabin (Kabin hanya rute jarak jauh) |
| `price_idr` | int | harga per tiket |

### `orders_history_daily.csv` — Riwayat Pesanan Harian
| kolom | tipe | keterangan |
|---|---|---|
| `date` | date (YYYY-MM-DD) | tanggal |
| `route_id` | str | → `routes_existing` |
| `trips` | int | jumlah perjalanan hari itu (**0 jika tidak berlayar**) |
| `seats_available` | int | total kursi tersedia hari itu |
| `tickets_sold` | int | tiket terjual (**ada nilai hilang** akibat downtime sistem) |
| `load_factor` | float | rasio keterisian (0–1) (**ada nilai hilang**) |
| `cancelled` | int | 1 jika keberangkatan dibatalkan (cuaca / hari khusus), 0 jika tidak |

### `calendar_events.csv` — Kalender Hari Khusus (eksogen)
| kolom | tipe | keterangan |
|---|---|---|
| `date` | date | tanggal |
| `event_name` | str | nama event (mis. Idul Fitri, Nataru, Nyepi, Libur Sekolah) |
| `event_type` | str | national_holiday / religious / school_holiday |

> Berguna sebagai variabel eksogen untuk forecast permintaan. Perhatikan pola mudik/balik
> (Idul Fitri) dan Nataru. Catatan domain: pada hari tertentu, layanan dari/ke suatu wilayah
> bisa berhenti total.

### `route_opex_monthly.csv` — Biaya Operasional Rute Eksisting (bulanan)
| kolom | tipe | keterangan |
|---|---|---|
| `route_id` | str | → `routes_existing` |
| `month` | str (YYYY-MM) | bulan |
| `fuel_cost_idr`, `crew_cost_idr`, `port_fees_idr`, `maintenance_idr` | int | komponen biaya |
| `total_opex_idr` | int | total biaya operasional bulan tsb |

### `tides_hourly.csv` — Observasi Muka Air per Jam (≈500k baris, 3 tahun)
| kolom | tipe | keterangan |
|---|---|---|
| `datetime` | datetime (tz +07:00) | stempel waktu **per jam**, WIB |
| `port_id` | str | → `ports` / `tide_stations` |
| `water_level_m` | float | tinggi muka air terukur di atas chart datum (**ada gap akibat downtime sensor**) |

> Resolusi per jam selama **3 tahun**, sehingga tersedia beberapa siklus tahunan untuk
> melatih & menguji model (mis. latih 2 tahun, uji 1 tahun). Ini observasi mentah (pasut astronomik +
> faktor non-astronomik + noise) — silakan turunkan sendiri pasang/surut harian bila perlu.

### `tide_stations.csv` — Metadata Stasiun Pasut
| kolom | tipe | keterangan |
|---|---|---|
| `station_id`, `port_id` | str | kode stasiun & pelabuhan |
| `port_name`, `lat`, `lon`, `sea_area` | — | lokasi |
| `datum` | str | acuan ketinggian (LAT) |
| `timezone`, `sampling` | str | zona waktu & resolusi (hourly) |

### `weather_wind_wave_daily.csv` — Angin & Gelombang Laut Harian per Wilayah Perairan
| kolom | tipe | keterangan |
|---|---|---|
| `date` | **DD/MM/YYYY** | ⚠️ format tanggal berbeda dari file lain |
| `sea_area` | str | → `ports.sea_area` |
| `wind_speed_knots` | float | kecepatan angin (knot) |
| `wave_height_m` | float | tinggi gelombang signifikan (m) |

### `weather_rainfall_daily.csv` — Curah Hujan Harian per Region
| kolom | tipe | keterangan |
|---|---|---|
| `date` | date (YYYY-MM-DD) | tanggal |
| `region` | str | → `ports.region` |
| `rainfall_mm` | float | curah hujan (mm) |

### `mobility_daily.csv` — Mobilitas Antar-Region Harian
| kolom | tipe | keterangan |
|---|---|---|
| `date` | date | tanggal |
| `origin_region`, `dest_region` | str | → `ports.region` (pasangan tak-berarah; satu baris per pasangan) |
| `estimated_travelers` | int | estimasi jumlah orang berpindah (semua moda, bukan hanya laut) |

---

**Catatan join:** `mobility` dan `weather_rainfall` memakai level **region**, sedangkan
`weather_wind_wave` memakai level **sea_area**, dan `tides` memakai level **port**.
Tabel `ports` adalah jembatan antar semua level ini.
