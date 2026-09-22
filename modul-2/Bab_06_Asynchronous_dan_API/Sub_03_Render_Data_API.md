# Sub-Bab 03: Rendering Data API ke Antarmuka DOM

Di sub-bab terakhir ini, kita akan menggabungkan seluruh keahlian yang telah dipelajari sejak Bab 01 hingga Bab 06:
1. Mengambil data dari internet via **`fetch()`** (Bab 06).
2. Memproses array data menggunakan **`.forEach()` / `.map()` / `.filter()`** (Bab 03).
3. Me-render kartu/elemen visual baru ke dalam halaman web via **DOM Manipulation** (Bab 04).

---

## 1. Tiga Status Tampilan (UI States) yang Wajib Dikelola

Ketika aplikasi Anda memuat data dari internet, waktu yang dibutuhkan tidak pernah instan (bisa 0.5 detik hingga beberapa detik tergantung kecepatan koneksi).

Aplikasi web profesional wajib mengelola **3 status antarmuka (UI States)**:

1. **Status Memuat (*Loading State*)**:
   - Tampilkan teks *"Sedang memuat data..."* atau animasi loading tepat saat tombol ditekan atau halaman dibuka.
   - Ini penting agar pengguna tahu aplikasi sedang bekerja dan tidak mengira websitenya macet.
2. **Status Berhasil (*Success State*)**:
   - Hapus teks loading, lalu tampilkan kartu-kartu data yang berhasil diambil.
3. **Status Gagal (*Error State*)**:
   - Jika koneksi gagal, tampilkan pesan peringatan ramah (misal: *"Gagal terhubung ke server. Periksa koneksi internet Anda."*).

---

## 2. Pola Alur Kode Lengkap di Industri

Berikut adalah arsitektur kode standar untuk mengambil data API dan menampilkannya ke layar:

```javascript
async function muatDaftarPengguna() {
  const kontainer = document.querySelector("#wadah-kartu");

  // 1. Tampilkan status Loading
  kontainer.innerHTML = "<p class='loading'>Sedang memuat data dari server...</p>";

  try {
    // 2. Ambil data dari API publik
    const respon = await fetch("https://jsonplaceholder.typicode.com/users");
    if (!respon.ok) throw new Error("Gagal mengambil respon dari server");

    const daftarUser = await respon.json();

    // 3. Kosongkan wadah loading
    kontainer.innerHTML = "";

    // 4. Render setiap data menjadi elemen kartu DOM
    daftarUser.forEach(user => {
      const card = document.createElement("div");
      card.className = "card-user";
      card.innerHTML = `
        <h3>${user.name}</h3>
        <p>Email: ${user.email}</p>
        <p>Kota: ${user.address.city}</p>
      `;
      kontainer.append(card);
    });

  } catch (error) {
    // 5. Tampilkan status Error jika koneksi gagal
    kontainer.innerHTML = `<p class='error'>Terjadi kesalahan: ${error.message}</p>`;
  }
}
```

---

## 3. Integrasi dengan Fitur Pencarian / Filter (Bab 03)

Setelah data berhasil diambil dari API dan disimpan di sebuah variabel array, Anda bisa langsung menyaringnya secara instan menggunakan method **`.filter()`** yang telah kita pelajari di Bab 03:

```javascript
// Menyaring pengguna yang berasal dari kota tertentu:
const userKotaTertentu = semuaUser.filter(u => u.address.city === "Gwenborough");
```

---

## Rangkuman Capstone Pembelajaran

Selamat! Anda kini telah menguasai alur lengkap pengembangan web modern:
* **Sintaksis & Variabel** (`let`, `const`, scope)
* **Logika & Fungsi** (`if/else`, ternary `? :`, arrow function `() => {}`)
* **Struktur Data** (Array, Object, Array of Objects, `map`, `filter`)
* **DOM Manipulation** (Seleksi elemen, `classList`, `createElement`, `remove`)
* **Event Handling & Storage** (Event delegation, `e.preventDefault()`, `localStorage`)
* **Asynchronous & Network** (`fetch`, `async/await`, `try/catch`, render API)

---
*Silakan buka berkas [Praktik_Bab_06.html](Praktik_Bab_06.html) untuk mencoba langsung live fetching dan rendering data API publik.*
