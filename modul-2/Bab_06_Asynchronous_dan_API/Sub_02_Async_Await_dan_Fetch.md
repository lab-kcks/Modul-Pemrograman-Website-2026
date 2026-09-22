# Sub-Bab 02: Modern `async` / `await`, `fetch()` API, & `try...catch`

Setelah memahami konsep asynchronous di Sub-Bab 01, sekarang kita masuk ke standar industri modern untuk mengambil data dari internet: perpaduan **`fetch()` API**, kata kunci **`async` / `await`**, dan pengaman error **`try...catch`**.

---

## 1. Apa Itu `fetch()` API?

**`fetch()`** adalah fungsi bawaan browser modern yang digunakan untuk mengirim permintaan HTTP (seperti mengambil data, mengirim data) ke sebuah URL API di internet.

Secara default, `fetch("url")` akan melakukan metode **HTTP GET** (meminta/mengambil data).

---

## 2. Kata Kunci `async` dan `await`

Di masa lalu, pengembang web menggunakan rantai panjang `.then()`. Sintaksis modern ES6+ memperkenalkan **`async`** dan **`await`**, yang membuat kode asynchronous bisa ditulis dan dibaca lurus dari atas ke bawah seperti kode biasa!

* **`async`**: Wajib diletakkan di awal deklarasi fungsi untuk memberi tahu JavaScript bahwa di dalam fungsi ini terdapat operasi yang butuh waktu (asynchronous).
* **`await`**: Hanya boleh digunakan **di dalam fungsi `async`**. Berfungsi untuk menahan jalannya baris kode berikutnya di dalam fungsi tersebut sampai data dari `fetch()` selesai didapatkan.

---

## 3. Dua Langkah Mengambil Data dengan `fetch()`

Ketika mengambil data berformat JSON dari server, selalu ada **2 tahap berurutan**:

```javascript
async function ambilPengguna() {
  // Tahap 1: Hubungi server dan tunggu respon paket datang
  const respon = await fetch("https://jsonplaceholder.typicode.com/users");

  // Tahap 2: Buka isi paket dan terjemahkan teks JSON menjadi Array/Object JavaScript
  const dataPengguna = await respon.json();

  console.log("Data berhasil diambil:", dataPengguna);
}

// Panggil fungsinya:
ambilPengguna();
```

---

## 4. Penanganan Error Tangguh dengan `try...catch`

Koneksi internet bisa putus, URL bisa salah ketik, atau server backend bisa saja sedang mati/down. Jika hal itu terjadi tanpa pengaman, aplikasi JavaScript Anda akan berhenti bekerja (*crash*).

Untuk mengamankannya, bungkus operasi `fetch` di dalam blok **`try...catch`**:

```javascript
async function ambilDataAman() {
  try {
    // 1. Coba jalankan proses pengambilan data di dalam blok 'try'
    const respon = await fetch("https://jsonplaceholder.typicode.com/posts/1");

    // Periksa apakah status HTTP sukses (kode 200 - 299)
    if (!respon.ok) {
      throw new Error(`Gagal memuat data! Status: ${respon.status}`);
    }

    const data = await respon.json();
    console.log("Judul Post:", data.title);

  } catch (error) {
    // 2. Jika ada masalah (koneksi mati/URL rusak), tangani di blok 'catch':
    console.error("Terjadi Kesalahan:", error.message);
  }
}
```

---

## Rangkuman Singkat

| Bagian | Peran |
| :--- | :--- |
| `async` | Menandai bahwa fungsi memuat proses asynchronous |
| `await` | Menunggu janji (*Promise*) selesai sebelum melanjutkan baris berikutnya |
| `fetch(url)` | Menghubungi alamat API internet |
| `respon.json()` | Menerjemahkan body respon mentah menjadi Object/Array JavaScript |
| `try { ... } catch (err) { ... }` | Mengamankan program agar tidak crash jika internet/server bermasalah |

---
*Silakan buka berkas [Praktik_Bab_06.html](Praktik_Bab_06.html) untuk mencoba langsung request ke API publik nyata.*
