# MEMORY & PANDUAN BELAJAR FUNDAMENTAL JAVASCRIPT (MODUL 2)

Dokumen ini adalah **single source of truth (sumber acuan utama)** yang merangkum seluruh perencanaan, silabus, aturan sistem belajar, progres saat ini, dan panduan untuk melanjutkan sesi jika berpindah *conversation* chat baru.

---

## 1. Profil & Tujuan Utama (The Goal)
* **Pengguna**: Mahasiswa Teknologi Informasi Semester 5 (sudah menguasai HTML & CSS, sedang belajar fundamental JavaScript).
* **Target Waktu**: 2 Hari @ 7 Jam per hari (**Total 14 Jam** belajar aktif).
* **Fokus Materi**: JavaScript murni modern (*Vanilla JS ES6+*). **TypeScript sepenuhnya dihiraukan/diabaikan**.
* **Sumber Rujukan**: 
  - Materi praktikum kampus: `modul-2/README.md` (100% materi JS bagian 1-4 telah diintegrasikan).
  - W3Schools, freeCodeCamp, dan Dicoding (standar web modern).

---

## 2. Struktur Silabus To-The-Point (6 Bab)

### HARI 1: Logika Bahasa & Pengelolaan Data (7 Jam)
* **Bab 01: Sintaksis Dasar, Variabel, & Tipe Data (2 Jam)**
  - Sub 01: Environment, Menghubungkan JS ke HTML (Inline, Internal, External + `defer`), Output Dasar (`console`, dialog, DOM).
  - Sub 02: Variabel Modern (`let` vs `const` vs `var`) & Aturan Scope Blok `{}`.
  - Sub 03: Tipe Data Primitif, Template Literals, Operator Aritmatika, Perbandingan Ketat (`===` vs `==`), & Logika.
  - *Output Proyek*: Kalkulator Diskon Belanja Interaktif.
* **Bab 02: Percabangan, Looping, & Fungsi (2.5 Jam)**
  - Sub 01: Percabangan (`if`, `else if`, `else`, Ternary Operator `? :`).
  - Sub 02: Fungsi Modern (Declaration vs Arrow `() => {}`, Parameter, Return Value).
  - Sub 03: Perulangan Praktis (`for` dan `while`).
  - *Output Proyek*: Sistem Penentu Grade & Predikat Akademik Mahasiswa.
* **Bab 03: Mengelola Data: Array & Object (2.5 Jam)**
  - Sub 01: Array & Manipulasi Data (`push`, `pop`, `splice`, `indexOf`, `includes`, `forEach`).
  - Sub 02: Object Literals (Key-value, Dot vs Bracket, Array of Objects).
  - Sub 03: Method Array Modern (`map` dan `filter`).
  - *Output Proyek (Capstone Hari 1)*: Manajemen Data Inventaris / Nilai Kelas (Search & Filter).

---

### HARI 2: Interaktivitas Web Dinamis (DOM, Storage, & API) (7 Jam)
* **Bab 04: DOM Manipulation & Dinamika Halaman (2.5 Jam)**
  - Sub 01: Seleksi Elemen Web (`getElementById`, `querySelector`, `querySelectorAll`).
  - Sub 02: Mengubah Konten & Gaya (`textContent`, `innerHTML`, `style`, `classList`).
  - Sub 03: Membuat & Menghapus Elemen Dinamis (`createElement`, `append`, `remove`).
  - *Output Proyek*: Dynamic Card Generator.
* **Bab 05: Event Handling & Web Storage (2.5 Jam)**
  - Sub 01: Event Listener (`click`, `input`, `submit`, `event.preventDefault()`).
  - Sub 02: Event Delegation (menangani event pada elemen dinamis).
  - Sub 03: Web Storage (`localStorage` CRUD + `JSON.stringify`/`JSON.parse`).
  - *Output Proyek*: Persistent To-Do List App (data tidak hilang saat refresh).
* **Bab 06: Asynchronous JS & Fetch API Publik (2 Jam)**
  - Sub 01: Konsep Synchronous vs Asynchronous.
  - Sub 02: Modern `async` / `await`, `fetch()` API, & `try...catch`.
  - Sub 03: Rendering Data API ke Antarmuka DOM.
  - *Output Proyek (Final Submission)*: Mini Web Dashboard (Fetch Public API + Filter + Render UI).

---

## 3. Aturan & Standar Penulisan Berkas (Rules of Engagement)

1. **Struktur Folder di `modul-2/`**:
   - Format folder bab: `modul-2/Bab_xx_[Nama_Bab]/`
   - File materi teori: `Sub_xx_[Nama_Sub_Bab].md`
   - File praktik kumulatif: `Praktik_Bab_xx.html`
   - File proyek: `Tantangan_Proyek_Bab_xx.md` dan `Template_Proyek_Bab_xx.html`
