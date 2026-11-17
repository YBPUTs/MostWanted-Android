# Changelog

Format: tanggal, versi mod, perubahan. Dasar: RanggaCasper/MostWanted-Android v1.4.0 (GPL-3.0).

## 2026-11-18 — v1.4.0-modmenu (varian Z, rilis final)

Satu varian berisi semua patch: `python apply_patch.py libmain.so hasil.so --varian Z` (garasi 25 sudah tertanam).

### Ditambahkan
- Tab **SETTING** (PS2 Controls, DUMP Memory, Github, Telegram) dan tab **DEBUG** di MOD MENU.
- Github dan Telegram: tombol OPEN membuka tautan lewat `SDL_OpenURL`.
- Tab DEBUG menampilkan pesan "Only permission from YBPUT if you want to open Debug".
- Custom Heat 1–5 berlaku di world map dan pengejaran, termasuk di atas batas heat career.

### Diperbaiki
- Layar pillarbox walau Aspect Ratio = Full Screen (cache rasio di WidenAspect/ScreenAspect selalu dihitung ulang).
- Custom Heat hanya bekerja di Car Select; level 5 loop 4/5 saat pengejaran; layar putih saat mengganti level.
- PS2 Controls tidak aktif saat berkendara dan pemetaan tidak cocok (kini sesuai Control Type 1).
- Custom Heat level 6 dihapus.

### Catatan teknis
- Rincian per build percobaan (R sampai AC) ada di bagian bawah file ini.

## 2026-11-15 — v1.4.0-junkman4 (varian H)

### Diubah
- Baris CHEATS "NO SPIKE STRIPS" diganti nama menjadi **IMMUNE TO SPIKES**.
- Teks di menu MOD: "Port by Rangga Casper | Modded by YBPUT" (karakter `|` ASCII, karena font menu belum tentu punya glyph U+2503).

### Ditambahkan
- Baris CHEATS **PS2 CONTROLS** (bawaan OFF). Saat ON dan sedang berkendara (state game: mode 1 dan objek pemain ada), tombol gamepad dipetakan ala PS2: L1 = Nitrous, R1 = E-brake, R2 = Shift Up, L2 = Shift Down, Segitiga = Look Back, Lingkaran = Speedbreaker, R3 = Change Camera, Silang = gas, Kotak = rem/mundur (lewat sumbu stik kanan). Select/Start tidak berubah. Dipasang di hook XInputGetState (setelah `input::Poll`). Belum diuji di perangkat; deteksi "sedang berkendara" masih tebakan.

## 2026-11-15 — v1.4.0-junkman3c (varian H, perbaikan baris menu)

### Diperbaiki
- Baris ke-8 **NO SPIKE STRIPS** tidak muncul di tab CHEATS. Penyebab: `app::DrawUi` menggambar tab CHEATS dengan loop tetap 7 baris dari tabel asli (bukan lewat `rows::Page`). Sekarang loop 8 baris dan tabelnya disalin ke penyangga bss + baris ke-8 (cave D). Patch lama di `rows::Page` dihapus.

## 2026-11-15 — v1.4.0-junkman3b (varian H, perbaikan)

### Diperbaiki
- Nilai baris menu (ON/OFF, dll.) hilang di halaman Graphics dan CHEATS pada varian H awal. Penyebab: cabang bersyarat di fungsi `app::rows::Value` (0x8E9B9C) saya ganti dengan lompatan tanpa syarat ke cave, sehingga semua id baris melewati tabel lompatan nilai. Sekarang perbandingan `cmp w8,#0x29` (0x8E9B98) dialihkan ke cave yang hanya menangani id 44 dan mengembalikan id lain ke jalur asli.

## 2026-11-14 — v1.4.0-junkman3 (varian H)

### Ditambahkan
- Baris baru **NO SPIKE STRIPS** di halaman CHEATS menu bawaan APK: kebal paku ranjau polisi (ban tidak bisa dikempeskan). Bawaan mati; tidak disimpan antar sesi.

### Teknis
- Fungsi PunctureTire (x86 0x1EDEE0) dicabangkan ke kode kecil yang membaca byte `Opt()+0x39`; halaman CHEATS menjadi 8 baris lewat penyangga di akhir bss. Kode tambahan ditulis di fungsi mati `ImGui::ShowMetricsWindow`. Varian H awal punya bug nilai menu hilang (diperbaiki 2026-11-15).

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

## 2026-11-15 — varian L (pencatat Heat, diagnostik)
- `--varian L` = varian H + 5 kait yang menulis baris `HEATLOG ...` ke log game (SetWorldHeat, setter 0x16110, getter 0x336c0 saat nilai berubah, MinHeatLevel, 0x26ab0). Hanya untuk melacak variabel Heat; belum mengubah Heat.
- L2: tambah log PADLOG (status in-game untuk PS2 CONTROLS) di varian L.
- L3: varian L menulis guestdump.bin (memori game) saat PS2 CONTROLS pertama kali ON, untuk mencari alamat heat per mobil.
- L4: dump heap guest 0x80000000-0x84000000 ke guestdump2.bin (halaman nol dilewati).

