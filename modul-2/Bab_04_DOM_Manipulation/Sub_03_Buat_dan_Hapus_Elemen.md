# Sub-Bab 03: Membuat & Menghapus Elemen Dinamis (Dynamic Elements)

Di aplikasi web nyata (seperti Instagram, Trello, atau aplikasi Chat), postingan baru atau kartu tugas tidak ditulis secara statis di dalam file HTML. Elemen-elemen tersebut **dibuat dan dihapus secara dinamis** saat pengguna berinteraksi.

---

## 1. Tiga Langkah Membuat Elemen Baru (`createElement` & `append`)

Untuk memunculkan elemen baru ke halaman web menggunakan standar JavaScript yang benar, ada 3 langkah berurutan:

### Langkah 1: Buat Elemen di Memori (`document.createElement`)
Gunakan `document.createElement("namaTag")` untuk melahirkan elemen baru di memori browser.
```javascript
const kartuBaru = document.createElement("div");
```
> *Catatan: Pada tahap ini, elemen baru masih berada di memori JavaScript dan **belum terlihat di layar** karena belum ditempelkan ke pohon dokumen (DOM).*

### Langkah 2: Lengkapi Teks, Class, & Atributnya
Berikan isi dan gaya pada elemen baru tersebut sebelum ditampilkan:
```javascript
kartuBaru.textContent = "Ini adalah kartu tugas baru";
kartuBaru.classList.add("card-item");
```

### Langkah 3: Tempelkan ke Elemen Induk (`parentElement.append`)
Pilih elemen wadah/induk yang sudah ada di halaman, lalu tempelkan elemen baru ke dalamnya:
```javascript
const kontainer = document.getElementById("daftar-tugas");

// Tempelkan kartuBaru sebagai anak di dalam kontainer:
kontainer.append(kartuBaru);
```

---

## 2. Mengapa Memakai `createElement` dan Bukan `innerHTML +=`?

Mungkin Anda bertanya: *"Kenapa harus repot membuat elemen lewat 3 langkah di atas? Kenapa tidak pakai operator penambahan string teks `wadah.innerHTML += '<div>...</div>'` saja yang lebih pendek?"*

Mari kita bandingkan:

### Cara 1: Menambah via `innerHTML +=` (Trik Sambung String Teks)
Operator `+=` artinya menyambung teks:
```javascript
// Mengambil isi lama, menyambungnya dengan teks baru, lalu merender ulang seluruh wadah
wadah.innerHTML += "<li>Tugas Baru</li>";
```
**Kelemahan Fatal:**  
Setiap kali `innerHTML +=` dipanggil, browser akan **menghancurkan seluruh elemen anak yang lama**, lalu merender ulang semuanya dari awal dari bentuk teks mentah. Akibatnya:
- Kinerja lambat jika datanya banyak.
- **Event listener yang sudah dipasang pada tombol-tombol lama akan hilang dan mati!**

### Cara 2: Menambah via `createElement()` + `append()` (Standar Objek DOM Asli)
```javascript
const liBaru = document.createElement("li");
liBaru.textContent = "Tugas Baru";
wadah.append(liBaru);
```
**Keunggulan Utama:**  
Browser **hanya menciptakan satu elemen baru** dan menempelkannya di posisi paling akhir. Elemen-elemen lama di dalam wadah tidak terganggu, tidak dirender ulang, dan semua tombol lamanya tetap aktif berfungsi normal.

---

## 3. Menghapus Elemen dari Halaman

Setelah bisa membuat elemen, ada dua cara umum untuk menghapusnya:

### A. Menghapus Elemen Tertentu Secara Langsung (`element.remove()`)
Cara paling modern, singkat, dan bersih untuk menghapus satu elemen spesifik saat tombol hapus diklik:
```javascript
const kartu = document.getElementById("kartu-1");
kartu.remove(); // Kartu langsung lenyap dari layar
```

### B. Mengosongkan Seluruh Isi Wadah (`innerHTML = ""`)
Jika Anda ingin menghapus seluruh anak di dalam wadah sekaligus (misal untuk tombol *Reset Semua* / *Clear All*):
```javascript
const daftar = document.getElementById("daftar-tugas");
daftar.innerHTML = ""; // Mengosongkan wadah menjadi string hampa
```

---

## Rangkuman Metode Utama

| Kebutuhan | Method Terbaik | Keterangan |
| :--- | :--- | :--- |
| **Melahirkan elemen baru** | `document.createElement("tag")` | Elemen tercipta di memori |
| **Menempelkan ke halaman** | `parent.append(child)` | Ditempelkan ke dalam elemen induk |
| **Menghapus 1 elemen** | `element.remove()` | Menghapus elemen itu sendiri |
| **Mengosongkan semua anak**| `parent.innerHTML = ""` | Menghapus bersih seluruh isi wadah |