2. **Aturan CSS (Sangat Penting)**:
   - **JANGAN buat CSS kompleks atau rumit**. Gunakan CSS sederhana/minimalis hanya sebagai pendukung kerapian (margin, padding, border dasar).
   - Tujuannya agar mahasiswa tidak bingung dan bisa 100% fokus memahami alur logika JavaScript.
3. **Aturan Penempatan `<script>` di File Praktik**:
   - Gunakan pola **kolokasi**: Setiap blok materi HTML harus langsung diikuti oleh tag `<script>` tepat di bawahnya.
   - Jangan tumpuk seluruh script di bagian paling bawah file agar mahasiswa tidak perlu scroll naik-turun.
4. **Alur Belajar Sub-Bab**:
   - Buat materi 1 sub-bab saja dalam satu waktu.
   - Update file praktik kumulatif HTML bab tersebut.
   - Tanyakan: *"Apakah Anda sudah paham materi ini?"*.
   - Hanya lanjut ke sub-bab berikutnya jika pengguna mengonfirmasi sudah paham.
5. **ATURAN MUTLAK SOAL PROYEK (STRICT NO-HINT)**:
   - Di akhir setiap bab, sediakan soal proyek beserta template HTML-nya.
   - **DILARANG KERAS memberikan hint, bocoran, atau snippet solusi kecuali jika pengguna memintanya secara eksplisit**.
6. **Prinsip Pembuktian Kode Sederhana (Direct & To-The-Point Proof)**:
   - Untuk materi dasar sintaksis, variabel, tipe data, logika, dan perulangan, **jangan gunakan event listener atau tombol-tombol UI yang rumit**.
   - Cukup eksekusi langsung kodenya saat halaman dibuka, buktikan melalui `console.log()`, dan jika perlu tampilkan nilainya secara sederhana ke elemen `<p>`.
   - Tujuannya agar mahasiswa bisa 100% fokus memahami alur algoritma dan perilaku kodenya tanpa terdistraksi seremonial DOM/UI.
7. **Aturan Komentar Minimalis**:
   - **Jangan gunakan terlalu banyak komentar** `<!-- -->` atau `//` yang mengulang hal jelas atau mengulang teks heading.
   - Komentar hanya diberikan **jika ada informasi krusial/penting** (seperti penyebab error, catatan perbedaan perilaku, atau trik JavaScript penting). Aturan ini berlaku untuk seluruh file praktik ke depan.

---

## 4. Status Progres Pembelajaran

- [x] **Perencanaan & Setup Kurikulum 14 Jam** (PRD Approved)
- [x] **Bab 01 - Sub-Bab 01**: Environment, Menghubungkan JS ke HTML, & Output Dasar (`Sub_01_Environment_dan_Output.md` & `Praktik_Bab_01.html` Bagian 1 Selesai)
- [x] **Bab 01 - Sub-Bab 02**: Variabel Modern (`let`, `const`, `var`) & Scope Blok `{}` (`Sub_02_Variabel_dan_Scope.md` & `Praktik_Bab_01.html` Bagian 2 Selesai)
- [x] **Bab 01 - Sub-Bab 03**: Tipe Data Primitif, Template Literals, & Operator (`Sub_03_Tipe_Data_dan_Operator.md` & `Praktik_Bab_01.html` Bagian 3 Selesai)
- [x] **Bab 01 - Proyek**: Kalkulator Diskon Belanja Interaktif (LULUS & SELESAI SEMPURNA)
- [x] **Bab 02 - Sub-Bab 01**: Percabangan Logika (`if`, `else if`, `else`, & Ternary Operator) (`Sub_01_Percabangan_Logika.md` & `Praktik_Bab_02.html` Bagian 1 Selesai)
- [x] **Bab 02 - Sub-Bab 02**: Fungsi Modern (Declaration vs Arrow Function, Parameter, Return) (`Sub_02_Fungsi_Modern.md` & `Praktik_Bab_02.html` Bagian 2 Selesai)
- [x] **Bab 02 - Sub-Bab 03**: Perulangan Praktis (`for`, `while`) (`Sub_03_Perulangan_Praktis.md` & `Praktik_Bab_02.html` Bagian 3 Selesai)
- [x] **Bab 03**: Struktur Data: Array & Object (Teori Selesai 100%)
  - [x] **Sub-Bab 01**: Array & Manipulasi Data Dasar (`Sub_01_Array_dan_Manipulasi_Data.md` & `Praktik_Bab_03.html` Bagian 1 Selesai)
  - [x] **Sub-Bab 02**: Object Literals & Array of Objects (`Sub_02_Object_Literals.md` & `Praktik_Bab_03.html` Bagian 2 Selesai)
  - [x] **Sub-Bab 03**: Method Array Modern (`map` & `filter`) (`Sub_03_Method_Array_Modern.md` & `Praktik_Bab_03.html` Bagian 3 Selesai)
  - [ ] **Proyek Evaluasi Hari 1 (PR TERTUNDA)**: Manajemen Data Inventaris (`Tantangan_Proyek_Bab_03.md` & `Template_Proyek_Bab_03.html` Siap — *Menunggu Aba-aba User*)
