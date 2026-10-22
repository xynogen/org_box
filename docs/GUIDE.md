# Panduan cetak dan perakitan

## 1. Cetak test kunci

Gunakan [sampel badan](../stl/test_snap_lock_base.stl), [sampel poros](../stl/test_snap_lock_lid.stl), dan [kait](../stl/test_snap_lock_latch.stl) di folder `stl/`. Semua memakai geometri yang sama dengan produk, dipotong menjadi sampel kecil. Cetak **100%**, bukan scale-to-fit.

Pengaturan awal untuk PLA dan nozzle 0,4 mm:

- Layer 0,2 mm, 5 perimeter, infill 25%.
- Mulai dari profil PLA produsen yang sudah terkalibrasi; suhu tidak ditetapkan oleh model.
- Gunakan orientasi STL sebagai titik awal. Cek preview layer sebelum mengirim ke printer.
- Poros horizontal pada sampel tutup mungkin perlu support lokal. Bersihkan support tanpa mengikis diameter poros.

Pasang kait ke poros dengan tangan, jangan dipaksa atau dipukul. Pegang kedua potongan dinding sesuai rakitan tertutup lalu kaitkan penguncinya. Sampel tidak punya engsel belakang atau sekrup untuk menyatukan dua potongan dinding.

Periksa:

1. Kait bisa terpasang tanpa retak atau bekas putih.
2. Kait berputar bebas di poros, tetapi tidak mudah terlepas dari mulut clip.
3. Kait menahan kedua sampel ketika ditarik ringan dengan tangan.
4. Bibir bawah bisa ditarik untuk membuka tanpa alat.
5. Ulangi setidaknya 20 kali; hentikan jika muncul retak, kelonggaran berlebih, atau bekas putih. Ini pemeriksaan awal, bukan sertifikasi umur pakai.

Jika terlalu ketat, ubah `SNAP_PIVOT_CLEARANCE`, `SNAP_MOUTH`, atau `CATCH_GAP` di makro utama, lalu regenerasi **model utama dan test**. Jangan scale STL karena poros, pin, dan dimensi alat akan ikut berubah. Cek CAD hanya mengevaluasi geometri dan overlap, bukan tegangan atau deformasi PLA.

## 2. Cetak bagian utama

| Bagian | Orientasi awal | Hal yang harus dicek |
| --- | --- | --- |
| Badan | Dasar di bed | Alur stack bawah, chamfer, akar engsel, catch depan |
| Tutup | Sisi luar menghadap bed | Rib stack menjadi bidang kontak awal; bidang panel bisa memerlukan support |
| Kait | Sisi samping di bed | Lubang poros dan hook tidak tertutup support |
| Tray | Dasar di bed | Pegangan dalam memiliki bidang miring 36,9° dari vertikal |
| Cable clip | Datar di bed | Mulut masuk dan jalur kabel tidak tertutup elephant foot |

**Tutup dengan rib stacking tidak boleh diasumsikan support-free.** Rib 0,8 mm akan membuat panel berada di atas bed jika sisi luar menghadap bawah. Gunakan support lokal yang sesuai, atau ubah orientasi dan cek underside lip/engsel. Uji test kunci dengan orientasi dan kualitas permukaan poros yang mewakili cetakan final.

Badan memiliki alur stack bawah lebar 2,7 mm dan dalam 1 mm. Cek apakah bridging pendeknya rapi pada printer/filamen yang dipakai. Pegangan tray dirancang tumbuh bertahap dari dinding, tetapi keberhasilan overhang tetap bergantung pada pendinginan dan kecepatan.

Tiap komponen muat A1 Mini. Tidak dimaksudkan seluruh rakitan masuk satu plate. Cek brim, skirt, dan zona eksklusi di slicer; orientasi jangan diputar diagonal tanpa memeriksa ulang envelope.

## 3. Rakit organizer

1. Buang support dan burr. Jangan membesarkan lubang poros tanpa uji.
2. Selang-selingkan lima ruas engsel: badan–tutup–badan–tutup–badan.
3. Masukkan filamen Ø1,75 mm ke lubang awal Ø2,1 mm. Mulai dengan potongan sekitar 153 mm dan rapikan sesuai hasil cetak. Pin harus tertahan agar tidak berjalan keluar; jangan melelehkannya saat masih menempel pada badan PLA.
4. Pasang snap-lock ke poros integral di bagian depan tutup, seperti pada test print.
5. Masukkan ketiga tray. Semua berukuran 54 × 108,5 × 43 mm. Rotasi 90° ketika memindah tray dari posisi kiri ke kanan atau sebaliknya.
6. Uji slot alat menggunakan alat asli. Tebal 15 mm dan relief USB samping masih asumsi awal.

Jangan mengangkat kotak melalui kait atau tutup. Kunci bukan alat keamanan dan belum memiliki rating beban.

## 4. Stack dan kabel

Tumpuk hanya dengan tutup tertutup. Rib atas masuk ke alur bawah kotak berikutnya. Ini mencegah geser, bukan mengunci antarkotak. Mulai dengan dua kotak kosong di meja datar; cek rocking dan kecocokan sebelum menambah isi. Jangan menganggap aman untuk tumpukan tinggi.

Cable clip terpisah memegang dua ruas kabel nominal Ø5 mm dengan jalur Ø5,4 mm dan mulut 4,6 mm. Kabel masuk dari samping tanpa melepas konektornya. Jika jacket keras atau diameternya berbeda, sesuaikan parameter dan cetak satu clip dahulu. Jangan paksa kabel listrik atau gunakan clip sebagai strain relief berbeban.

## 5. Struktur kode dan regenerasi

```text
cad/
  organizer_a1mini.FCMacro    # Model, validasi geometri, ekspor FCStd/STL
  test_snap_lock.FCMacro     # Sampel test dari geometri produksi
scripts/
  render_docs.FCMacro        # Render dokumentasi saja; tidak menyimpan perubahan CAD
docs/
  GUIDE.md
  images/                    # Gambar hasil render
stl/                        # Semua STL siap cetak
*.FCStd                     # Model CAD di root
```

Urutan di FreeCAD GUI:

1. Jalankan `cad/organizer_a1mini.FCMacro`.
2. Jalankan `cad/test_snap_lock.FCMacro`.
3. Bila ingin memperbarui README, jalankan `scripts/render_docs.FCMacro`.

Model yang dihasilkan disimpan dalam posisi tertutup. Skrip dokumentasi hanya membuka salinan untuk gambar tutup terbuka/tertutup; kode CAD tidak bergantung pada kode dokumentasi. Output FCStd masuk root proyek, STL masuk `stl/`, bukan current working directory. Simpan edit manual dengan nama lain sebelum regenerasi.

Cek sederhana tanpa FreeCAD:

```sh
python cad/organizer_a1mini.FCMacro --check
```

Makro generator juga menjalankan assert untuk solid, benturan rakitan, semua kombinasi tray/slot, stacking, dan gerak snap-lock. Regenerasi diperlukan untuk menguji geometri setelah perubahan parameter. Ukuran panjang/lebar saling terikat oleh pola tiga tray identik; assert akan menolak kombinasi yang tidak modular.

Tidak ada library pihak ketiga selain modul bawaan FreeCAD. Jangan menambahkan print split atau sambungan M3 sebelum perlu: model sekarang sudah muat A1 Mini.
