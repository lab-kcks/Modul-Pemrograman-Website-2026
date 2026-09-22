# Sub-Bab 03: Web Storage (`localStorage` & JSON)

Hingga saat ini, setiap kali halaman web di-refresh (tekan F5) atau tab ditutup, seluruh variabel dan data di memori JavaScript akan **hilang seketika kembali ke kondisi awal**.

Bagaimana cara membuat data tetap tersimpan abadi di browser pengguna (seperti to-do list, preferensi Dark Mode, atau status login)? Jawabannya adalah **`localStorage`**.

---

## 1. Apa Itu `localStorage`?

**`localStorage`** adalah fitur penyimpanan bawaan browser yang memungkinkan Anda menyimpan data langsung di perangkat komputer pengguna.

### Karakteristik Utama:
* **Persisten (Abadi)**: Data tidak akan terhapus meskipun browser ditutup, laptop dimatikan, atau halaman di-refresh.
* **Kapasitas Cukup Besar**: Mampu menampung sekitar 5 MB data per domain/website (sangat besar untuk data teks).
* **Format Penyimpanan**: Berbasis pasangan **Key-Value** (Kunci dan Nilai).
* **Syarat Mutlak**: Nilai (*value*) yang disimpan **WAJIB berupa String (Teks)**.

---

## 2. Empat Perintah Dasar (CRUD) `localStorage`

JavaScript menyediakan 4 method yang sangat sederhana untuk mengelola `localStorage`:

### A. Menyimpan Data (`.setItem`)
```javascript
// localStorage.setItem("namaKunci", "isiNilaiString")
localStorage.setItem("namaPengguna", "Hikari");
localStorage.setItem("tema", "gelap");
```

### B. Membaca Data (`.getItem`)
```javascript
const user = localStorage.getItem("namaPengguna");
console.log(user); // "Hikari"

// Jika kunci tidak ditemukan di memori, hasilnya adalah null:
const skor = localStorage.getItem("skorGame");
console.log(skor); // null
```

### C. Menghapus Satu Data Tertentu (`.removeItem`)
```javascript
localStorage.removeItem("tema"); // Kunci 'tema' dihapus
```

### D. Menghapus Seluruh Data Website (`.clear`)
```javascript
localStorage.clear(); // Seluruh data yang disimpan oleh website ini dihapus bersih
```

---

## 3. Masalah Fatal: Menyimpan Array & Object

Karena `localStorage` **hanya bisa menyimpan teks string murni**, perhatikan apa yang terjadi jika Anda menyimpan Object atau Array secara langsung:

```javascript
const dataUser = { nama: "Hikari", semester: 5 };

// ❌ CARA SALAH:
localStorage.setItem("profil", dataUser);

console.log(localStorage.getItem("profil")); 
// Hasilnya: "[object Object]" (DATANYA RUSAK & TIDAK BISA DIBACA LAGI!)
```

---

## 4. Solusi Emas: Duet `JSON.stringify()` & `JSON.parse()`

Untuk menyimpan Array atau Object tanpa merusaknya, kita wajib menggunakan format **JSON (JavaScript Object Notation)**:

### 1. Sebelum Menyimpan &rarr; Ubah ke String dengan `JSON.stringify()`
```javascript
const daftarTugas = ["Belajar HTML", "Belajar CSS", "Belajar JS"];

// Ubah Array menjadi teks string JSON:
const dataTeks = JSON.stringify(daftarTugas);
// dataTeks sekarang berwujud: '["Belajar HTML","Belajar CSS","Belajar JS"]'

localStorage.setItem("tugasKu", dataTeks); // Aman tersimpan!
```

### 2. Saat Mengambil &rarr; Kembalikan ke Bentuk Asli dengan `JSON.parse()`
```javascript
// Ambil teks string JSON dari storage:
const dataTeksAmbil = localStorage.getItem("tugasKu");

// Terjemahkan kembali menjadi Array JavaScript:
const tugasAsli = JSON.parse(dataTeksAmbil);

console.log(tugasAsli[0]); // "Belajar HTML" (Bisa diakses seperti array normal kembali!)
```

### 💡 Pola Standar Industri (Fallback Data Kosong):
Jika aplikasi baru pertama kali dibuka, data di `localStorage` masih bernilai `null`. Agar program tidak error, pasang nilai cadangan (*fallback*) menggunakan operator `||`:

```javascript
// Jika ada data di storage, ambil. Jika belum ada (null), pakai array kosong []
const listTugas = JSON.parse(localStorage.getItem("tugasKu")) || [];
```

---

## Rangkuman Singkat

| Perintah | Fungsi |
| :--- | :--- |
| `localStorage.setItem("kunci", nilai)` | Menyimpan/memperbarui data |
| `localStorage.getItem("kunci")` | Membaca data (menghasilkan string atau `null`) |
| `localStorage.removeItem("kunci")` | Menghapus 1 data spesifik |
| `localStorage.clear()` | Mengosongkan seluruh data storage |
| `JSON.stringify(object/array)` | Mengubah Object/Array menjadi Teks String JSON |
| `JSON.parse(teksJSON)` | Mengembalikan Teks JSON menjadi Object/Array asli |
