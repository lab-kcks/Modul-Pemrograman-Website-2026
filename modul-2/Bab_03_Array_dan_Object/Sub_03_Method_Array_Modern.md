# Sub-Bab 03: Method Array Modern (`map` & `filter`)

Dalam JavaScript modern, memproses data di dalam array jarang menggunakan perulangan manual `for`. Dua method yang paling sering digunakan di industri dan framework web (seperti React, Vue, dll.) adalah **`.map()`** dan **`.filter()`**.

Keduanya memiliki karakteristik istimewa: **Immutability (Tidak Mengubah Array Asli)**. Kedua method ini selalu menghasilkan **Array Baru**, sehingga data asli tetap aman.

---

## 1. Perbedaan Mendasar dengan `.forEach()`

Sebelum masuk ke detail, pahami perbedaan tujuan utamanya:

* **`.forEach()`**: Hanya bertugas "berkeliling" mengunjungi setiap elemen untuk melakukan aksi (seperti `console.log` atau cetak ke layar). Method ini **tidak mengembalikan nilai apa pun** (`return undefined`).
* **`.map()`**: Bertugas **mentransformasi (mengubah bentuk)** setiap elemen menjadi nilai baru, lalu menghasilkan **Array Baru** dengan panjang yang sama.
* **`.filter()`**: Bertugas **menyaring (menyeleksi)** elemen berdasarkan kriteria tertentu, lalu menghasilkan **Array Baru** yang hanya berisi elemen-elemen yang lolos seleksi.

---

## 2. Method `.map()`: Transformasi Data

Gunakan `.map()` ketika Anda ingin mengubah setiap isi elemen array menjadi bentuk lain.

### A. Contoh Sederhana: Mengalikan Angka
```javascript
const angka = [1, 2, 3, 4, 5];

// Kalikan setiap angka dengan 2:
const hasilKali = angka.map(x => x * 2);

console.log(hasilKali); // [2, 4, 6, 8, 10] (Array baru)
console.log(angka);     // [1, 2, 3, 4, 5] (Array asli tetap aman)
```

### B. Contoh Nyata: Mengekstrak Data dari Array of Objects
Bayangkan Anda memiliki daftar produk dan hanya butuh mengambil nama-namanya saja:

```javascript
const daftarProduk = [
  { nama: "Laptop", harga: 8000000 },
  { nama: "Mouse", harga: 150000 },
  { nama: "Keyboard", harga: 450000 }
];

// Mengambil hanya properti 'nama' dari setiap produk:
const namaProduk = daftarProduk.map(produk => produk.nama);
console.log(namaProduk); 
// Hasil: ["Laptop", "Mouse", "Keyboard"]

// Menghitung harga setelah diskon 10%:
const hargaDiskon = daftarProduk.map(p => ({
  nama: p.nama,
  hargaBaru: p.harga * 0.9
}));
console.log(hargaDiskon);
```

---

## 3. Method `.filter()`: Menyaring Data

Gunakan `.filter()` ketika Anda ingin membuang data yang tidak memenuhi syarat dan hanya menyimpan data yang memenuhi kriteria.

Fungsi di dalam `.filter()` harus mengembalikan nilai **boolean**:
* Jika mengembalikan **`true`**, elemen tersebut dimasukkan ke array baru.
* Jika mengembalikan **`false`**, elemen tersebut dibuang/dilewati.

### A. Contoh Sederhana: Menyaring Angka Lulus
```javascript
const nilaiMahasiswa = [55, 78, 90, 45, 82, 60];

// Saring nilai yang >= 60 (Lulus):
const nilaiLulus = nilaiMahasiswa.filter(nilai => nilai >= 60);

console.log(nilaiLulus); // [78, 90, 82, 60]
```

### B. Contoh Nyata: Filter Barang yang Tersedia
Menyaring produk yang stoknya masih ada (stok > 0) dan harganya di bawah Rp 500.000:

```javascript
const inventaris = [
  { nama: "Mouse", harga: 150000, stok: 10 },
  { nama: "Keyboard", harga: 600000, stok: 5 },
  { nama: "Flashdisk", harga: 85000, stok: 0 },
  { nama: "Headset", harga: 300000, stok: 4 }
];

// Hanya barang yang stok > 0:
const barangTersedia = inventaris.filter(item => item.stok > 0);
console.log(barangTersedia); // Flashdisk tidak akan masuk karena stok 0

// Barang murah (< 500.000) yang siap beli:
const barangMurahSiapBeli = inventaris.filter(item => item.harga < 500000 && item.stok > 0);
console.log(barangMurahSiapBeli); // [Mouse, Headset]
```

---

## 4. Menggabungkan Keduanya (*Method Chaining*)

Karena `.filter()` dan `.map()` sama-sama mengembalikan array baru, kita bisa menyambungkannya secara langsung satu setelah yang lain (*chaining*):

```javascript
const inventaris = [
  { nama: "Mouse", harga: 150000, stok: 10 },
  { nama: "Keyboard", harga: 600000, stok: 5 },
  { nama: "Flashdisk", harga: 85000, stok: 0 },
  { nama: "Headset", harga: 300000, stok: 4 }
];

// Skenario: Ambil NAMA barang saja dari produk yang harganya di bawah 500.000 dan ready stock!
const rekomendasiNama = inventaris
  .filter(item => item.harga < 500000 && item.stok > 0) // 1. Saring dulu
  .map(item => item.nama);                              // 2. Ambil namanya

console.log(rekomendasiNama); 
// Hasil: ["Mouse", "Headset"]
```

---

## Rangkuman Perbandingan

| Method | Tujuan | Mengubah Array Asli? | Nilai Kembalian (*Return*) |
| :--- | :--- | :--- | :--- |
| **`.forEach()`** | Melakukan aksi pada tiap elemen | Tidak (kecuali diubah manual) | `undefined` (kosong) |
| **`.map()`** | Mengubah setiap elemen jadi bentuk baru | **Tidak** | **Array Baru** (panjang sama persis) |
| **`.filter()`** | Menyaring elemen sesuai kondisi boolean | **Tidak** | **Array Baru** (panjang $\le$ asli) |
