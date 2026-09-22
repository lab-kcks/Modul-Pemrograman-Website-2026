# Sub-Bab 02: Mengubah Konten & Gaya Tampilan (Content & Styles)

Setelah berhasil memilih (*select*) elemen HTML di Sub-Bab 01, langkah berikutnya adalah **memanipulasi** elemen tersebut: mengubah isi teksnya, menyuntikkan tag HTML, dan mengubah gaya tampilannya secara dinamis.

---

## 1. Mengubah Konten: `textContent` vs `innerHTML`

Ada dua cara utama untuk mengisi atau membaca isi dari sebuah elemen:

### A. `element.textContent` (Teks Murni - Sangat Aman)
* Membaca atau menulis **teks polos**.
* Jika Anda memasukkan tag HTML (seperti `<b>` atau `<span>`), browser **tidak akan merendernya**, melainkan mencetaknya sebagai teks mentah biasa.
* **Standar Keamanan**: Selalu gunakan `textContent` untuk menampilkan data dari pengguna guna mencegah celah keamanan **XSS (Cross-Site Scripting)**.

```javascript
const kotakPesan = document.getElementById("pesan");
kotakPesan.textContent = "Halo, selamat datang kembali!";

// Jika memasukkan tag HTML:
kotakPesan.textContent = "<strong>Penting!</strong>";
// Layar akan menampilkan tulisan: <strong>Penting!</strong> (tag tidak diterjemahkan)
```

---

### B. `element.innerHTML` (Render Tag HTML)
* Digunakan ketika Anda ingin **merender kode HTML** baru ke dalam elemen tersebut.
* Browser akan menguraikan tag HTML di dalamnya menjadi elemen DOM baru yang nyata.

```javascript
const kotakPesan = document.getElementById("pesan");

// Tag <strong> dan <span> akan benar-benar dirender tebal dan berwarna:
kotakPesan.innerHTML = "<strong style='color: red;'>Penting:</strong> Data berhasil disimpan.";
```

---

## 2. Mengubah Gaya CSS Langsung (`element.style`)

JavaScript memungkinkan Anda mengubah gaya inline elemen menggunakan properti `.style`:

```javascript
const kotak = document.getElementById("box-promo");

kotak.style.color = "#ffffff";
kotak.style.display = "block";
```

### Aturan Penulisan Nama Properti (camelCase):
Di CSS biasa, kita menggunakan tanda hubung (*kebab-case*), misalnya: `background-color`, `font-size`, `border-radius`.  
Namun di JavaScript, tanda `-` dianggap pengurangan matematika. Karena itu, JavaScript mengubahnya menjadi **camelCase**:

| CSS Biasa | Properti JavaScript (`element.style`) |
| :--- | :--- |
| `background-color` | `element.style.backgroundColor = "#0066cc";` |
| `font-size` | `element.style.fontSize = "18px";` |
| `border-radius` | `element.style.borderRadius = "8px";` |
| `margin-top` | `element.style.marginTop = "20px";` |

---

## 3. Mengelola Class CSS Modern (`element.classList`)

Mengubah `.style` satu per satu di JavaScript sering kali membuat kode berantakan. Cara terbaik di industri adalah **menyiapkan class styling di CSS**, lalu menggunakan JavaScript untuk menambah atau mencabut class tersebut melalui **`.classList`**.

JavaScript menyediakan 4 method utama pada `classList`:

### A. `.add("nama-class")`
Menambahkan class baru tanpa menghapus class yang sudah ada sebelumnya.
```javascript
kotak.classList.add("aktif");
```

### B. `.remove("nama-class")`
Menghapus class tertentu dari elemen.
```javascript
kotak.classList.remove("tersembunyi");
```

### C. `.toggle("nama-class")`
Sakelar otomatis: jika class **belum ada**, ia akan **menambahkannya**. Jika class **sudah ada**, ia akan **menghapusnya**. Sangat populer untuk fitur *Dark Mode* atau menu buka-tutup (*accordion/dropdown*).
```javascript
// Klik 1x: class 'dark-mode' ditambahkan
// Klik 2x: class 'dark-mode' dihapus
document.body.classList.toggle("dark-mode");
```

### D. `.contains("nama-class")`
Mengecek apakah elemen memiliki class tertentu (mengembalikan boolean `true` atau `false`).
```javascript
if (kotak.classList.contains("aktif")) {
  console.log("Kotak sedang dalam status aktif");
}
```

---

## Rangkuman Singkat

| Fitur | Fungsi | Kapan Dipakai? |
| :--- | :--- | :--- |
| **`textContent`** | Isi teks mentah polos | Menampilkan teks/angka biasa (Aman dari XSS) |
| **`innerHTML`** | Isi teks + render tag HTML | Menyuntikkan template HTML dinamis |
| **`style.camelCase`**| Ubah gaya inline spesifik | Mengubah nilai dinamis seperti posisi koordinat atau display |
| **`classList`** | Tambah/Hapus/Toggle class CSS | **Rekomendasi Utama** untuk styling interaktif yang rapi |
