Trading Dashboard (Frontend Only)

Dashboard ini menampilkan performa trading dari file CSV tanpa backend (cukup buka index.html di browser).
Fokus: pair XAU (MRG), tapi mendukung pair lain juga.

Fitur Utama

Upload CSV (delimiter ;) dengan header:

Pair;Action;Tanggal Entry;Tanggal Exit;Entry;TP;SL;Keterangan


Konfigurasi MRG (di modal awal):

Pip Size (MRG) — contoh XAU = 0.1

USD per Pip per 1 Lot — contoh XAU ≈ 1

Modal Awal & Lot per trade

Pemisahan Pips:

Actual Pips (display) → dihitung otomatis dari granularitas harga di CSV

Profit (MRG) → dihitung pakai Pip Size MRG dari modal

Tab:

Overall: total & rata-rata (USD & Pips), winrate, RR, holding period, consecutive win/loss

Per Month: tabel profit bulanan (warna biru +, merah −, “-” bila tidak ada trade)

Growth: chart equity (modal awal + akumulasi profit)

Detail Streak:

Tabel ringkas (per panjang streak, dikelompokkan dedup)

Kolom: Type, WS/LS, Streak, Sum

Tombol Detail → modal berisi tanggal-tanggal untuk panjang streak tersebut

Semua berjalan lokal (data tidak dikirim ke server)

Cara Pakai

Buka file index.html di browser (Chrome/Edge/Firefox).

Isi Konfigurasi Awal:

Pilih Pair (XAUUSD default) → bisa ubah Pip Size (MRG) & USD/pip/lot sesuai broker MRG.

Isi Modal Awal dan Lot.

Klik Mulai.

Upload file CSV Anda.

Lihat metrik di tab Overall, Per Month, dan Growth.

Klik Lihat Detail pada kotak Consecutive Profit/Loss untuk membuka modal Detail Streak.

Di tabel, klik tombol Detail (kolom terakhir) untuk melihat tanggal tiap streak dengan panjang tersebut.

Format CSV

Delimiter wajib: ;

Header wajib (case-sensitive sesuai contoh):

Pair;Action;Tanggal Entry;Tanggal Exit;Entry;TP;SL;Keterangan


Nilai kolom:

Pair: mis. XAUUSD

Action: BUY atau SELL

Tanggal Entry / Tanggal Exit: string tanggal yang bisa dibaca JS (YYYY-MM-DD, YYYY/MM/DD, dll.)

Entry: harga entry (angka)

TP / SL: harga TP/SL (angka)

Keterangan: TP atau SL (untuk menentukan exit). Jika kosong/selain itu, dianggap exit = entry (pips 0).

Contoh CSV mini
Pair;Action;Tanggal Entry;Tanggal Exit;Entry;TP;SL;Keterangan
XAUUSD;BUY;2025-01-02;2025-01-02;2388.1;2389.5;2386.0;TP
XAUUSD;SELL;2025-01-03;2025-01-03;2390.3;2388.8;2392.0;TP
XAUUSD;BUY;2025-01-04;2025-01-04;2385.0;2384.1;2387.0;SL


Catatan: Untuk MRG XAU, umumnya 1 pip = 0.1.

Cara Hitung (Penting)
1) Actual Pips (untuk tampilan pips)

Dideteksi otomatis dari jumlah desimal terbesar di kolom Entry/TP/SL.

Rumus per trade:

delta = (exit - entry) untuk BUY

delta = (entry - exit) untuk SELL

pips_actual = round( delta / pip_actual_unit )

Hanya dipakai untuk tampilan total/avg pips & streak pips.

2) Profit (skema MRG)

Menggunakan Pip Size (MRG) dari modal (tidak diubah oleh auto-detect).

Rumus per trade:

pips_mrg = delta / pip_mrg_unit (boleh pecahan)

profit_usd = pips_mrg * (USD per pip per 1 lot) * lot

Total & rata-rata USD, RR, equity, dan streak USD menggunakan angka profit ini.

Kustomisasi

Default Pair ada di bagian PAIR_DEFAULTS pada index.html:

const PAIR_DEFAULTS = {
  XAUUSD: { pipSize: 0.1, usdPerPipLot: 1 }, // MRG (XAU)
  XAGUSD: { pipSize: 0.01, usdPerPipLot: 0.5 },
  EURUSD: { pipSize: 0.0001, usdPerPipLot: 10 },
  GBPUSD: { pipSize: 0.0001, usdPerPipLot: 10 },
  USDJPY: { pipSize: 0.01, usdPerPipLot: 10 },
};


Ubah sesuai kebutuhan broker Anda.

Format uang & angka:

fmtMoney: $1,234.56

fmtNum: 1,234 (pips, tanpa desimal)

Troubleshooting

Total Pips 10× lebih besar
Biasanya karena pakai pip size 0.01 untuk XAU, padahal data/granularitas actual 0.1.
Solusi: actual pips sudah auto-detect. Pastikan data harga konsisten. Untuk profit, pastikan Pip Size (MRG) di modal benar (XAU=0.1).

Profit terasa tidak sesuai
Cek USD per Pip per 1 Lot dan Lot. Contoh XAU MRG lazim:

Pip size: 0.1

USD/pip/lot: 1

Lot: sesuai ukuran Anda (mis. 0.10)

Tanggal tidak terbaca
Gunakan format tanggal standar (YYYY-MM-DD). Jika kolom tanggal kosong, holding period tidak dihitung.

Warna Per Month tidak muncul
Warna dipakai untuk nilai ≠ 0. Bulan tanpa trade tampil “-”.

CSV tidak memuat
Pastikan delimiter ;, header tepat, dan semua angka bisa di-parse (tanpa tanda ribuan/koma desimal yang tidak standar).

Menjalankan di Lokal

Cara termudah: klik 2× index.html (atau seret ke tab browser).

Opsi lain:

VS Code → Live Server plugin.

Host statis (Netlify/GitHub Pages) — cukup unggah index.html.

Tidak perlu Node/Streamlit/backend.

FAQ

Q: Bisa tambah Export CSV untuk tabel streak?
A: Bisa. Bilang saja, nanti saya tambahkan tombol Export di modal streak (USD & Pips).

Q: Bisa filter tanggal?
A: Bisa. Kita tambahkan date-picker dan filter sebelum proses hitung.

Q: Saya pakai pair lain (mis. EURUSD)?
A: Pilih pair di modal → sesuaikan pip size & USD/pip/lot sesuai broker Anda.

Struktur File
index.html   # seluruh kode (HTML + CSS + JS) dalam satu file
README.md    # (file ini)

Catatan Keamanan & Privasi

Semua proses terjadi di browser Anda.

File CSV tidak di-upload ke server mana pun.
