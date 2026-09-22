# Sub-Bab 03: Tipe Data Primitif, Template Literals, & Operator

JavaScript adalah bahasa yang bersifat **dynamically typed** (bertipe dinamis). Artinya, Anda tidak perlu menuliskan tipe data secara eksplisit saat membuat variabel (seperti `int x` di C/Java). Tipe data variabel ditentukan secara otomatis oleh nilai (*value*) yang disimpan di dalamnya.

---

## 1. Tipe Data Primitif di JavaScript

Tipe data primitif adalah data dasar yang tidak dapat dipecah lagi dan disimpan langsung di dalam memori:

| Tipe Data | Deskripsi | Contoh |
| :--- | :--- | :--- |
| **`string`** | Teks | `"Halo"`, `'Dunia'`, `` `TI 2026` `` |
| **`number`** | Angka bulat maupun desimal/pecahan | `42`, `3.14`, `-10`, `NaN` |
| **`boolean`** | Nilai logika kebenaran | `true` atau `false` |
| **`null`** | Sengaja dikosongkan oleh pembuat program | `let data = null;` |
| **`undefined`** | Variabel sudah dibuat tapi belum diisi nilai | `let skor;` |

### Mengecek Tipe Data dengan `typeof`
Gunakan kata kunci `typeof` untuk memeriksa tipe data dari variabel:
```javascript
typeof "Budi"    // "string"
typeof 100       // "number"
typeof true      // "boolean"
typeof undefined // "undefined"

// ⚠️ Perhatian: Bug historis JavaScript sejak tahun 1995:
typeof null      // Menghasilkan "object" (secara teknis null adalah primitif, bukan object)
```

---

## 2. Template Literals (Modern String ES6)

Sebelum ES6, menggabungkan variabel ke dalam teks menggunakan operator plus `+` (*string concatenation*). Cara ini panjang dan rawan salah spasi.

Cara modern menggunakan **Template Literals** dengan simbol **backtick ( `` ` `` )** (terletak di sebelah kiri angka 1 keyboard) dan ekspresi `${...}`:

```javascript
const nama = "Hikari";
const semester = 5;

// Cara Lama:
const teksLama = "Halo " + nama + ", kamu semester " + semester + ".";

// Cara Modern (Template Literals):
const teksModern = `Halo ${nama}, kamu semester ${semester}. Tahun depan semester ${semester + 1}.`;

// Mendukung teks banyak baris (multi-line) langsung tanpa \n:
const pesan = `
  Baris 1
  Baris 2
`;
```

---

## 3. Operator Perbandingan: Mengapa WAJIB `===` (Strict Equality)?

JavaScript memiliki dua jenis operator perbandingan sama-dengan:

### A. Loose Equality (`==`) ❌ (Hindari)
Hanya membandingkan **nilai**, dan otomatis melakukan konversi tipe data paksa (*implicit type coercion*) jika tipenya berbeda. Ini sering memicu bug tak terduga!
```javascript
5 == "5"       // true (Angka 5 dianggap sama dengan String "5"!)
0 == false     // true
"" == false    // true
```

### B. Strict Equality (`===`) ✅ (Standar Wajib Industri)
Membandingkan **nilai DAN tipe data sekaligus**. Jika tipe datanya beda, langsung `false`.
```javascript
5 === "5"      // false (karena number !== string)
5 === 5        // true
0 === false    // false
```

> **Aturan Mutlak:**  
> Selalu gunakan **`===`** dan **`!==`** (tiga tanda sama dengan). Jangan pernah gunakan `==` atau `!=` kecuali ada alasan yang sangat spesifik.

---

## 4. Operator Logika & Konversi Tipe Data

### Operator Logika
* **`&&` (AND)**: Bernilai `true` jika **semua** kondisi benar.
* **`||` (OR)**: Bernilai `true` jika **salah satu** kondisi benar.
* **`!` (NOT)**: Membalikkan nilai logika (`!true` menjadi `false`).

### Konversi Tipe Data Eksplisit
Hindari membiarkan JavaScript menebak konversi tipe data secara otomatis. Lakukan konversi secara sadar:
```javascript
// Mengubah ke Angka:
const angka = Number("123");       // 123 (tipe number)
const angkaInt = parseInt("50px"); // 50

// Mengubah ke Teks:
const teks = String(100);          // "100" (tipe string)

// Mengubah ke Boolean:
Boolean(1);   // true
Boolean(0);   // false
Boolean("");  // false (string kosong adalah falsy)
```

---

*Silakan buka berkas [Praktik_Bab_01.html](Praktik_Bab_01.html) untuk melihat pembuktian kode secara langsung di browser dan console.*
