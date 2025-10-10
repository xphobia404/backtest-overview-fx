# Trading Dashboard (Frontend Only)

Dashboard ini menampilkan performa trading dari file CSV **tanpa backend** (cukup buka `index.html` di browser).  
Fokus: **pair XAU (MRG)**, tapi mendukung pair lain juga.

---

## Fitur Utama

- **Upload CSV** (delimiter `;`) dengan header:
  ```
  Pair;Action;Tanggal Entry;Tanggal Exit;Entry;TP;SL;Keterangan
  ```
- **Konfigurasi MRG** (di modal awal):
  - Pip Size (MRG) — contoh XAU = `0.1`
  - USD per Pip per 1 Lot — contoh XAU ≈ `1`
  - Modal Awal & Lot per trade
- **Pemisahan Pips:**
  - **Actual Pips (display)** → dihitung otomatis dari granularitas harga di CSV
  - **Profit (MRG)** → dihitung pakai Pip Size **MRG** dari modal
- Tab:
  - **Overall**: total & rata-rata (USD & Pips), winrate, RR, holding period, consecutive win/loss
  - **Per Month**: tabel profit bulanan (biru = positif, merah = negatif, “-” bila tidak ada trade)
  - **Growth**: chart equity (modal awal + akumulasi profit)
- **Detail Streak**:
  - Tabel ringkas (per panjang streak, dikelompokkan/dedup)
  - Kolom: **Type**, **WS/LS**, **Streak**, **Sum**, dan tombol **Detail** (menampilkan tanggal tiap streak)
- **Semua berjalan lokal** (data tidak dikirim ke server)

---

## Cara Pakai

1. Buka file `index.html` di browser (Chrome/Edge/Firefox).
2. Isi **Konfigurasi Awal**:
   - Pilih **Pair** (XAUUSD default) → pastikan **Pip Size (MRG)** & **USD/pip/lot** sesuai broker MRG.
   - Isi **Modal Awal** dan **Lot**.
   - Klik **Mulai**.
3. Upload file **CSV** Anda.
4. Lihat metrik di tab **Overall**, **Per Month**, dan **Growth**.
5. Klik **Lihat Detail** pada kotak Consecutive Profit/Loss → modal **Detail Streak**.
   - Tekan tombol **Detail** di kolom terakhir untuk melihat daftar tanggal streak pada panjang tersebut.

---

## Format CSV

- **Delimiter wajib**: `;`
- **Header wajib** (case-sensitive seperti contoh):
  ```
  Pair;Action;Tanggal Entry;Tanggal Exit;Entry;TP;SL;Keterangan
  ```
- **Nilai kolom**:
  - `Pair`: mis. `XAUUSD`
  - `Action`: `BUY` atau `SELL`
  - `Tanggal Entry` / `Tanggal Exit`: string tanggal yang valid (`YYYY-MM-DD`, dsb.)
  - `Entry`: harga entry (angka)
  - `TP` / `SL`: harga TP/SL (angka)
  - `Keterangan`: `TP` atau `SL` (menentukan exit). Jika kosong/selain itu → `exit = entry` (pips 0).

### Contoh CSV mini

```csv
Pair;Action;Tanggal Entry;Tanggal Exit;Entry;TP;SL;Keterangan
XAUUSD;BUY;2025-01-02;2025-01-02;2388.1;2389.5;2386.0;TP
XAUUSD;SELL;2025-01-03;2025-01-03;2390.3;2388.8;2392.0;TP
XAUUSD;BUY;2025-01-04;2025-01-04;2385.0;2384.1;2387.0;SL
```

> **Catatan**: Untuk **MRG XAU**, umumnya **1 pip = 0.1**.

---

## Cara Hitung (Penting)

### 1) Actual Pips (untuk tampilan pips)
- Dideteksi **otomatis** dari jumlah desimal terbesar di kolom `Entry/TP/SL`.
- Rumus per trade:
  - `delta = (exit - entry)` untuk `BUY`
  - `delta = (entry - exit)` untuk `SELL`
  - `pips_actual = round( delta / pip_actual_unit )`
- **Hanya** dipakai untuk tampilan total/avg pips & streak pips (tanpa desimal).

### 2) Profit (skema MRG)
- Menggunakan **Pip Size (MRG)** dari modal (tidak diubah oleh auto-detect).
- Rumus per trade:
  - `pips_mrg = delta / pip_mrg_unit` (boleh pecahan)
  - `profit_usd = pips_mrg * (USD per pip per 1 lot) * lot`
- Total & rata-rata USD, RR, equity, dan streak USD menggunakan angka profit ini.

---

## Kustomisasi

- **Default Pair** (`PAIR_DEFAULTS`) di `index.html`:
  ```js
  const PAIR_DEFAULTS = {
    XAUUSD: { pipSize: 0.1, usdPerPipLot: 1 }, // MRG (XAU)
    XAGUSD: { pipSize: 0.01,  usdPerPipLot: 0.5 },
    EURUSD: { pipSize: 0.0001,usdPerPipLot: 10 },
    GBPUSD: { pipSize: 0.0001,usdPerPipLot: 10 },
    USDJPY: { pipSize: 0.01,  usdPerPipLot: 10 },
  };
  ```
  Ubah sesuai kebutuhan broker Anda.

- **Format angka**:
  - `fmtMoney`: `$1,234.56` (koma ribuan, 2 desimal)
  - `fmtNum` (pips): `1,234` (tanpa desimal)

---

## Troubleshooting

- **Total Pips 10× lebih besar**  
  Biasanya karena pip size actual berbeda (mis. XAU data 0.1 tapi Anda pakai 0.01).  
  Solusi: actual pips sudah **auto-detect**; profit tetap berdasarkan **pip MRG** dari modal.

- **Profit terasa tidak sesuai**  
  Cek **USD per Pip per 1 Lot** dan **Lot**. Contoh XAU (MRG) lazim:
  - Pip size: `0.1`
  - USD/pip/lot: `1`
  - Lot: sesuai ukuran Anda (mis. `0.10`)

- **Tanggal tidak terbaca**  
  Gunakan format tanggal standar (`YYYY-MM-DD`). Jika kolom tanggal kosong, holding period tidak dihitung.

- **Warna Per Month tidak muncul**  
  Warna dipakai untuk nilai ≠ 0. Bulan tanpa trade tampil “-”.

- **CSV tidak memuat**  
  Pastikan delimiter `;`, header tepat, dan semua angka bisa di-parse (tanpa pemisah ribuan/koma desimal non-standar).

---

## Menjalankan di Lokal

- **Termudah**: klik 2× `index.html` (atau drag ke tab browser).
- Alternatif:
  - VS Code → **Live Server** extension.
  - Host statis (Netlify/GitHub Pages) — unggah `index.html`.

> Tidak perlu Node/Streamlit/backend.

---

## FAQ

**Q: Bisa tambah Export CSV untuk tabel streak?**  
A: Bisa. Saya bisa tambahkan tombol `Export` di modal streak (USD & Pips).

**Q: Bisa filter tanggal?**  
A: Bisa. Tambahkan date range picker sebelum perhitungan.

**Q: Pair lain (mis. EURUSD)?**  
A: Pilih pair di modal → sesuaikan Pip Size (MRG) & USD/pip/lot sesuai broker Anda.

---

## Struktur File

```
index.html   # seluruh kode (HTML + CSS + JS) dalam satu file
README.md    # (file ini)
```

---

## Privasi

- Semua proses di sisi **browser**.
- CSV **tidak di-upload** ke server mana pun.

---

## Lisensi

Bebas digunakan untuk keperluan pribadi/pekerjaan. Attribution dihargai.
