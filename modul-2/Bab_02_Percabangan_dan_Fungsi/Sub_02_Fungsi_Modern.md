# Sub-Bab 02: Fungsi Modern (Declaration vs Arrow Function, Parameter, & Return)

Dalam pengembangan perangkat lunak, prinsip utama yang harus dipegang adalah **DRY (Don't Repeat Yourself)**. Jika Anda menulis 5 baris kode yang sama di 3 tempat berbeda, gunakan **Fungsi (Function)**.

Fungsi adalah blok kode terisolasi yang dirancang untuk melakukan tugas spesifik, menerima input, memprosesnya, dan mengembalikan hasil.

---

## 1. Tiga Cara Menulis Fungsi di JavaScript

### A. Function Declaration (Cara Tradisional)
Cara klasik yang paling umum dan mudah dipahami:
```javascript
function hitungLuasPersegi(sisi) {
  return sisi * sisi;
}
```
* **Karakteristik Unik**: Mengalami **Hoisting penuh**. Artinya, Anda bisa memanggil fungsi ini di baris 1, meskipun fungsinya baru ditulis di baris 50.

### B. Function Expression
Fungsi tanpa nama (*anonymous function*) yang disimpan ke dalam variabel:
```javascript
const hitungLuasPersegi = function(sisi) {
  return sisi * sisi;
};
```
* Mengikuti aturan variabel (`const`/`let`), sehingga **tidak bisa dipanggil sebelum baris deklarasinya**.

### C. Arrow Function (`() => {}`) ⚡ (Standar Modern ES6)
Diperkenalkan pada ES6 (2015), cara ini adalah standar industri modern karena sintaksisnya yang sangat ringkas:
```javascript
// Bentuk standar Arrow Function:
const hitungLuasPersegi = (sisi) => {
  return sisi * sisi;
};
```

---

## 2. Keajaiban Arrow Function (Sintaks Ringkas)

Arrow function memiliki fitur pemangkasan kode yang elegan:

### 1. Implicit Return (Return Otomatis Tanpa Kata Kunci `return`)
Jika fungsi Anda **hanya berisi 1 baris ekspresi**, Anda boleh menghapus kurung kurawal `{ }` dan kata kunci `return`:
```javascript
// Sangat ringkas dalam 1 baris:
const kaliDua = angka => angka * 2;

console.log(kaliDua(5)); // 10
```

### 2. Parameter Tunggal Tanpa Kurung `( )`
Jika fungsi hanya menerima **satu parameter**, tanda kurung `( )` boleh dihilangkan:
```javascript
const sapa = nama => `Halo, ${nama}!`;
```
*(Catatan: Jika parameternya ada 2 atau lebih, atau tidak ada parameter sama sekali, tanda kurung `( )` tetap wajib ditulis: `(a, b) => a + b` atau `() => "Halo"`)*.

---

## 3. Parameter, Argumen, dan Default Parameter

* **Parameter**: Nama variabel penampung di dalam tanda kurung definisi fungsi.
* **Argumen**: Nilai nyata yang dikirimkan saat fungsi dipanggil.

### Default Parameter
Mencegah nilai `undefined` jika pengguna lupa memasukkan argumen saat memanggil fungsi:
```javascript
// Memberikan nilai default "Mahasiswa" jika argumen nama tidak diisi:
function sambut(nama = "Mahasiswa", semester = 1) {
  return `Selamat datang ${nama}, Anda di semester ${semester}.`;
}

console.log(sambut("Hikari", 5)); // "Selamat datang Hikari, Anda di semester 5."
console.log(sambut());             // "Selamat datang Mahasiswa, Anda di semester 1."
```

---

## 4. Nilai Kembalian (`return`)

* Kata kunci `return` mengembalikan hasil akhir ke baris kode yang memanggilnya.
* `return` langsung **menghentikan eksekusi fungsi**. Kode apa pun di bawah baris `return` di dalam fungsi yang sama tidak akan pernah dijalankan.
* Jika suatu fungsi tidak memiliki pernyataan `return`, fungsi tersebut secara otomatis mengembalikan nilai **`undefined`**.

```javascript
function cekStatus(nilai) {
  if (nilai < 0) {
    return "Nilai tidak valid"; // Fungsi langsung berhenti di sini jika nilai < 0
  }
  return nilai >= 75 ? "Lulus" : "Remedial";
}
```

---

*Silakan buka berkas [Praktik_Bab_02.html](Praktik_Bab_02.html) untuk melihat pembuktian eksekusi fungsi ini di browser dan console.*
