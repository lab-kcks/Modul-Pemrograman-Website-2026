# Sub-Bab 02: Variabel Modern (`let` vs `const` vs `var`) & Aturan Scope

Dalam pemrograman, **variabel** adalah wadah (atau kotak berlabel di memori komputer) yang digunakan untuk menyimpan suatu nilai atau data agar dapat digunakan dan dimanipulasi di baris kode berikutnya.

---

## 1. Tiga Kata Kunci Pembuat Variabel

Di JavaScript modern, Anda akan menjumpai 3 kata kunci untuk membuat variabel: `var`, `let`, dan `const`.

```javascript
var namaLama = "Budi";  // Cara lama (hindari)
let namaBaru = "Siti";  // Cara modern (bisa diubah nilainya)
const phi = 3.14;       // Cara modern (konstan / tidak bisa diubah)
```

---

## 2. Mengapa `var` Ditinggalkan? (Masalah `var`)

Sebelum tahun 2015 (sebelum standar modern **ES6**), JavaScript hanya memiliki `var`. Namun, `var` memiliki dua perilaku aneh yang sering menimbulkan bug tersembunyi:

### A. Masalah Kebocoran Scope (*Function Scope* vs *Block Scope*)
Variabel `var` **tidak peduli** dengan blok kurung kurawal `{ }` pada `if`, `while`, atau `for`. Variabel tersebut akan bocor dan tetap bisa diakses di luar blok.

```javascript
if (true) {
  var hewan = "Kucing";
  let tumbuhan = "Mawar";
}

console.log(hewan);    // Output: "Kucing" -> BOCOR ke luar blok!
console.log(tumbuhan); // ❌ ReferenceError: tumbuhan is not defined (Aman!)
```

### B. Bahaya Deklarasi Ulang (*Re-declaration*)
Dengan `var`, Anda bisa mendeklarasikan variabel dengan nama yang sama persis tanpa sengaja, dan browser tidak akan memberi peringatan error. Ini sangat berbahaya di proyek besar!

```javascript
var saldo = 100000;
// ... 50 baris kode kemudian ...
var saldo = 5000; // Tidak error, nilai lama tertimpa tanpa disadari!

// Bandingkan dengan let:
let tabungan = 100000;
let tabungan = 5000; // ❌ SyntaxError: Identifier 'tabungan' has already been declared
```

---

## 3. `let` vs `const`: Kapan Menggunakan yang Mana?

Kedua kata kunci ini diperkenalkan di ES6 dan memiliki sifat **Block Scope** (hanya hidup di dalam kurung kurawal `{ }` tempat ia dibuat).

| Fitur | `let` | `const` | `var` |
| :--- | :--- | :--- | :--- |
| **Sifat Scope** | Block Scope `{}` | Block Scope `{}` | Function Scope (Bocor di if/for) |
| **Bisa Diubah Nilainya (*Reassign*)?** | **BISA** (`x = 10`) | **TIDAK BISA** (Akan TypeError) | **BISA** |
| **Wajib Diisi Nilai Awal?** | Tidak (`let x;`) | **Wajib** (`const x = 5;`) | Tidak |
| **Bisa Dideklarasi Ulang?** | Tidak | Tidak | Bisa (Rentan bug) |
| **Rekomendasi Pemakaian** | Gunakan saat nilai pasti berubah (counter loop, input form) | **Gunakan sebagai pilihan default** | **Jangan gunakan lagi** |

### Aturan Emas JavaScript Modern:
> **Gunakan `const` secara default.** Jika dan hanya jika nilai variabel tersebut memang perlu diubah di baris berikutnya, barulah gunakan `let`. Jangan gunakan `var`.

---

## 4. Perilaku Khusus `const` pada Array dan Object

Banyak pemula mengira bahwa jika variabel dibuat dengan `const`, isi datanya sama sekali tidak bisa disentuh. Ini adalah kesalahpahaman umum:

* `const` mencegah **re-assignment** (mengganti seluruh wadah dengan wadah baru).
* Namun, untuk tipe data kompleks (seperti **Object** dan **Array**), **isi properti di dalamnya masih bisa diubah/dimutasi**!

```javascript
const user = { nama: "Andi", umur: 20 };

// INI DIPERBOLEHKAN (mengubah isi properti):
user.umur = 21; 
console.log(user.umur); // 21

// INI DILARANG (mengganti seluruh objek dengan nilai baru):
user = { nama: "Budi" }; // ❌ TypeError: Assignment to constant variable.
```

---

## 5. Aturan Penamaan Variabel (Identifier Rules)

1. Gunakan gaya penulisan **camelCase** (contoh: `namaLengkap`, `totalBelanja`, `isLoggedIn`).
2. Karakter pertama harus berupa **huruf**, tanda dolar (`$`), atau garis bawah (`_`). **Tidak boleh diawali angka** (contoh salah: `1user`).
3. Bersifat **case-sensitive** (`nama` dan `Nama` adalah dua variabel yang berbeda).
4. Tidak boleh menggunakan kata kunci yang dicadangkan bahasa JavaScript (*reserved keywords*, seperti `function`, `let`, `return`, `class`).

---

*Silakan buka dan coba pengujian variabel ini pada berkas [Praktik_Bab_01.html](Praktik_Bab_01.html)!*
