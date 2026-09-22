# Sub-Bab 03: Perulangan Praktis (`for`, `while`, `break`, & `continue`)

Dalam pemrograman, sering kali kita perlu melakukan tugas yang sama berkali-kali—misalnya: menampilkan 100 data produk, menghitung rata-rata nilai sekelas, atau mengecek saldo secara berkala.

Daripada menulis baris kode yang sama 100 kali, kita menggunakan **Perulangan (Looping)**.

---

## 1. `for` Loop (Perulangan dengan Jumlah Putaran Pasti)

`for` loop adalah jenis perulangan yang paling sering digunakan jika Anda **sudah tahu berapa kali perulangan harus berjalan**.

Sintaks terdiri dari 3 bagian di dalam tanda kurung:
```javascript
for (let i = 1; i <= 5; i++) {
  console.log(`Putaran ke-${i}`);
}
```

* **Inisialisasi (`let i = 1`)**: Dijalankan **sekali** di awal sebagai titik mulai counter.
* **Kondisi Batas (`i <= 5`)**: Dicek sebelum setiap putaran dimulai. Jika `true`, blok `{}` dijalankan; jika `false`, loop langsung berhenti.
* **Perubahan Nilai (`i++`)**: Dijalankan di akhir setiap putaran untuk menambah nilai `i` (sama dengan `i = i + 1`).

---

## 2. `while` Loop (Perulangan Berdasarkan Kondisi)

`while` loop digunakan jika Anda **tidak tahu pasti berapa kali putaran yang dibutuhkan**, asalkan suatu kondisi masih bernilai `true`.

```javascript
let baterai = 100;

while (baterai > 20) {
  console.log(`Baterai masih cukup: ${baterai}%`);
  baterai -= 30; // Mengurangi baterai 30% setiap putaran
}

console.log("Baterai lemah! Perlu dicharge.");
```

> [!WARNING]
> **Bahaya Infinite Loop (Looping Tanpa Henti):**  
> Pastikan variabel kondisi selalu berubah di dalam blok `while` (misal: ada `baterai -= 30`). Jika kondisinya selalu `true` selamanya, browser akan *freeze* / *hang*.

---

## 3. Pengendali Alur Loop: `break` dan `continue`

Kadang di tengah putaran, kita ingin menghentikan loop lebih awal atau melompati putaran tertentu:

### A. `break` (Berhenti Total)
Memaksa perulangan untuk **berhenti seketika dan langsung keluar dari loop**.
```javascript
for (let i = 1; i <= 10; i++) {
  if (i === 4) {
    break; // Langsung berhenti total saat i bernilai 4
  }
  console.log(i); // Hanya mencetak: 1, 2, 3
}
```

### B. `continue` (Lewati Putaran Ini)
Hanya **melompati sisa kode pada putaran saat ini**, lalu langsung lanjut ke putaran berikutnya.
```javascript
for (let i = 1; i <= 5; i++) {
  if (i === 3) {
    continue; // Putaran 3 dilewati, angka 3 tidak dicetak!
  }
  console.log(i); // Mencetak: 1, 2, 4, 5
}
```

---

## 4. Modern JS: Perulangan Data dengan `for...of`

Di JavaScript modern, jika Anda ingin membaca isi dari sebuah daftar (Array), cara paling bersih dan mudah dibaca adalah menggunakan `for...of`:

```javascript
const daftarBahasa = ["HTML", "CSS", "JavaScript"];

// Membaca satu per satu item langsung:
for (const bahasa of daftarBahasa) {
  console.log(`Sedang belajar: ${bahasa}`);
}
```

---

*Silakan buka berkas [Praktik_Bab_02.html](Praktik_Bab_02.html) untuk melihat pembuktian eksekusi perulangan ini.*
