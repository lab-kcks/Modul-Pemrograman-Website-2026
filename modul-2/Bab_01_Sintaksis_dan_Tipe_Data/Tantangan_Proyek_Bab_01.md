# Tantangan Proyek Bab 01: Kalkulator Diskon Belanja Interaktif

Selamat! Anda telah menyelesaikan seluruh materi teori dasar di Bab 01. Sekarang saatnya menguji pemahaman logika JavaScript Anda secara mandiri/kelompok melalui proyek mini pertama ini.

---

## 📌 Deskripsi Kasus
Sebuah minimarket kampus ingin membuat kalkulator kasir sederhana di halaman web. Kasir akan memasukkan **Harga Asli Barang** (dalam Rupiah) dan **Persentase Diskon** (dalam persen, misal `10` untuk 10%). 

Tugas Anda adalah menulis logika JavaScript di dalam berkas [Template_Proyek_Bab_01.html](Template_Proyek_Bab_01.html) agar sistem dapat menghitung dan menampilkan rincian pembayaran secara otomatis.

---

## 🎯 Spesifikasi Pengerjaan

Gunakan seluruh konsep yang telah dipelajari di Bab 01:
1. **Variabel Modern**:
   - Gunakan `const` untuk elemen DOM dan nilai yang tidak berubah.
   - Gunakan `let` untuk nilai yang berubah/dihitung.
   - **DILARANG** menggunakan `var`.
2. **Konversi Tipe Data**:
   - Input dari HTML bertipe `string`. Pastikan Anda mengonversinya ke `number` menggunakan `Number()` sebelum melakukan kalkulasi.
3. **Kalkulasi Aritmatika**:
   - Rumus Potongan Diskon: `(hargaAsli * persenDiskon) / 100`
   - Rumus Total Bayar: `hargaAsli - potonganDiskon`
4. **Validasi Sederhana**:
   - Jika harga barang kosong atau bernilai `<= 0`, tampilkan pesan peringatan error bahwa harga tidak valid.
5. **Template Literals**:
   - Format rincian hasil struk menggunakan backtick ( `` ` `` ) dan `${...}`.
6. **Output**:
   - Tampilkan rincian struk ke elemen `<div id="hasil-struk">` di halaman web.
   - Cetak juga rincian objek transaksi ke `console.log()`.

---

## 📋 Checklist Kriteria Kelulusan Proyek (Buat Pembantu ajah)
- [ ] Tombol **"Hitung Pembayaran"** merespons saat diklik.
- [ ] Input teks berhasil dikonversi ke tipe data angka (`number`).
- [ ] Perhitungan potongan diskon dan total bayar akurat secara matematis.
- [ ] Muncul peringatan jika harga diisi `0` atau angka negatif.
- [ ] Rincian teks ditampilkan menggunakan **Template Literals**.
- [ ] Output transaksi tercatat di console browser (`F12`).

---

Kerjakan secara mandiri/kelompok di berkas [Template_Proyek_Bab_01.html](Template_Proyek_Bab_01.html).  
