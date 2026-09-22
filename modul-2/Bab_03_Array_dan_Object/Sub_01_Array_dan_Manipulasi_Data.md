# Sub-Bab 01: Array & Manipulasi Data Dasar

Selamat datang di **Bab 03: Mengelola Data dengan Array & Object**! Di bab ini, kita mulai mempelajari cara menyimpan, mengelompokkan, dan memanipulasi banyak data sekaligus layaknya aplikasi nyata.

---

## 1. Apa Itu Array?

**Array** adalah struktur data berbentuk daftar berurutan (*ordered list*) yang mampu menyimpan banyak nilai di dalam satu variabel tunggal.

Array didefinisikan menggunakan tanda kurung siku `[]`:

```javascript
// Array berisi kumpulan string
const daftarMahasiswa = ["Hikari", "Budi", "Siti"];

// Array bisa menampung angka, boolean, atau tipe data campuran
const nilaiUjian = [85, 90, 78, 92];
```

### Karakteristik Penting:
1. **Zero-Indexed (Dimulai dari Indeks 0)**:
   - Elemen pertama berada di indeks `0`.
   - Elemen kedua berada di indeks `1`, dan seterusnya.
2. **Properti `.length`**:
   - Digunakan untuk mengetahui jumlah total elemen di dalam array.
   - Indeks elemen terakhir selalu berada di `array.length - 1`.

```javascript
const buah = ["Apel", "Jeruk", "Mangga"];

console.log(buah[0]); // "Apel" (elemen pertama)
console.log(buah[1]); // "Jeruk"
console.log(buah.length); // 3 (jumlah elemen)
console.log(buah[buah.length - 1]); // "Mangga" (elemen terakhir)

// Mengubah isi elemen tertentu:
buah[1] = "Alpukat";
console.log(buah); // ["Apel", "Alpukat", "Mangga"]
```

> **Catatan Kritis (`const` pada Array):**  
> Mendeklarasikan array dengan `const` bukan berarti isi datanya beku. `const` hanya melarang Anda menimpa variabelnya dengan array baru (`buah = []` akan error). Mengubah, menambah, atau menghapus elemen di dalam array **sepenuhnya sah dan diperbolehkan**.

---

## 2. Menambah & Menghapus Elemen

JavaScript menyediakan fungsi bawaan (*built-in methods*) untuk mengubah susunan array:

### A. Di Ujung Belakang: `.push()` & `.pop()`
* **`.push(item)`**: Menambahkan elemen baru ke posisi **paling belakang**.
* **`.pop()`**: Menghapus dan mengambil elemen **paling terakhir**.

```javascript
const angka = [10, 20, 30];

angka.push(40);
console.log(angka); // [10, 20, 30, 40]

const elemenDihapus = angka.pop();
console.log(elemenDihapus); // 40
console.log(angka); // [10, 20, 30]
```

### B. Di Ujung Depan: `.unshift()` & `.shift()`
* **`.unshift(item)`**: Menambahkan elemen ke posisi **paling depan** (menggeser elemen lain ke kanan).
* **`.shift()`**: Menghapus elemen **paling pertama**.

```javascript
const antrean = ["Budi", "Siti"];

antrean.unshift("Hikari"); // Masuk ke depan
console.log(antrean); // ["Hikari", "Budi", "Siti"]

antrean.shift(); // Keluar dari depan
console.log(antrean); // ["Budi", "Siti"]
```

### C. Di Sembarang Posisi: `.splice()`
`.splice(indeksAwal, jumlahDihapus, itemBaru...)` sangat serbaguna: bisa menghapus, menyisipkan, atau mengganti elemen di posisi mana pun.

```javascript
const warna = ["Merah", "Hijau", "Biru", "Kuning"];

// 1. Menghapus 1 elemen mulai dari indeks 1 ("Hijau" dihapus)
warna.splice(1, 1);
console.log(warna); // ["Merah", "Biru", "Kuning"]

// 2. Menyisipkan "Ungu" di indeks 2 tanpa menghapus apa pun (jumlahDihapus = 0)
warna.splice(2, 0, "Ungu");
console.log(warna); // ["Merah", "Biru", "Ungu", "Kuning"]
```

---

## 3. Mencari Elemen di Dalam Array

Untuk memeriksa apakah suatu data ada di dalam array:

### A. `.includes(nilaiCari)`
Mengembalikan nilai boolean `true` jika data ditemukan, atau `false` jika tidak ada.

```javascript
const hobi = ["Membaca", "Ngoding", "Gaming"];

console.log(hobi.includes("Ngoding")); // true
console.log(hobi.includes("Memasak")); // false
```

### B. `.indexOf(nilaiCari)`
Mengembalikan **nomor indeks** tempat data pertama kali ditemukan. Jika data **tidak ada**, method ini akan mengembalikan angka `-1`.

```javascript
const kota = ["Jakarta", "Bandung", "Surabaya"];

console.log(kota.indexOf("Bandung")); // 1
console.log(kota.indexOf("Medan"));   // -1 (tidak ditemukan)
```

---

## 4. Mengulang Array dengan `.forEach()`

Selain perulangan tradisional `for`, cara modern dan paling bersih untuk mengunjungi setiap elemen array adalah menggunakan method **`.forEach()`**:

```javascript
const daftarSiswa = ["Andi", "Budi", "Citra"];

// forEach menerima callback function: (item, index)
daftarSiswa.forEach((nama, urutan) => {
  console.log(`Siswa ke-${urutan + 1}: ${nama}`);
});
```

Keuntungan `.forEach()`:
- Kode lebih ringkas dan deklaratif.
- Menghindari bug salah hitung batas indeks (`i < array.length`).
- Parameter pertama otomatis berisi nilai elemen, parameter kedua otomatis nomor indeksnya.

---

## Rangkuman Singkat

| Method / Properti | Fungsi | Hasil / Efek |
| :--- | :--- | :--- |
| `.length` | Menghitung jumlah elemen | Angka total panjang |
| `array[i]` | Akses/ubah elemen indeks ke-`i` | Nilai elemen |
| `.push(x)` | Tambah elemen di akhir | Menambah elemen ke ujung belakang |
| `.pop()` | Hapus elemen terakhir | Mengembalikan elemen yang dibuang |
| `.splice()` | Hapus / sisip di sembarang posisi | Mengubah susunan array secara langsung |
| `.includes(x)`| Cek keberadaan nilai `x` | `true` atau `false` |
| `.indexOf(x)` | Cari nomor indeks nilai `x` | Indeks (angka) atau `-1` |
| `.forEach()` | Perulangan tiap elemen | Menjalankan fungsi untuk tiap data |
