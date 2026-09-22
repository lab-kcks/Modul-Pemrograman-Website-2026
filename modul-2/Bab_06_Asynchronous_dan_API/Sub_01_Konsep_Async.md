# Sub-Bab 01: Konsep Synchronous vs Asynchronous

Selamat datang di **Bab 06: Asynchronous JavaScript & Fetch API**! Ini adalah bab puncak dalam silabus fundamental JavaScript modern. Di bab ini, Anda akan mempelajari bagaimana website berkomunikasi dengan server internet untuk mengambil data nyata dari dunia luar.

---

## 1. Masalah: JavaScript Bersifat Single-Threaded

JavaScript pada dasarnya bekerja secara **Single-Threaded**, yang berarti:
* JavaScript hanya memiliki **satu jalur kerja (Call Stack)**.
* JavaScript hanya bisa mengeksekusi **satu instruksi dalam satu waktu**.

### Apa Itu Synchronous (Blocking)?
Dalam eksekusi biasa (*Synchronous*), setiap baris kode harus **menunggu** baris sebelumnya selesai dieksekusi:

```javascript
console.log("1. Mulai");
// Bayangkan ada operasi berat memuat data yang butuh waktu 3 detik:
operasiBeratMenunggu3Detik(); 
console.log("2. Selesai");
```
👉 **Bahaya Blocking**: Jika operasi tersebut memakan waktu lama (misalnya mengambil data dari server di belahan dunia lain), antarmuka web akan **membeku total (*freeze*)**. Pengguna tidak bisa mengklik tombol, mengetik, atau bahkan menggulir (*scroll*) halaman!

---

## 2. Solusi: Asynchronous (Non-Blocking)

Untuk mengatasi masalah pembekuan tersebut, JavaScript menggunakan mekanisme **Asynchronous (Tidak Menunggu)**:

* Operasi yang butuh waktu (seperti mengambil data internet atau timer) akan **didelegasikan ke latar belakang browser (Web APIs)**.
* JavaScript **tidak menunggu** operasi tersebut selesai, melainkan **langsung melanjutkan mengeksekusi baris kode berikutnya**.
* Ketika operasi di latar belakang sudah selesai, hasilnya akan dikirimkan kembali ke JavaScript melalui fungsi pemanggil (*callback*).

### Pembuktian dengan `setTimeout()`:
`setTimeout(fungsi, milidetik)` adalah contoh paling sederhana dari operasi asynchronous:

```javascript
console.log("A. Antrean pertama");

// Dijalankan di latar belakang setelah 2000 milidetik (2 detik):
setTimeout(() => {
  console.log("B. Proses latar belakang selesai (setelah 2 detik)");
}, 2000);

console.log("C. Antrean berikutnya langsung jalan!");
```

**Urutan Output di Console:**
1. `"A. Antrean pertama"`
2. `"C. Antrean berikutnya langsung jalan!"` *(Langsung dieksekusi tanpa menunggu 2 detik!)*
3. `"B. Proses latar belakang selesai (setelah 2 detik)"` *(Muncul belakangan setelah timer usai).*

---

## 3. Mengenal Objek `Promise` (Janji)

Di JavaScript modern, operasi asynchronous yang berkomunikasi dengan server dibungkus dalam sebuah objek bernama **`Promise`**.

Sesuai namanya, **Promise adalah "Janji"** bahwa sebuah proses di masa depan akan menghasilkan nilai:
* **Pending (Menunggu)**: Janji sedang diproses (masih berlangsung di latar belakang).
* **Fulfilled / Resolved (Ditepati / Berhasil)**: Operasi sukses dan data berhasil didapatkan.
* **Rejected (Gagal)**: Terjadi masalah (misalnya koneksi internet putus, server mati, atau data tidak ditemukan).

Di masa lalu, pengembang web menggunakan rantai panjang `.then()` dan `.catch()` untuk menangani Promise. Namun di JavaScript modern, cara terbaik, terbersih, dan paling mudah dibaca adalah menggunakan **`async` / `await`**, yang akan kita pelajari di Sub-Bab 02.

---

## Rangkuman Singkat

| Konsep | Cara Kerja | Dampak pada Halaman Web |
| :--- | :--- | :--- |
| **Synchronous** | Baris demi baris, menunggu proses lama selesai | Halaman bisa membeku (*freeze/blocking*) jika ada proses berat |
| **Asynchronous** | Menjalankan proses berat di latar belakang, lanjut mengeksekusi kode berikutnya | Halaman tetap responsif dan lancar (*non-blocking*) |
| **Promise** | Wadah penampung data asynchronous yang memiliki status Pending, Fulfilled, atau Rejected | Menjadi standar komunikasi modern di JavaScript |

---
*Silakan buka berkas [Praktik_Bab_06.html](Praktik_Bab_06.html) untuk melihat pembuktian eksekusi Asynchronous secara langsung.*