## 2026-11-15 — varian N (Custom Heat)
- `--varian N` = varian H + baris CHEATS **CUSTOM HEAT** (OFF, 1–6). Saat bukan OFF, tiap frame menulis float N.0 ke heat 24 slot mobil (tabel record mobil di heap, ditemukan lewat pemindaian berpola + validasi; stride 0x38). Heat dicari dari dump memori nyata (guestdump2), bukan tebakan.
- Catatan: nilai Opt (Immune to Spikes, PS2 Controls, Custom Heat) belum tersimpan antar sesi.

## 2026-11-15 — varian O
- `--varian O` = N + panah kiri (<) CUSTOM HEAT berfungsi (arah dibaca dari w21 = +1/-1) + toggle MOD (Immune to Spikes, PS2 Controls, Custom Heat) disimpan di `MostWanted/ybput_mod.bin` + baris **DUMP MEMORI** (tulis `dump_0..9.bin`: heap 0x80000000–0x84000000 di offset 0, memori statis 0x10000–0x520000 di offset 0x4000000).

## 2026-11-15 — varian P
- `--varian P` = O + Custom Heat juga menulis heat pengejaran: objek perp ditemukan lewat pola `[heat float][-1.0][0xAAAAAAAA]` (ditemukan unik di 3 dump), dua float (level & heat berjalan) diisi N tiap frame, tanpa menunggu pengejaran.
- Temuan: angka HUD kiri/tengah/kanan (cops damaged/involved/roadblock) hanyalah penghitung statistik di heap (~0x8296c948), bukan kontrol jumlah polisi.

## 2026-11-15 — varian Q
- `--varian Q` = P + tab **SETTING** di sebelah MY MUSIC (byte tab Opt+0x471: 0 CHEATS, 1 MY MUSIC, 2 SETTING). Isinya: "PS2 Controls - Gamepad Custom YBPUT only" dan "DUMP Memory - Danger don't Click this" (dipindah dari CHEATS). CHEATS kini: 7 bawaan + Immune to Spikes + Custom Heat. Batas baris loop DrawUi kini dibaca dari variabel (scratch+0x78).

## Varian R (libmain_R_layar_ps2_fix.so) = Q +
- Layar pillarbox: cache rasio layar (WidenAspect 0x909878, ScreenAspect 0x9504ac) kini selalu dihitung ulang dari ukuran jendela (dump menunjukkan cache basi 800/480 = 1.667 vs 1066/480 = 2.222).
- PS2 Controls: syarat aktif diganti: `game::Racing()` (guest 0x4936b8 == 6) DAN `input::SetPadMenuMode` == 0 (byte 0x1d17950, artinya tidak ada menu FE/pause). Pemetaan tetap: L1 Nitrous, R1 E-brake, R2 Shift Up, L2 Shift Down, Segitiga Look Back, Lingkaran Speedbreaker, R3 Change Camera, Silang gas, Kotak rem (lewat sumbu kanan; stik kanan asli tetap berfungsi), Select Reset Car, Start Pause.
- Custom Heat di dunia/pengejaran: belum diperbaiki.

## Varian S (libmain_S_heat_dunia.so) = R +
- Logika Custom Heat kini juga dipanggil tiap frame lewat hook di 0x8f112c (sebelum `app::PaceFrame`), bukan hanya saat game membaca gamepad (dump_B: tabel mobil sudah 5.0 tetapi objek pengejaran di dunia tetap 3.0, artinya hook lama tidak jalan saat berkendara).
- Custom Heat level 6 dihapus (OFF, 1-5); nilai 6 tersimpan otomatis dipotong ke 5.

## Varian T (libmain_T_heat_dunia2.so) = S +
- Menulis juga objek heat pengejaran berkelas vtable 0x3da440 (float kontinu di +0x10; dump: 2.149 -> 3.042 -> 3.093 selama pengejaran, 3.0 tak berubah di dump_B). Menulis log `YBPUT heatV found <alamat>` bila objek ditemukan.

## Varian U (libmain_U_heat_dirty_ps2log.so) = T +
- Penulisan heat utama (vtable 0x3da440, +0x10) kini meniru setter game: bila nilai berubah, byte "dirty" di +8 diisi 1 supaya HUD/objek lain diberi tahu (dump_1: tabel mobil dan objek utama = 5.0 tetapi level pengejaran dan ikon tetap 1).
- Syarat PS2 dipindah ke fungsi gateP dan mencatat log `YBPUT ps2 gate pm|state<<8=XXXX` tiap kali berubah (diagnostik).

