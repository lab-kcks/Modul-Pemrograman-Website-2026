# Tantangan Proyek Bab 02: Sistem Penentu Grade & Evaluasi Nilai Akademik

Selamat telah menyelesaikan materi Bab 02! Di proyek kedua ini, Anda akan menggabungkan materi inti: **Percabangan Logika**, **Fungsi Modern (Arrow Function / Function Declaration)**, dan **Ternary Operator**.

---

## 📌 Deskripsi Kasus
Fakultas Teknologi Informasi membutuhkan sistem web sederhana untuk membantu dosen mengolah nilai mahasiswa secara otomatis. Dosen akan menginput **Nama Mahasiswa**, **Nilai Tugas**, **Nilai UTS**, dan **Nilai UAS** (skala 0 - 100).

Tugas Anda adalah menulis fungsi-fungsi JavaScript modular di dalam [Template_Proyek_Bab_02.html](Template_Proyek_Bab_02.html) untuk memproses nilai tersebut.

---

## 🎯 Spesifikasi & Aturan Pengerjaan

Gunakan konsep-konsep dari Bab 02:

### 1. Validasi Input
* Pastikan kolom **Nama Mahasiswa** tidak kosong (`.trim() === ""`).
* Semua nilai (**Tugas**, **UTS**, **UAS**) harus berupa angka valid (bukan teks kosong atau `NaN`), dan berada di rentang **0 sampai 100**.
* Jika salah satu field tidak valid, hentikan proses dan tampilkan pesan peringatan di layar.

### 2. Fungsi Modular (Wajib Menggunakan Function / Arrow Function)
Pecah logika program menjadi fungsi-fungsi kecil terpisah:
* **Fungsi 1: `hitungNilaiAkhir(tugas, uts, uas)`**
  - Bobot penilaian: Tugas 30%, UTS 35%, UAS 35%.
  - Rumus: `(tugas * 0.3) + (uts * 0.35) + (uas * 0.35)`
  - Mengembalikan (*return*) angka nilai akhir.
* **Fungsi 2: `tentukanGrade(nilaiAkhir)`**
  - Menggunakan percabangan `if`, `else if`, `else`:
    - `>= 85` : Grade **A**
    - `>= 75` : Grade **B**
    - `>= 60` : Grade **C**
    - `< 60`  : Grade **D**
  - Mengembalikan teks Grade.
* **Fungsi 3: Evaluasi Kelulusan**
  - Menggunakan **Ternary Operator (`? :`)**:
    - Jika nilai akhir `>= 60`, hasil: `"Lulus"`.
    - Jika `< 60`, hasil: `"Remedial"`.

### 3. Tampilan Hasil
* Tampilkan hasil evaluasi ke elemen `#hasil-evaluasi` menggunakan format rapi (Nama, Nilai Akhir, Grade, Status Kelulusan).
* Pastikan kotak hasil muncul di layar (`display: block`) dan catat pula ringkasannya ke `console.log`.

---

## 📋 Checklist Kriteria Kelulusan Proyek (Buat pembantu ajah)
- [ ] Terdapat validasi input (nama terisi, input berupa angka valid rentang 0 - 100).
- [ ] Logika dibuat terpisah menjadi fungsi-fungsi modular (memiliki parameter dan `return` yang jelas).
- [ ] Perhitungan bobot nilai akhir akurat (30%, 35%, 35%).
- [ ] Penentuan Grade A/B/C/D menggunakan `if-else` bekerja sesuai rentang nilai.
- [ ] Status kelulusan ditentukan menggunakan **Ternary Operator**.
- [ ] Hasil evaluasi berhasil ditampilkan ke layar web dan tercatat di `console.log`.

---

## ⚠️ Aturan Penting (Strict No-Hint Rule):
Kerjakan secara mandiri/kelompok di berkas [Template_Proyek_Bab_02.html](Template_Proyek_Bab_02.html).  

