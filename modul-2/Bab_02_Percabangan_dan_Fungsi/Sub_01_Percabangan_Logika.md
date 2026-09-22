# Sub-Bab 01: Percabangan Logika (`if`, `else if`, `else`, & Ternary Operator)

Secara default, komputer membaca dan mengeksekusi kode dari atas ke bawah (*sequential*). Namun, dalam aplikasi nyata, program perlu mengambil keputusan: *"Jika pengguna sudah login, buka dashboard; jika belum, arahkan ke halaman login"*. 

Inilah fungsi dari **Percabangan Logika (Control Flow)**.

---

## 1. Struktur `if`, `else if`, dan `else`

Blok kode di dalam kurung kurawal `{ }` hanya akan dieksekusi jika kondisi di dalam tanda kurung `( )` bernilai **`true`**.

```javascript
const nilaiUjian = 85;

if (nilaiUjian >= 85) {
  console.log("Grade: A");
} else if (nilaiUjian >= 70) {
  console.log("Grade: B");
} else if (nilaiUjian >= 60) {
  console.log("Grade: C");
} else {
  console.log("Grade: D (Tidak Lulus)");
}
```

* Urutan evaluasi selalu **dari atas ke bawah**.
* Begitu ada salah satu kondisi yang bernilai `true`, browser mengeksekusi blok tersebut lalu **langsung keluar**, mengabaikan cabang-cabang di bawahnya.

---

## 2. Ternary Operator (`kondisi ? nilaiJikaTrue : nilaiJikaFalse`)

Ternary operator adalah jalan pintas untuk menuliskan `if-else` dua cabang dalam **satu baris**. 

Perbedaannya: `if-else` biasa adalah sebuah *pernyataan (statement)*, sedangkan Ternary Operator adalah sebuah *ekspresi (expression)* yang langsung **menghasilkan nilai**, sehingga bisa langsung ditampung ke dalam variabel.

```javascript
const nilai = 78;

// Cara Panjang (if-else biasa):
let statusKelulusan;
if (nilai >= 75) {
  statusKelulusan = "LULUS";
} else {
  statusKelulusan = "REMEDIAL";
}

// Cara Ringkas & Modern (Ternary Operator):
const statusModern = nilai >= 75 ? "LULUS" : "REMEDIAL";

console.log(statusModern); // "LULUS"
```

> **Kapan Menggunakan Ternary?**  
> Gunakan Ternary jika percabangannya sederhana (hanya 2 pilihan: A atau B). Jika kondisinya bertingkat banyak (`else if` berulang kali), tetap gunakan `if-else` biasa agar kode tetap mudah dibaca.

---

## 3. Konsep Penting: Truthy dan Falsy Values

Di JavaScript, kondisi di dalam `if (...)` tidak harus berupa boolean murni (`true` atau `false`). Tipe data apa pun (string, number, object) bisa dievaluasi sebagai benar (*truthy*) atau salah (*falsy*).

### 6 Nilai yang Dianggap "FALSY" (Selalu Bernilai False):
1. `false` (boolean false)
2. `0` (angka nol)
3. `""` (string kosong)
4. `null` (tidak ada nilai)
5. `undefined` (belum diisi)
6. `NaN` (*Not-a-Number*)

**Selain 6 nilai di atas, SEMUANYA dianggap TRUTHY (True)!**  
*Catatan:* Angka negatif `-5`, string berisi spasi `" "`, string teks `"0"`, array kosong `[]`, dan object kosong `{}` semuanya bernilai **truthy**!

```javascript
const namaInput = ""; // Falsy (string kosong)

if (namaInput) {
  console.log("Nama terisi:", namaInput);
} else {
  console.log("Nama belum diisi!"); // Blok ini yang akan berjalan!
}
```

---

## 4. Nilai Default Singkat: `||` dan Nullish Coalescing `??`

Memanfaatkan sifat Truthy/Falsy, pengembang JavaScript sering menggunakan operator logika untuk menentukan nilai *fallback* (nilai cadangan):

### A. Operator OR (`||`)
Mengambil nilai kanan jika nilai kiri bernilai **falsy**:
```javascript
let namaPengguna = ""; 
let sapaan = namaPengguna || "Tamu"; 
console.log(sapaan); // "Tamu" (karena "" adalah falsy)
```

### B. Nullish Coalescing (`??`)
Hanya mengambil nilai kanan jika nilai kiri bernilai **`null` atau `undefined`** (angka `0` dan string kosong `""` tetap dipertahankan):
```javascript
let jumlahSkor = 0;

let skorA = jumlahSkor || 10; // Menghasilkan 10 (karena 0 dianggap falsy oleh ||)
let skorB = jumlahSkor ?? 10; // Menghasilkan 0 (karena 0 BUKAN null/undefined. Ini lebih akurat!)
```

---

*Silakan buka berkas [Praktik_Bab_02.html](Praktik_Bab_02.html) untuk melihat pembuktian alur logika ini secara langsung.*