- [x] **Bab 04**: DOM Manipulation & Dinamika Halaman (Teori Selesai 100%)
  - [x] **Sub-Bab 01**: Seleksi Elemen Web (`getElementById`, `querySelector`, `querySelectorAll`) (`Sub_01_Seleksi_Elemen.md` & `Praktik_Bab_04.html` Bagian 1 Selesai)
  - [x] **Sub-Bab 02**: Mengubah Konten & Gaya (`textContent`, `innerHTML`, `style`, `classList`) (`Sub_02_Ubah_Konten_dan_Gaya.md` & `Praktik_Bab_04.html` Bagian 2 Selesai)
  - [x] **Sub-Bab 03**: Membuat & Menghapus Elemen Dinamis (`createElement`, `append`, `remove`) (`Sub_03_Buat_dan_Hapus_Elemen.md` & `Praktik_Bab_04.html` Bagian 3 Selesai)
  - [ ] **Proyek Bab 04 (PR TERTUNDA)**: Dynamic Card Generator (`Tantangan_Proyek_Bab_04.md` & `Template_Proyek_Bab_04.html` Siap — *Menunggu Aba-aba User*)
- [x] **Bab 05**: Event Handling & Web Storage (Teori Selesai 100%)
  - [x] **Sub-Bab 01**: Event Listener (`click`, `input`, `submit`, `event.preventDefault()`) (`Sub_01_Event_Listener.md` & `Praktik_Bab_05.html` Bagian 1 Selesai)
  - [x] **Sub-Bab 02**: Event Delegation (Menangani Event pada Elemen Dinamis) (`Sub_02_Event_Delegation.md` & `Praktik_Bab_05.html` Bagian 2 Selesai)
  - [x] **Sub-Bab 03**: Web Storage (`localStorage` CRUD + `JSON.stringify`/`JSON.parse`) (`Sub_03_Web_Storage.md` & `Praktik_Bab_05.html` Bagian 3 Selesai)
  - [ ] **Proyek Bab 05 (PR TERTUNDA)**: Persistent To-Do List App (`Tantangan_Proyek_Bab_05.md` & `Template_Proyek_Bab_05.html` Siap — *Menunggu Aba-aba User*)
- [x] **Bab 06**: Asynchronous JS & Fetch API (Teori & Praktik Selesai 100%)
  - [x] **Sub-Bab 01**: Konsep Synchronous vs Asynchronous (`Sub_01_Konsep_Async.md` & `Praktik_Bab_06.html` Bagian 1 Selesai)
  - [x] **Sub-Bab 02**: Modern `async` / `await`, `fetch()`, & `try...catch` (`Sub_02_Async_Await_dan_Fetch.md` & `Praktik_Bab_06.html` Bagian 2 Selesai)
  - [x] **Sub-Bab 03**: Rendering Data API ke Antarmuka DOM (`Sub_03_Render_Data_API.md` & `Praktik_Bab_06.html` Bagian 3 Selesai)
  - [ ] **Proyek Akhir (Final Capstone Day 2)**: Mini Web Dashboard (Fetch Public API + Filter + Render UI)

---

## 5. Template Prompt untuk Memulai Conversation Baru
Jika Anda memulai percakapan baru di masa depan, Anda cukup menyalin pesan berikut ke prompt:

```text
Halo! Saya ingin melanjutkan sesi belajar JavaScript fundamental di repo ini.
Tolong baca file panduan dan status progres kita di:
modul-2/MEMORY.md

Kita sedang belajar dengan metode:
1. Satu per satu sub-bab.
2. File praktik HTML kumulatif dengan CSS sederhana dan script tepat di bawah elemen HTML-nya.
3. Tanyakan pemahaman saya sebelum lanjut.
4. Di akhir bab ada proyek mandiri TANPA hint kecuali saya minta.

Berdasarkan checklist di MEMORY.md, silakan lanjutkan materi berikutnya!
```
