# Sub-Bab 01: Environment, Menghubungkan JS ke HTML, & Output Dasar

Selamat datang di langkah pertama belajar JavaScript! Sebagai mahasiswa TI yang sudah memahami HTML (struktur) dan CSS (tampilan), sekarang saatnya Anda memberi **otak dan interaksi (logika)** pada halaman web menggunakan **JavaScript**.

---

## 1. Bagaimana JavaScript Bekerja di Browser?

HTML dan CSS bersifat statis atau deklaratif. Ketika browser mengunduh berkas `.html`, browser membaca tag dari atas ke bawah (*parsing*). 
Di dalam browser modern (seperti Chrome, Edge, Firefox), terdapat **JavaScript Engine** (contoh: *V8 Engine* di Google Chrome) yang bertugas membaca, mengompilasi secara instan (*Just-In-Time Compilation*), dan mengeksekusi kode JavaScript Anda.

---

## 2. Tiga Cara Menghubungkan JavaScript ke HTML

Sama seperti CSS (inline, internal, external), JavaScript juga memiliki 3 cara integrasi:

### A. Inline JavaScript
Kode JavaScript ditulis langsung di dalam atribut HTML (disebut *event attribute*, contohnya `onclick`, `onmouseover`).

```html
<button onclick="alert('Halo dari Inline JS!')">Klik Saya</button>
```

* **Kelebihan**: Cepat untuk pengujian kilat 1 baris.
* **Kekurangan**: **Sangat tidak direkomendasikan** untuk proyek nyata. Kode bercampur aduk dengan struktur HTML (*Separation of Concerns* dilanggar) dan sulit di-maintain jika ada 100 tombol dengan logika yang sama.

---

### B. Internal JavaScript
Kode JavaScript ditulis di dalam tag `<script>` pada berkas HTML yang sama.

```html
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Belajar JS</title>
</head>
<body>
  <h1>Selamat Datang</h1>

  <script>
    console.log("Kode JS dieksekusi di sini!");
  </script>
</body>
</html>
```

* **Posisi Terbaik**: Biasanya diletakkan **sebelum tag penutup `</body>`** agar seluruh elemen HTML di atasnya selesai dimuat terlebih dahulu sebelum script dijalankan.
* **Penggunaan**: Cocok untuk prototipe halaman tunggal atau kode spesifik yang hanya ada di halaman tersebut.

---

### C. External JavaScript (Standar Industri / Best Practice)
Kode JavaScript ditulis di berkas terpisah berekstensi `.js` (misalnya `app.js`), lalu dipanggil menggunakan tag `<script src="...">`.

```html
<!-- Diletakkan di dalam <head> dengan atribut defer -->
<script src="./app.js" defer></script>
```

* **Mengapa Best Practice?**
  1. **Separation of Concerns**: Memisahkan logika (`.js`), tampilan (`.css`), dan struktur (`.html`).
  2. **Browser Caching**: Berkas `.js` eksternal akan disimpan di cache browser sehingga reload halaman berikutnya jauh lebih cepat.
  3. **Kolaborasi Tim**: Tim front-end bisa bekerja pada file JS tanpa mengganggu file HTML.

> [!TIP]
> **Atribut `defer` vs `async` pada tag `<script>`:**
> Secara default jika script ditaruh di `<head>`, browser akan berhenti me-render HTML saat mendownload dan mengeksekusi script (*render-blocking*).
> - Tambahkan atribut `defer`: `<script src="app.js" defer></script>`. Browser akan mendownload script di latar belakang, dan **mengeksekusinya hanya setelah seluruh DOM HTML selesai dibaca**.

---

## 3. Empat Cara Menampilkan Output di JavaScript

Ketika belajar bahasa seperti C/C++ atau Java, Anda menggunakan `printf` atau `System.out.println`. Di JavaScript untuk web, ada beberapa cara menampilkan data:

### 1. `console.log()` (Sahabat Utama Developer)
Menampilkan pesan atau nilai data di tab **Console Developer Tools** browser (Tekan `F12` atau `Ctrl + Shift + I` lalu buka tab *Console*).

```javascript
console.log("Ini pesan biasa");
console.info("Ini informasi");
console.warn("Ini peringatan berwarna kuning");
console.error("Ini pesan error berwarna merah");

// Bisa juga menampilkan data tabel yang rapi:
console.table(["HTML", "CSS", "JavaScript"]);
```
*Output di console tidak terlihat oleh pengunjung umum, menjadikannya sarana nomor 1 untuk debugging kode.*

---

### 2. Dialog Interaktif Browser (`alert`, `confirm`, `prompt`)
Memunculkan kotak dialog bawaan sistem operasi/browser yang mem-pause eksekusi halaman sementara.

* **`alert(pesan)`**: Hanya menampilkan pesan dengan tombol OK.
  ```javascript
  alert("Pendaftaran berhasil!");
  ```
* **`confirm(pertanyaan)`**: Menampilkan konfirmasi OK / Cancel (mengembalikan `true` atau `false`).
  ```javascript
  let yakin = confirm("Apakah Anda yakin ingin menghapus data?");
  console.log(yakin); // true jika klik OK, false jika Cancel
  ```
* **`prompt(pertanyaan, defaultValue)`**: Menampilkan input teks sederhana (mengembalikan teks yang diketik atau `null` jika batal).
  ```javascript
  let nama = prompt("Masukkan nama Anda:", "Mahasiswa TI");
  console.log("Halo, " + nama);
  ```

---

### 3. Menulis Langsung ke Elemen HTML (`textContent` / `innerHTML`)
Ini adalah cara modern yang sesungguhnya digunakan dalam pembuatan website interaktif.

```html
<p id="teks-sambutan">Teks awal...</p>

<script>
  document.getElementById("teks-sambutan").textContent = "Halo, selamat datang di TI!";
</script>
```

---

### 4. `document.write()` (JANGAN DIGUNAKAN DI ERA MODERN)
Dahulu digunakan untuk mencetak langsung ke body HTML. Namun jika dipanggil setelah halaman selesai dimuat, `document.write()` akan **menghapus seluruh isi halaman web Anda**. Oleh karena itu, hindari penggunaan ini kecuali untuk riset kuno.

---

## Ringkasan Cepat Sub-Bab 01
1. JavaScript dijalankan oleh mesin JS di browser (*client-side*).
2. Tiga cara menyambungkan JS: **Inline** (hindari), **Internal** (`<script>`), dan **External** (`<script src="..." defer>`).
3. Gunakan `console.log()` untuk memeriksa jalannya variabel/logika, dan `document.getElementById().textContent` untuk menampilkan hasil ke user.

---
*Silakan buka dan coba berkas live demo [Praktik_Bab_01.html](Praktik_Bab_01.html) di browser Anda!*
