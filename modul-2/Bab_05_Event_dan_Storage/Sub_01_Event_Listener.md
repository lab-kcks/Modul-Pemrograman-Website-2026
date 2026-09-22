# Sub-Bab 01: Event Listener & Penanganan Form (`click`, `input`, `submit`)

Setelah bisa memanipulasi elemen DOM di Bab 04, sekarang kita belajar bagaimana merespons setiap tindakan dan aksi pengguna secara interaktif melalui **Event Handling**.

---

## 1. Apa Itu Event?

**Event** adalah sinyal atau kejadian yang dipicu saat sesuatu terjadi pada halaman web, baik oleh tindakan pengguna (seperti mengklik mouse, mengetik di keyboard) maupun oleh browser (seperti halaman selesai dimuat).

Cara modern dan standar untuk menangani event di JavaScript adalah method **`.addEventListener()`**:

```javascript
element.addEventListener("namaEvent", function(event) {
  // Kode yang akan dijalankan ketika event terjadi
});
```
* **Argumen 1**: Nama event dalam bentuk string (misal: `"click"`, `"input"`, `"submit"`). *Catatan: Jangan tambahkan awalan 'on' (bukan 'onclick').*
* **Argumen 2**: Fungsi yang akan dieksekusi (*callback function*).
* **Parameter `event` (atau `e`)**: Objek bawaan yang otomatis dikirim oleh browser, berisi detail tentang kejadian tersebut.

---

## 2. Event-Event Utama yang Paling Sering Digunakan

### A. Event `'click'`
Dipicu ketika pengguna mengklik elemen (biasanya tombol, kartu, atau ikon):
```javascript
const tombol = document.querySelector("#btn-simpan");

tombol.addEventListener("click", (e) => {
  console.log("Tombol diklik!");
  console.log("Elemen target:", e.target); // Elemen yang diklik
});
```

---

### B. Event `'input'` (Real-time Live Typing)
Dipicu **secara langsung setiap kali karakter berubah** di dalam `<input>` atau `<textarea>`:
```javascript
const inputNama = document.querySelector("#input-nama");
const previewTeks = document.querySelector("#preview-nama");

inputNama.addEventListener("input", (e) => {
  // e.target.value mengambil isi teks yang sedang diketik
  previewTeks.textContent = e.target.value;
});
```
> *Tips: Event `input` sangat ideal untuk fitur live search/filter atau live preview.*

---

### C. Event `'submit'` pada Form
Ketika mengelola formulir, biasakan memasang event listener pada tag **`<form>`** dengan event **`submit`**, bukan pada tombolnya. Mengapa? Karena event `submit` akan otomatis menangkap pengiriman form baik saat tombol diklik **maupun saat pengguna menekan tombol Enter di keyboard**.

```javascript
const formLogin = document.querySelector("#form-login");

formLogin.addEventListener("submit", (e) => {
  // Lakukan validasi dan ambil data form di sini
});
```

---

## 3. Sangat Krusial: `event.preventDefault()`

Secara bawaan (*default behavior*), browser akan **me-refresh / memuat ulang seluruh halaman** setiap kali formulir `<form>` di-submit.

Dalam aplikasi web modern (Single Page Application / SPA), kita tidak ingin halaman me-refresh karena seluruh data di memori JavaScript akan hilang/reset.

Untuk menghentikan refresh bawaan tersebut, kita memanggil:
```javascript
e.preventDefault();
```

### Contoh Lengkap Penanganan Form:
```javascript
const formDaftar = document.querySelector("#form-pendaftaran");

formDaftar.addEventListener("submit", (e) => {
  // 1. Wajib: Hentikan refresh bawaan browser!
  e.preventDefault();

  // 2. Ambil nilai input dengan aman
  const nama = document.querySelector("#input-nama").value;
  console.log("Form berhasil diproses tanpa reload! Nama:", nama);
});
```

---

## Rangkuman Singkat

| Event / Method | Waktu Kejadian | Contoh Kasus |
| :--- | :--- | :--- |
| **`"click"`** | Saat mouse mengklik elemen | Tombol aksi, tutup modal |
| **`"input"`** | Real-time saat mengetik | Live search, karakter counter |
| **`"submit"`** | Saat form dikirim (klik tombol / Enter) | Formulir pendaftaran / login |
| **`e.preventDefault()`**| Menghentikan aksi bawaan browser | Mencegah halaman reload saat submit form |
| **`e.target`** | Elemen yang memicu event | Mengetahui elemen mana yang disentuh |
