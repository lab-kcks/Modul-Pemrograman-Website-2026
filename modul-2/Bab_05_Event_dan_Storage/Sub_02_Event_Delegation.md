# Sub-Bab 02: Event Delegation (Menangani Elemen Dinamis)

Di Bab 04 Sub-Bab 03, kita belajar membuat elemen baru secara dinamis (seperti membuat kartu atau item to-do baru). Namun ada satu masalah besar dalam JavaScript jika kita tidak berhati-hati: **Bagaimana cara menangani event pada elemen yang baru lahir tersebut?**

Solusi terbaik dan standar emas di industri adalah teknik **Event Delegation**.

---

## 1. Masalah: Mengapa Memasang Listener Satu per Satu Itu Buruk?

Bayangkan Anda memiliki aplikasi dengan 1.000 daftar barang, dan setiap barang memiliki tombol "Hapus":

* **Cara Buruk (Tanpa Delegation)**:
  Anda memasang 1.000 `addEventListener` ke masing-masing tombol.
  👉 **Dampaknya:** Memori RAM browser boros, dan setiap kali ada barang baru yang ditambahkan, Anda harus ingat untuk memasang listener baru lagi secara manual.

* **Cara Cerdas (Event Delegation)**:
  Anda **HANYA MEMASANG 1 LISTENER SAJA pada elemen INDUK (Wadah)**, lalu biarkan wadah tersebut mengelola klik dari semua anak-anaknya!

---

## 2. Rahasia di Balik Layar: *Event Bubbling*

Bagaimana bisa elemen induk mendengar klik pada anak-anaknya?

Karena JavaScript memiliki mekanisme bawaan bernama **Event Bubbling (Penggelembungan Event)**.
Saat sebuah tombol anak diklik, sinyal event klik tersebut akan **menggelembung naik ke atas** seperti gelembung udara di air:

```text
[Klik Mouse] 
    │
    ▼
<button class="btn-hapus"> (Elemen Asli yang Diklik)
    │  (Sinyal menggelembung naik ke atas...)
    ▼
<li class="item">
    │  (Naik lagi...)
    ▼
<ul id="wadah-daftar">   ◄─── KITA PASANG LISTENER HANYA DI SINI!
    │  (Naik lagi...)
    ▼
<body> -> <html> -> document
```

---

## 3. Cara Menerapkan Event Delegation

Cukup pasang satu listener pada wadah induk, lalu gunakan **`e.target`** untuk memeriksa siapa elemen yang sebenarnya diklik:

```javascript
const wadah = document.querySelector("#daftar-tugas");

// Pasang HANYA SATU listener pada wadah induk:
wadah.addEventListener("click", (e) => {
  
  // Periksa: Apakah yang diklik adalah tombol yang punya class 'btn-hapus'?
  if (e.target.classList.contains("btn-hapus")) {
    
    // Ambil elemen <li> induk terdekatnya, lalu hapus:
    const itemTugas = e.target.closest("li");
    itemTugas.remove();
    
    console.log("Tugas berhasil dihapus lewat Event Delegation!");
  }
  
});
```

---

## 4. Method Sakti: `e.target.closest()`

Terkadang di dalam tombol ada ikon atau teks span:
`<button class="btn-hapus"><span>Hapus</span></button>`

Jika pengguna mengklik teks `<span>`, maka `e.target` akan berisi `<span>`, bukan `<button>`.

Untuk mengatasi hal ini, JavaScript menyediakan method **`.closest("selector")`**:
* Method ini akan mencari ke atas menuju elemen leluhur terdekat yang cocok dengan selector tersebut.
* Sangat aman dan andal untuk mengambil elemen pembungkus (seperti `<li>` atau `.card`).

---

## Rangkuman Keuntungan Event Delegation

1. **Hemat Memori (Performa Tinggi)**: Cukup 1 event listener di induk untuk menangani ratusan hingga ribuan elemen anak.
2. **Otomatis untuk Elemen Baru**: Elemen apa pun yang baru ditambahkan ke dalam wadah di masa depan akan **langsung aktif dan bisa diklik secara otomatis** tanpa perlu dipasangi listener tambahan!
