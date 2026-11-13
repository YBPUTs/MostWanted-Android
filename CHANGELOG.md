# Changelog

Format: tanggal, versi mod, perubahan. Dasar: RanggaCasper/MostWanted-Android v1.4.0 (GPL-3.0).

## 2026-11-14 — v1.4.0-junkman2 (varian F)

### Ditambahkan
- Garasi Safe House: batas beli mobil naik dari 15 ke 25 mobil total (batas nyata save).
- Opsi `--garasi N` di `apply_patch.py` (1-25, bawaan 25).

### Diperbaiki
- Crash (freeze lalu force close) saat memilih marker "Bonus Markers" (pink slip mobil rival) ketika garasi sudah penuh 25 mobil. Penyebab: fungsi penambah mobil mengembalikan null saat tabel slot part penuh, dan kode pemberi hadiah memakainya tanpa pengecekan. Sekarang mobil hadiah dilewati tanpa crash.
- Pembelian mobil saat garasi penuh kini diblokir dengan pesan "no more room" (menghitung total mobil, termasuk mobil pink slip).

### Diketahui
- Garasi lebih dari 25 tidak bisa: tabel slot part di save hanya 25 entri dan langsung diikuti data lain.
- Pada garasi penuh, mobil pink slip tidak masuk; jual satu mobil lebih dulu.

## 2026-11-11 — v1.4.0-junkman (varian D)
- Pilihan marker setelah mengalahkan Blacklist: 2 -> 6.
- Junkman tidak habis dan tidak berkurang saat dipakai.
- Gerbang Backroom dibuka: semua kategori tampil.
