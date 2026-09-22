# Sub-Bab 02: Object Literals & Array of Objects

Jika Array digunakan untuk menyimpan sekumpulan data yang berurutan (seperti daftar angka atau nama), maka **Object** digunakan untuk menyimpan sekumpulan karakteristik/properti dari satu entitas yang sama (seperti data lengkap seorang mahasiswa atau sebuah barang).

---

## 1. Apa Itu Object Literal?

**Object** di JavaScript adalah struktur data yang menyimpan informasi dalam pasangan **Key-Value** (Kunci dan Nilai). 

Object dibuat menggunakan tanda kurung kurawal `{}`:

```javascript
const mahasiswa = {
  nim: "220101",
  nama: "Hikari",
  jurusan: "Teknologi Informasi",
  semester: 5,
  aktif: true
};
```
- `nim`, `nama`, `jurusan` disebut **Key** atau **Properti**.
- `"220101"`, `"Hikari"`, `5` disebut **Value** (bisa berupa teks, angka, boolean, array, atau bahkan object lain).

> **Karakteristik `const` pada Object:**  
> Sama seperti Array, mendeklarasikan object dengan `const` melarang penimpaan variabel (`mahasiswa = {}` akan error), namun **isi propertinya bebas diubah, ditambah, atau dihapus**.

---

## 2. Cara Mengakses & Memodifikasi Properti

Ada dua cara untuk membaca nilai dari sebuah object:

### A. Dot Notation (`object.properti`)
Cara yang paling umum, bersih, dan direkomendasikan jika nama kuncinya sudah pasti:
```javascript
console.log(mahasiswa.nama);     // "Hikari"
console.log(mahasiswa.jurusan);  // "Teknologi Informasi"
```

### B. Bracket Notation (`object["properti"]`)
Menggunakan tanda kurung siku dan string nama kunci. Cara ini **wajib digunakan** jika:
1. Nama kunci tersimpan di dalam variabel.
2. Nama kunci mengandung spasi atau karakter khusus (misal: `"kode pos"`).

```javascript
console.log(mahasiswa["nim"]);   // "220101"

// Akses dinamis menggunakan variabel:
const kunciYangDicari = "jurusan";
console.log(mahasiswa[kunciYangDicari]); // "Teknologi Informasi"
```

### C. Menambah, Mengubah, & Menghapus Properti
```javascript
// 1. Mengubah properti yang sudah ada:
mahasiswa.semester = 6;

// 2. Menambah properti baru:
mahasiswa.ipk = 3.85;

// 3. Menghapus properti menggunakan keyword 'delete':
delete mahasiswa.aktif;
```

---

## 3. Method di Dalam Object

Jika sebuah fungsi disimpan sebagai nilai di dalam object, fungsi tersebut disebut **Method**. Di dalam method, kita bisa menggunakan keyword **`this`** untuk mengakses properti milik object itu sendiri.

```javascript
const user = {
  namaDepan: "Budi",
  namaBelakang: "Santoso",
  
  // Method
  namaLengkap() {
    return `${this.namaDepan} ${this.namaBelakang}`;
  }
};

console.log(user.namaLengkap()); // "Budi Santoso"
```

---

## 4. Struktur Data Dunia Nyata: Array of Objects

Di dunia pengembangan web nyata (seperti data yang diterima dari database atau API backend), data hampir selalu berbentuk **Array of Objects** (kumpulan beberapa object di dalam satu array):

```javascript
const daftarProduk = [
  { id: 1, nama: "Laptop", harga: 8500000, stok: 5 },
  { id: 2, nama: "Mouse", harga: 150000, stok: 20 },
  { id: 3, nama: "Keyboard", harga: 450000, stok: 12 }
];
```

### Cara Mengakses Data Bertingkat:
1. Ambil elemen array berdasarkan indeksnya: `daftarProduk[0]` (menghasilkan object Laptop).
2. Ambil propertinya menggunakan dot notation: `daftarProduk[0].nama` (menghasilkan `"Laptop"`).

### Mengulang Array of Objects dengan `.forEach()`:
```javascript
daftarProduk.forEach((produk, urutan) => {
  console.log(`${urutan + 1}. ${produk.nama} - Rp ${produk.harga} (Sisa stok: ${produk.stok})`);
});
```

---

## Rangkuman Singkat

| Operasi | Sintaksis | Contoh |
| :--- | :--- | :--- |
| Membuat Object | `{ key: value }` | `const mhs = { nama: "Hikari" };` |
| Baca Nilai (Dot) | `obj.key` | `mhs.nama` |
| Baca Nilai (Bracket) | `obj["key"]` atau `obj[variabel]` | `mhs["nama"]` |
| Tambah / Edit | `obj.keyBaru = nilai` | `mhs.ipk = 3.9;` |
| Hapus Properti | `delete obj.key` | `delete mhs.ipk;` |
| Array of Objects | `[{...}, {...}]` | `data[0].nama` |