## Varian V (libmain_V_ps2_tipe1.so) = U +
- PS2 Controls disesuaikan dengan "Control Type 1" bawaan game (L=rem, R=gas, A=E-brake, B=Nitrous, X=Speedbreaker, Y=Look Back, stik kanan = gigi). Pemetaan (fungsi mapP): Silang = gas, Kotak = rem/mundur, L1 = Nitrous, R1 = E-brake, Lingkaran = Speedbreaker, Segitiga = Look Back, R2 = gigi naik, L2 = gigi turun, R3 = ganti kamera, stik kanan atas = gas, bawah = rem. Select = Reset Car, Start = Pause.

## Varian W (libmain_W_heat_resmi_link.so) = V +
- Custom Heat di world map/pengejaran kini lewat jalur resmi game: memanggil handler `SetWorldHeat` (guest 0x190cf0, host `__imp__sub_00190CF0`) dengan konteks X86 dari `sub_003768B0` (register guest disimpan & dikembalikan). Dipanggil maksimal tiap 30 frame bila level heat (objek manajer [0x480b30]+0x1ec) berbeda dari pilihan. Log: `YBPUT SetWorldHeat N (level sebelumnya M)`.
- Penulisan langsung ke objek heat (vtable 0x3da440) dan objek level dihentikan; tabel heat mobil hanya ditulis saat tidak sedang di world (garasi/Car Select).
- Tab SETTING: baris "Github" dan "Telegram" (nilai OPEN), panah kiri/kanan membuka tautan lewat `SDL_OpenURL`.

## Varian X (libmain_X_heat5_kunci.so) = W +
- Heat dikunci di setter heat milik pemain: `SetHeat` guest 0x2050b0 (host `__imp__sub_002050B0` 0x1200ebc dan salinannya `sub_002050B0` 0x1200ef0). Bila objek ber-vtable 0x3cbae8 (IPerpetrator pemain) dan Custom Heat aktif, nilai yang disimpan selalu = level pilihan. Mencegah game menurunkan heat 5 -> 4 saat pengejaran (loop 4/5/4/5 di W).

## Varian Y (libmain_Y_heat5_iface.so) = W + (pengganti X)
- Kunci heat berdasarkan interface pemain yang sebenarnya: setelah memanggil SetWorldHeat, pointer `ecx` (interface heat pemain) disimpan (scratch+0x44). Setter float generik +0x3c (0x1200ebc/0x1200ef0) dan +0x40 (`sub_000310F0` 0xa32b44/0xa32b78) memaksa nilai = Custom Heat bila `this` sama dengan interface itu.
- Log diperluas: `YBPUT SetWorldHeat N (lvl M) ecx=.. vt=.. get=.. set=..`.
- Efek transisi level heat (`sub_00248760`, dipanggil dari SetHeatLevel 0x1353934) dilewati selama Custom Heat aktif (percobaan perbaikan layar putih).

## Varian Z (libmain_Z_heat_debug.so) = Y + diagnostik di layar
- Tab SETTING menambah 3 baris: "DBG Heat" (L = level heat x100, H = heat interface x100, N = jumlah panggilan SetWorldHeat, W = jumlah tulisan heat yang dipaksa), "DBG Iface" (I = interface heat pemain, V = vtable), "DBG Func" (G/S = alamat guest getter/setter heat).

## Varian AA (libmain_AA_heat_kunci2.so) = Z +
- Debug Z menunjukkan penangkapan interface heat gagal (I=0): fungsi SetHeat asli mengubah ecx. AA mencari interface heat pemain sendiri tiap frame: daftar pemain [0x495ea4] -> getter vtable+4 (didekode: mov/lea [ecx+K]) -> objek COM -> daftar interface, cari id 0x11c20.
- Selama world/pengejaran dan Custom Heat aktif, nilai heat ditulis langsung ke interface+K (K didekode dari getter `fld [ecx+K]`) setiap frame, sehingga game tidak bisa menurunkannya ke batas career.
- DBG Iface: bila interface tidak ketemu, menampilkan F (4 byte kode getter pemain) dan K.

## Varian AB (libmain_AB_heat_kunci3.so) = AA +
- DBG AA menunjukkan getter pemain vtable+4 berupa thunk `sub ecx,0x20; jmp` (F=E920E983). AB mengikuti thunk (`sub ecx,imm8; jmp` dan `jmp`) sebelum mendekode getter, baik untuk objek pemain maupun getter heat.

## Varian AC (libmain_AC_heat_setter_debugtab.so) = AB +
- DBG AB: interface heat pemain I=829AC380, vtable 0x3cc568, getter 0x336c0 (`fld [ecx+0x1c]`), setter 0x16110 (fungsi kompleks). Setter itu kini di-hook di awal (`__imp__sub_00016110` 0x9b7d8c): bila `this` = interface pemain dan Custom Heat aktif, argumen heat di stack guest diganti level pilihan sebelum fungsi berjalan.
- Tab baru "DEBUG" (byte tab = 4) di kanan SETTING; baris DBG dipindah ke sana. SETTING kembali berisi PS2, DUMP, Github, Telegram.
