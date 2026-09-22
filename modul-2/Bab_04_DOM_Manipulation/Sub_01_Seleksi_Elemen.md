# Sub-Bab 01: Seleksi Elemen Web (DOM Selection)

Selamat datang di **Hari 2: Interaktivitas Web Dinamis**! Di Bab 04 ini, kita menghubungkan seluruh logika JavaScript yang sudah dipelajari di Hari 1 langsung ke antarmuka web (HTML & CSS).

---

## 1. Apa Itu DOM (Document Object Model)?

Saat browser memuat file HTML, browser tidak sekadar membaca teks. Browser mengubah setiap tag HTML menjadi **pohon objek JavaScript** yang disebut **DOM**.

* Setiap tag HTML (`<h1>`, `<div>`, `<button>`) menjadi sebuah **Node / Object Elemen** di memori.
* Objek induk utamanya bernama **`document`**.
* Melalui objek `document`, JavaScript bisa mencari elemen, membaca isinya, mengubah gayanya, hingga menambah/menghapus tag HTML secara dinamis.

---

## 2. Tiga Metode Seleksi Elemen Utama

Dalam pengembangan web modern, ada 3 metode seleksi elemen yang paling sering digunakan:

### A. `document.getElementById("namaId")`
* Memilih **satu elemen tunggal** berdasarkan atribut `id`.
* **Ciri Khas**: Tulis nama ID-nya saja, **JANGAN** sertakan tanda pagar `#`.
* Sangat cepat dan paling sering dipakai untuk target elemen yang unik.

```javascript
// HTML: <h1 id="judul-utama">Halo Dunia</h1>
const judul = document.getElementById("judul-utama");
console.log(judul); // <h1 id="judul-utama">...</h1>
```

---

### B. `document.querySelector("selectorCSS")`
* Memilih **elemen pertama** yang cocok dengan aturan **CSS Selector**.
* Sangat fleksibel karena bisa menyeleksi tag, class, id, maupun hierarki anak:
  - Class: `document.querySelector(".deskripsi")` (Wajib pakai titik `.`)
  - ID: `document.querySelector("#judul-utama")` (Wajib pakai pagar `#`)
  - Tag: `document.querySelector("p")` (Mengambil paragraf pertama yang ditemukan)
  - Hierarki: `document.querySelector(".card button")`
* Jika elemen tidak ditemukan di halaman, ia akan mengembalikan nilai **`null`**.

```javascript
// Mengambil tombol pertama di dalam elemen ber-class .card
const tombolAksi = document.querySelector(".card .btn-aksi");
```

---

### C. `document.querySelectorAll("selectorCSS")`
* Memilih **SEMUA elemen** yang cocok dengan CSS selector tersebut.
* Mengembalikan kumpulan elemen dalam bentuk **NodeList** (daftar elemen yang sangat mirip dengan Array).
* NodeList bisa langsung diulang menggunakan method **`.forEach()`**!

```javascript
// HTML: <li class="item">Satu</li> <li class="item">Dua</li>
const semuaItem = document.querySelectorAll(".item");

console.log(semuaItem.length); // Mengetahui berapa banyak elemen yang ditemukan

// Mengulang setiap elemen yang ditemukan:
semuaItem.forEach((el, index) => {
  console.log(`Elemen ke-${index + 1}:`, el.textContent);
});
```

---

## 3. Jebakan Pemula (Common Gotchas)

1. **Lupa Tanda `.` atau `#` pada `querySelector`**:
   ```javascript
   // ❌ SALAH: dianggap mencari tag <card></card>
   document.querySelector("card");

   // ✅ BENAR: mencari class="card"
   document.querySelector(".card");
   ```

2. **Memperlakukan Hasil `querySelectorAll` Seperti Satu Elemen**:
   `querySelectorAll` menghasilkan kumpulan (*NodeList*), bukan satu tag tunggal.
   ```javascript
   const semuaParagraf = document.querySelectorAll("p");

   // ❌ SALAH: NodeList tidak punya properti textContent langsung
   semuaParagraf.textContent = "Ubah Teks"; 

   // ✅ BENAR: Akses dengan indeks atau perulangan forEach
   semuaParagraf[0].textContent = "Paragraf Pertama Diubah";
   semuaParagraf.forEach(p => p.style.color = "blue");
   ```

---

## Rangkuman Pemilihan Metode

| Kebutuhan | Metode Terbaik | Nilai Kembalian |
| :--- | :--- | :--- |
| Cari 1 elemen via ID unik | `getElementById("id")` | 1 Elemen HTML atau `null` |
| Cari 1 elemen via Class/Tag pertama | `querySelector(".class")` | 1 Elemen HTML atau `null` |
| Cari banyak elemen sekaligus | `querySelectorAll(".class")` | **NodeList** (kumpulan elemen) |
