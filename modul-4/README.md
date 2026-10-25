# Modul 4 - Express JS & Database Cloud

> Stack: **Node.js + Express 5 + TypeScript + Prisma ORM 7 + PostgreSQL (Neon Cloud)**

<div align="center">
  <img src="images/nodeexpress.png" alt="Node.js dan Express" width="160" height="120" style="object-fit: contain;">
  <img src="images/ts.png" alt="TypeScript" width="160" height="120" style="object-fit: contain;">
  <img src="images/prisma.png" alt="Prisma ORM" width="160" height="120" style="object-fit: contain;">
  <img src="images/neon.png" alt="Neon PostgreSQL" width="160" height="120" style="object-fit: contain;">
</div>


---

## 1. Tujuan Pembelajaran & Prasyarat

Setelah mengikuti modul ini, kalian diharapkan mampu:

- Menjelaskan alur sebuah request dari client sampai database dan kembali lagi.
- Membuat REST API dengan **Express 5 + TypeScript** (router, middleware, request, response).
- Menjelaskan perbedaan database lokal dan **database cloud**, serta membuat database di **Neon**.
- Memodelkan data dengan **Prisma schema**, melakukan **migrasi**, dan membuat operasi **CRUD**.
- Menggunakan **filtering, pagination, aggregate, transaction, dan raw query**.

### Prasyarat

| Kebutuhan | Versi | Keterangan |
| --- | --- | --- |
| Node.js | **24 LTS** atau **26** | Prisma 7 membutuhkan minimal Node 20.19 / 22.12 / 24. Node 26 resmi menjadi LTS pada 28 Oktober 2026 |
| npm | bawaan Node | `yarn` / `pnpm` boleh dipakai |
| Akun Neon | gratis | Tidak perlu kartu kredit |
| Postman / Apidog / Hoppscotch | bebas | Untuk menguji API |
| Pemahaman TypeScript dasar | | Materi Modul 3 |

Cek versi Node kalian:

```bash
node -v
npm -v
```

> [!NOTE]
> Mulai Node.js 27 (rilis April 2027), Node.js hanya merilis **satu versi mayor per tahun** dan semuanya menjadi LTS. Untuk praktikum ini cukup gunakan Node 24 atau 26.

---

## 2. Gambaran Besar

Sebelum menulis kode, pahami dulu **siapa bicara dengan siapa**. Semua materi di modul ini hanyalah bagian-bagian dari gambar berikut.

```mermaid
flowchart LR
    C["Client<br/>Postman / Browser / Frontend"] -->|"1. HTTP request"| E["Express 5<br/>Node.js + TypeScript"]
    E -->|"2. panggil method"| P["Prisma Client<br/>+ driver adapter"]
    P -->|"3. SQL lewat internet"| N[("Neon<br/>PostgreSQL di cloud")]
    N -->|"4. baris data"| P
    P -->|"5. objek TypeScript"| E
    E -->|"6. JSON response"| C
```

### Alur sebuah request (versi detail)

Diagram ini akan sering kita rujuk di bagian-bagian berikutnya.

```mermaid
sequenceDiagram
    autonumber
    participant C as Client (Postman)
    participant M as Middleware
    participant R as Router
    participant K as Controller
    participant P as Prisma Client
    participant D as Neon (PostgreSQL)

    C->>M: GET /api/users/1
    M->>R: logger, express.json(), lalu next()
    R->>K: cocokkan route /api/users/:id
    K->>P: prisma.user.findUnique(...)
    P->>D: SELECT ... WHERE id = 1
    D-->>P: 1 baris data
    P-->>K: objek user
    K-->>C: 200 OK, JSON (status, message, data)
```

---

## 3. ExpressJS: Inisiasi Project

**ExpressJS** adalah framework NodeJS yang populer untuk membuat aplikasi web dan API. Dengan Express, developer tidak perlu membuat back-end _from scratch_ dan bisa langsung fokus pada _business logic_.

| Tanpa framework (modul `http` bawaan Node) | Dengan Express |
| --- | --- |
| Cek `req.method` dan `req.url` dengan `if` bertingkat | `app.get("/users", ...)` |
| Parsing body JSON secara manual | `app.use(express.json())` |
| Kirim header dan status satu per satu | `res.status(200).json(...)` |

### 3.1 Package manager (opsional)

`npm` sudah ikut terinstal bersama Node. Kalian boleh memakai package manager lain, perintahnya diinstal lewat `npm`.

```bash
# Yarn (opsional)
npm install -g yarn
yarn -v

# PNPM (opsional)
npm install -g pnpm
pnpm -v
```

> Flag `-g` membuat tool bisa dipakai di folder mana pun.

Di modul ini contoh perintah ditulis dengan `npm`. Padanannya:

| npm | yarn | pnpm |
| --- | --- | --- |
| `npm install <pkg>` | `yarn add <pkg>` | `pnpm add <pkg>` |
| `npm install -D <pkg>` | `yarn add -D <pkg>` | `pnpm add -D <pkg>` |
| `npm run <script>` | `yarn <script>` | `pnpm <script>` |

### 3.2 Inisiasi project Express + TypeScript

**Langkah 1.** Buat folder project, lalu inisiasi `package.json` dengan package manager pilihan kalian.

```bash
mkdir modul-4
cd modul-4

# Menggunakan npm
npm init

# Menggunakan yarn
yarn init

# Menggunakan pnpm
pnpm init
```

Kalian akan diberi beberapa pertanyaan. Jawaban dari pertanyaan ini **tidak memengaruhi** jalannya project, jadi boleh tekan Enter terus. Atau tambahkan flag `-y` (`npm init -y`) untuk langsung menerima semua nilai default.

| Pertanyaan | Isi `package.json` | Arti |
| --- | --- | --- |
| package name | `name` | Nama project (huruf kecil, tanpa spasi) |
| version | `version` | Versi project |
| description | `description` | Deskripsi singkat |
| entry point | `main` | File utama |
| test command | `scripts.test` | Perintah untuk menjalankan test |
| git repository | `repository` | Alamat repo GitHub |
| keywords, author, license | `keywords`, `author`, `license` | Informasi tambahan |

`package.json` adalah "KTP" project: berisi identitas, daftar dependensi, dan **script** yang bisa dijalankan dengan `npm run <nama>`.

**Langkah 2.** Aktifkan mode ESM. Prisma 7 hanya dibuat sebagai ES Module, jadi project kita juga harus ESM.

```bash
npm pkg set type=module
```

<!-- TODO (asisten): ganti dengan screenshot package.json terbaru, jika ingin -->

**Langkah 3.** Buat `.gitignore` agar file sensitif dan file hasil generate tidak masuk GitHub.

```gitignore
node_modules
dist
.env
src/generated
```

> [!WARNING]
> File `.env` akan berisi password database. **Jangan pernah** di-commit ke GitHub.

**Langkah 4.** Instal Express.

```bash
npm install express@5
```

**Langkah 5.** Instal dependensi development dengan flag `-D`.

```bash
npm install -D typescript tsx @types/node @types/express
```

| Package | Fungsi |
| --- | --- |
| `typescript` | Compiler dan type checker (`tsc`). Sekarang versi 7 yang ditulis ulang dengan Go sehingga jauh lebih cepat |
| `tsx` | Menjalankan file `.ts` langsung, plus mode `watch` (auto-restart saat file berubah). Menggantikan `ts-node` + `nodemon` |
| `@types/node`, `@types/express` | Definisi tipe agar editor dan `tsc` paham Node dan Express |

> **Dependencies vs devDependencies.** `dependencies` adalah package yang dibutuhkan aplikasi saat berjalan. `devDependencies` hanya dipakai saat pengembangan atau testing.

> [!NOTE]
> **Kenapa `tsx`, bukan `ts-node`?** `tsx` hanya menghapus tipe lalu menjalankan kode, **tanpa memeriksa tipe**. Pemeriksaan tipe dilakukan terpisah dengan `tsc --noEmit` (kita buat script `typecheck`). `ts-node` bergantung pada API compiler TypeScript, sedangkan TypeScript 7.0 belum menyediakan API stabil (direncanakan di 7.1).

**Langkah 6.** Buat `tsconfig.json`. Kalian tidak perlu lagi memakai `npx tsc --init` yang isinya panjang.

```json
{
  "compilerOptions": {
    "target": "ES2023",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "types": ["node"],
    "strict": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "skipLibCheck": true,
    "noEmit": true
  },
  "include": ["src", "prisma.config.ts"]
}
```

| Opsi | Arti |
| --- | --- |
| `module: ESNext` + `moduleResolution: bundler` | Pakai `import`/`export` modern; import boleh tanpa ekstensi (`"./routes"`). Sesuai panduan Prisma 7 |
| `types: ["node"]` | Sejak TypeScript 6, paket `@types/*` tidak lagi dimuat otomatis, jadi harus disebut |
| `strict: true` | Pengecekan tipe ketat (kebiasaan baik dari Modul 3) |
| `noEmit: true` | `tsc` hanya mengecek, tidak menghasilkan file `.js` |
| `include` | Folder/file yang diperiksa |

**Langkah 7.** Buat folder `src`, lalu file `src/index.ts`. Ini adalah file utama project.

```typescript
import express from "express";

const app = express();

app.use(express.json());

// check endpoint
app.get("/", (_req, res) => {
  res.status(200).send("Server is up and running 💫");
});

const PORT = 3000;
app.listen(PORT, (error?: Error) => {
  if (error) throw error; // Express 5: error saat listen (mis. port dipakai) dikirim ke callback
  console.log(`Express is running on Port ${PORT}`);
});
```

Sebelum lanjut, pahami baris-baris di `index.ts`. Setiap `app.get(...)` (dan method lain) punya anatomi yang sama:

```
app.get(  "/"  ,  (req, res) => { ... }  )
 │   │     │             │
 │   │     │             └─ handler: fungsi yang dijalankan saat request cocok
 │   │     └─ path: alamat endpoint
 │   └─ method HTTP (get, post, patch, put, delete)
 └─ instance Express yang dibuat dengan express()
```

| Baris | Fungsi |
| --- | --- |
| `const app = express()` | Membuat aplikasi Express |
| `app.use(express.json())` | Memasang [middleware](#4-middleware) yang mengubah body JSON menjadi `req.body` |
| `app.get("/", handler)` | Mendaftarkan endpoint `GET /` |
| `res.status(200).send(...)` | Mengirim response dengan status 200 |
| `const PORT = 3000` | "Pintu" tempat server menunggu request. Alamat lengkapnya `http://localhost:3000` |
| `app.listen(PORT, callback)` | Menyalakan server. Callback dipanggil saat server siap, atau saat gagal menyala |

> Parameter yang tidak dipakai diberi awalan garis bawah (`_req`) sebagai tanda "sengaja tidak dipakai".

**Langkah 8.** Tambahkan script pada `package.json`.

```json
"scripts": {
  "dev": "tsx watch src/index.ts",
  "start": "tsx src/index.ts",
  "typecheck": "tsc --noEmit"
}
```

| Script | Fungsi |
| --- | --- |
| `npm run dev` | Menjalankan server dan **restart otomatis** saat kode berubah |
| `npm run start` | Menjalankan server sekali |
| `npm run typecheck` | Memeriksa error tipe tanpa menjalankan server |

**Langkah 9.** Jalankan server.

```bash
npm run dev
```

**Langkah 10.** Buka `http://localhost:3000` di browser. Jika muncul tulisan _Server is up and running 💫_, selamat, endpoint pertama kalian sudah jalan.

<!-- TODO (asisten): screenshot hasil di browser -->

Struktur direktori saat ini:

```
modul-4/
├── node_modules/
├── src/
│   └── index.ts
├── .gitignore
├── package.json
├── package-lock.json
└── tsconfig.json
```

> Modul ini tidak membahas _deployment production_. Untuk sekarang, `tsx` sudah cukup.

---

## 4. Middleware

**Middleware** adalah fungsi yang berada **di tengah** antara request masuk dan handler. Setiap middleware bisa membaca/mengubah `req` dan `res`, lalu memanggil `next()` untuk meneruskan ke tahap berikutnya, atau langsung mengirim response.

Bayangkan middleware sebagai **pos pemeriksaan** yang harus dilewati request secara berurutan.

```mermaid
flowchart TD
    A([Request masuk]) --> B["logger<br/>catat request"]
    B --> C["express.json()<br/>ubah body JSON jadi req.body"]
    C --> D["express.urlencoded()<br/>baca body form"]
    D --> E{"Ada route<br/>yang cocok?"}
    E -->|"Ya"| F["Route handler / controller"]
    E -->|"Tidak"| G["notFound, kirim 404"]
    F -->|"res.json(...)"| I([Response terkirim])
    F -->|"throw / reject / next(err)"| H["errorHandler"]
    G --> I
    H --> I
```

Tiga hal penting:

1. **Urutan itu penting.** Middleware dijalankan dari atas ke bawah sesuai urutan `app.use(...)`. `express.json()` harus berada sebelum route yang membaca `req.body`.
2. **Lupa `next()`** membuat request menggantung (loading terus tanpa response).
3. **Error handler** memiliki **4 parameter** `(err, req, res, next)` dan diletakkan **paling bawah**.

### Contoh middleware buatan sendiri: logger

Buat file `src/middlewares/logger.ts`:

```typescript
// src/middlewares/logger.ts
import type { NextFunction, Request, Response } from "express";

// Mencatat setiap request: METHOD /path -> status (durasi)
export const logger = (req: Request, res: Response, next: NextFunction): void => {
  const start = Date.now();

  res.on("finish", () => {
    console.log(
      `${req.method} ${req.originalUrl} -> ${res.statusCode} (${Date.now() - start}ms)`,
    );
  });

  next(); // lanjut ke middleware / handler berikutnya
};
```

Pasang dengan `app.use(logger)`. Setiap request akan tercatat di terminal, misalnya:

```
GET /api/food/cookie -> 200 (7ms)
GET /nope -> 404 (2ms)
```

---

## 5. Express Router

**Express Router** memisahkan dan mengorganisasi rute API agar modular. Rute dikelompokkan berdasarkan fungsinya (users, posts, food, dan seterusnya).

| | Semua route di `index.ts` | Memakai Router |
| --- | --- | --- |
| Ukuran file | Makin panjang tiap ada fitur baru | Dipecah per topik, tiap file pendek |
| Mencari kode | Harus scroll panjang | Langsung buka file yang sesuai (`user.routes.ts`) |
| Kerja tim | Sering bentrok di satu file | Tiap orang bisa mengerjakan file berbeda |
| Prefix URL | Ditulis berulang (`/users`, `/users/:id`) | Cukup sekali lewat `router.use("/users", ...)` |

Struktur ini disebut _function based structure_: setiap kelompok endpoint punya file sendiri. Struktur yang akan kita bangun:

```mermaid
flowchart LR
    APP["app<br/>src/index.ts"] -->|"/api"| IDX["routes/index.ts"]
    IDX -->|"/food"| F["food.routes.ts"]
    IDX -->|"/users"| U["user.routes.ts"]
    IDX -->|"/posts"| PO["post.routes.ts"]
```

URL lengkap sebuah endpoint = **prefix dari atas ke bawah**. Contoh: `/api` + `/food` + `/pizza` = `GET /api/food/pizza`.

**Langkah 1.** Buat folder `src/routes/` lalu file `food.routes.ts`:

```typescript
// src/routes/food.routes.ts
import { Router } from "express";

const router = Router();

router.get("/pizza", (_, res) => {
  res.status(200).send("Mmm... Pizza... 🍕");
});

router.get("/cookie", (_, res) => {
  res.status(200).send("Get some Cookie... 🍪");
});

router.get("/donut", (_, res) => {
  res.status(200).send("Do Not... 🍩");
});

export default router;
```

**Langkah 2.** Satukan semua router di `src/routes/index.ts`. Untuk sementara baru ada `food`; router `users` dan `posts` akan kita tambahkan di [bagian 13](#13-crud-menggunakan-prisma).

```typescript
import { Router } from "express";
import foodRouter from "./food.routes";

const router = Router();

router.use("/food", foodRouter);

export default router;
```

**Langkah 3.** Pasang di `src/index.ts`:

```typescript
import express from "express";
import router from "./routes"; // import router

const app = express();

app.use(express.json());

app.get("/", (_req, res) => {
  res.status(200).send("Server is up and running 💫");
});

app.use("/api", router); // semua route di dalam router diawali /api

const PORT = 3000;
app.listen(PORT, (error?: Error) => {
  if (error) throw error;
  console.log(`Express is running on Port ${PORT}`);
});
```

**Langkah 4.** Coba buka `http://localhost:3000/api/food/pizza`, `/api/food/cookie`, dan `/api/food/donut`.

<!-- TODO (asisten): screenshot hasil endpoint food -->

### Urutan route juga penting

Express mencocokkan route **dari atas ke bawah** dan berhenti di yang pertama cocok.

```mermaid
flowchart LR
    R["GET /api/posts/stats"] --> Q1{"Cocok dengan<br/>GET /stats ?"}
    Q1 -->|"Ya (ditulis lebih dulu)"| OK["handler getPostStats"]
    Q1 -->|"Tidak"| Q2{"Cocok dengan<br/>GET /:id ?"}
    Q2 -->|"Ya, id = 'stats'"| BAD["handler getPostById<br/>(salah!)"]
```

Karena itu route statis seperti `/stats` **harus ditulis sebelum** route dinamis seperti `/:id`.

---

## 6. HTTP Method & CRUD

Express mendukung semua method HTTP. Method menjelaskan **apa yang ingin dilakukan** client terhadap sebuah data. Kita biasa memetakannya ke operasi **CRUD** (Create, Read, Update, Delete):

| Method | CRUD | Fungsi singkat | Memakai body? | Status sukses yang umum |
| --- | --- | --- | --- | --- |
| `GET` | **R**ead | Mengambil data | Tidak | `200` |
| `POST` | **C**reate | Menambah data baru | Ya | `201` |
| `PATCH` | **U**pdate sebagian | Mengubah sebagian field | Ya | `200` |
| `PUT` | **U**pdate seluruhnya | Mengganti data secara utuh | Ya | `200` |
| `DELETE` | **D**elete | Menghapus data | Tidak | `200` atau `204` |

Contoh kode di bawah ini hanya latihan memahami method (isinya belum ke database). Tambahkan `import type { Request, Response } from "express";` di bagian atas file, dan anggap `router` sudah dibuat dengan `Router()`.

### `GET`

Metode GET digunakan untuk **mengambil data** dari server. Biasanya dipakai untuk membaca atau mengakses data yang tersedia. GET tidak mengubah apa pun di server dan tidak membawa body.

```typescript
// GET semua user
router.get("/users", (_req: Request, res: Response) => {
  res.status(200).json({ message: "Retrieve all users" });
});

// GET satu user berdasarkan ID
router.get("/users/:id", (req: Request, res: Response) => {
  const { id } = req.params;
  res.status(200).json({ message: `Retrieve user with ID: ${id}` });
});
```

### `POST`

Metode POST digunakan untuk **mengirim data baru** ke server, biasanya untuk menambah data. Data yang dikirim berada di body. Jika berhasil, balas dengan `201 Created`.

```typescript
router.post("/users", (req: Request, res: Response) => {
  const { name, email } = req.body ?? {};
  res.status(201).json({ message: "User created", data: { name, email } });
});
```

### `PATCH`

Metode PATCH digunakan untuk **memperbarui sebagian** data pada sumber daya yang sudah ada. Gunakan ketika hanya sebagian field yang ingin diubah.

```typescript
router.patch("/users/:id", (req: Request, res: Response) => {
  const { id } = req.params;
  const { email } = req.body ?? {};
  res.status(200).json({ message: `User with ID: ${id} updated`, data: { email } });
});
```

### `PUT`

Metode PUT digunakan untuk **memperbarui data secara menyeluruh**. Seluruh data lama ditimpa dengan data baru yang dikirimkan.

```typescript
router.put("/users/:id", (req: Request, res: Response) => {
  const { id } = req.params;
  const { name, email } = req.body ?? {};
  res.status(200).json({ message: `User with ID: ${id} replaced`, data: { name, email } });
});
```

**PUT vs PATCH.** Misal user 1 punya `{ name: "Budi", email: "budi@mail.com" }`, lalu client mengirim `{ "email": "baru@mail.com" }`:

| Method | Hasil | Penjelasan |
| --- | --- | --- |
| `PATCH` | `{ name: "Budi", email: "baru@mail.com" }` | Hanya `email` yang berubah, `name` aman |
| `PUT` | `{ email: "baru@mail.com" }` | Data diganti utuh, jadi `name` ikut **hilang** |

Endpoint update di modul ini hanya mengubah field yang dikirim, jadi kita memakai **`PATCH`**. (Modul tahun lalu memakai `PUT` untuk hal yang sama, padahal itu kurang tepat.)

### `DELETE`

Metode DELETE digunakan untuk **menghapus data** dari server.

```typescript
router.delete("/users/:id", (req: Request, res: Response) => {
  const { id } = req.params;
  res.status(200).json({ message: `User with ID: ${id} deleted` });
});
```

### Sifat method: aman dan idempoten

| Method | Aman (tidak mengubah data)? | Idempoten (dikirim berkali-kali, hasil akhir sama)? |
| --- | --- | --- |
| `GET` | Ya | Ya |
| `POST` | Tidak | **Tidak**: dikirim 2 kali membuat 2 data |
| `PUT` | Tidak | Ya |
| `PATCH` | Tidak | Tidak selalu |
| `DELETE` | Tidak | Ya: setelah terhapus, menghapus lagi tidak mengubah apa-apa |

Inilah alasan kenapa tombol "Submit" yang tidak sengaja diklik dua kali bisa membuat data ganda, jika endpoint-nya `POST`.

---

## 7. Express Request

Objek **Request** (`req`) menyimpan semua informasi yang dikirim client. Supaya mudah, lihat anatomi sebuah request HTTP berikut dan perhatikan bagian mana yang diambil dengan properti apa.

```
POST /api/posts/42?draft=true HTTP/1.1          ← baris request: method + path + query
Host: localhost:3000                             ┐
Content-Type: application/json                   ├ HEADER  → req.headers
Authorization: Bearer eyJhbGciOi...              ┘
                                                 ← baris kosong
{ "title": "Halo", "authorId": 1 }               ← BODY    → req.body
```

| Bagian URL / request | Diambil dengan | Contoh nilai |
| --- | --- | --- |
| `/posts/`**`42`** (bagian dinamis path) | `req.params` | `{ id: "42" }` |
| `?`**`draft=true`** (setelah tanda tanya) | `req.query` | `{ draft: "true" }` |
| Header | `req.headers` | `req.headers["content-type"]` |
| Body (data yang dikirim) | `req.body` | `{ title: "Halo", authorId: 1 }` |

> [!IMPORTANT]
> Semua nilai `req.params` dan `req.query` bertipe **string**. Jika butuh angka, ubah dulu dengan `Number(...)` dan validasi hasilnya.

### Request Head

Header adalah metadata dalam pasangan key-value, misalnya `Content-Type` dan `Authorization`.

```typescript
router.get("/headers", (req: Request, res: Response) => {
  const contentType = req.headers["content-type"];
  const authorization = req.headers["authorization"];

  res.status(200).json({
    message: "Headers received",
    headers: { contentType, authorization },
  });
});
```

### Request Body

Body berisi data yang dikirim lewat `POST`, `PUT`, atau `PATCH`. Agar terbaca, pastikan `app.use(express.json())` sudah dipasang.

```typescript
router.post("/data", (req: Request, res: Response) => {
  const { name, email } = req.body ?? {};

  res.status(201).json({
    message: "Data received",
    data: { name, email },
  });
});
```

> [!WARNING]
> **Perbedaan Express 5:** jika body tidak ada atau tidak bisa di-parse, `req.body` bernilai **`undefined`** (di Express 4 bernilai `{}`). Mendestruktur `undefined` akan menyebabkan error, karena itu kita menulis `req.body ?? {}`.

### Request Param

Digunakan untuk bagian URL yang dinamis, misalnya ID.

```typescript
router.get("/users/:id", (req: Request, res: Response) => {
  const { id } = req.params;

  res.status(200).json({ message: `User with ID: ${id} found` });
});
```

### Request Query

Digunakan untuk filter, pencarian, atau pagination. Ditulis setelah tanda `?` dan dipisah dengan `&`.

```typescript
router.get("/search", (req: Request, res: Response) => {
  const { keyword, page, limit } = req.query;

  res.status(200).json({
    message: "Search results",
    filters: {
      keyword,
      page: Number(page) || 1,
      limit: Number(limit) || 10,
    },
  });
});
```

Contoh pemanggilan: `GET /search?keyword=eco&page=2&limit=5`.

> **Catatan Express 5:** `req.query` kini hanya bisa dibaca (getter), jadi `req.query.page = 1` tidak bisa lagi dilakukan. Wildcard pada path juga harus punya nama, misalnya `/files/*splat`, dan hasilnya berupa array.

---

## 8. Express Response & Error Handling

### 8.1 Objek Response dan method-nya

Objek **Response** (`res`) dipakai untuk **mengirim jawaban** dari server ke client setelah request diproses. Lewat objek ini kita menentukan data yang dikembalikan, kode status HTTP, tipe konten, dan header. Response bisa berupa JSON, HTML, atau teks biasa.

| Method | Fungsi | Contoh |
| --- | --- | --- |
| `res.status(kode)` | Menentukan kode status. Bisa dirangkai dengan method lain | `res.status(404)` |
| `res.json(objek)` | Mengirim **JSON** (header `Content-Type` diatur otomatis). Paling sering dipakai di API | `res.json({ status: true })` |
| `res.send(isi)` | Mengirim teks, HTML, atau data lain | `res.send("<h1>Halo</h1>")` |
| `res.sendStatus(kode)` | Mengirim status beserta teks standarnya | `res.sendStatus(204)` |
| `res.redirect(kode, url)` | Mengalihkan client ke URL lain | `res.redirect(302, "/api/food/pizza")` |
| `res.set(nama, nilai)` | Menambah header response | `res.set("Cache-Control", "no-store")` |

```typescript
router.get("/response-demo", (_req: Request, res: Response) => {
  res.set("Cache-Control", "no-store");
  res.status(200).json({ status: true, message: "Contoh response JSON" });
});

router.get("/response-text", (_req: Request, res: Response) => {
  res.status(200).send("<h1>Halo dari Express</h1>"); // dikirim sebagai HTML
});

router.get("/response-empty", (_req: Request, res: Response) => {
  res.sendStatus(204); // 204 No Content, tanpa body
});

router.get("/response-redirect", (_req: Request, res: Response) => {
  res.redirect(302, "/api/food/pizza"); // Express 5: kode status ditulis lebih dulu
});
```

**Hal penting yang perlu diingat:**

1. **Satu request hanya boleh dijawab satu kali.** Memanggil `res.json()` dua kali di request yang sama menyebabkan error `Cannot set headers after they are sent`. Di percabangan `if`, pastikan hanya satu jalur yang mengirim response (di modul ini kita menyelesaikannya dengan `throw` ke error handler).
2. **Urutan `res.status(...)` selalu lebih dulu**, baru `.json(...)` atau `.send(...)`.
3. **Pola lama yang dihapus di Express 5:**

| Express 4 (tidak berlaku lagi) | Express 5 |
| --- | --- |
| `res.json(obj, 201)` | `res.status(201).json(obj)` |
| `res.send(404)` | `res.sendStatus(404)` |
| `res.redirect(url, 301)` | `res.redirect(301, url)` |

### 8.2 HTTP Status Code

Kode status HTTP adalah angka yang menunjukkan hasil dari permintaan client. Kode ini membantu client memahami apakah permintaannya berhasil, dialihkan, atau mengalami error. Ada lima kategori, ditentukan dari angka pertamanya:

- **`2XX`: permintaan berhasil.** Dipakai saat data dikirim dengan benar atau operasi selesai dengan sukses.
- **`3XX`: permintaan harus dialihkan.** Dipakai untuk redirect, misalnya saat konten pindah ke URL baru.
- **`4XX`: kesalahan di sisi client.** Permintaan tidak bisa diproses karena masalah dari client, seperti autentikasi gagal atau data tidak valid.
- **`5XX`: kesalahan di sisi server.** Server tidak bisa memenuhi permintaan karena masalah internal atau gangguan.

(Kategori `1XX` untuk informasi sementara jarang dipakai di API biasa.)

Kode yang akan sering kalian pakai di modul ini:

| Kode | Nama | Kapan dipakai |
| --- | --- | --- |
| `200` | OK | `GET`, `PATCH`, atau `DELETE` berhasil |
| `201` | Created | `POST` berhasil membuat data baru |
| `204` | No Content | Berhasil tetapi tidak ada body (opsional untuk `DELETE`) |
| `301` / `302` | Moved Permanently / Found | Alamat pindah (permanen / sementara) |
| `304` | Not Modified | Data tidak berubah, client boleh memakai cache-nya |
| `400` | Bad Request | Input kosong, format salah, ID bukan angka |
| `401` | Unauthorized | Belum login atau token tidak valid |
| `403` | Forbidden | Sudah login tetapi tidak punya izin |
| `404` | Not Found | Data atau endpoint tidak ada |
| `409` | Conflict | Bentrok dengan data yang ada, misalnya email sudah terdaftar |
| `500` | Internal Server Error | Error tak terduga di server |
| `502` / `503` | Bad Gateway / Service Unavailable | Server di belakang bermasalah atau sedang tidak tersedia |

Bingung memilih kode? Ikuti alur berikut:

```mermaid
flowchart TD
    S([Request sedang diproses]) --> Q1{"Format atau<br/>isi request valid?"}
    Q1 -->|"Tidak"| A400["400 Bad Request"]
    Q1 -->|"Ya"| Q2{"Sudah login<br/>dan punya izin?"}
    Q2 -->|"Belum login"| A401["401 Unauthorized"]
    Q2 -->|"Tidak punya izin"| A403["403 Forbidden"]
    Q2 -->|"Ya"| Q3{"Data atau<br/>endpoint ada?"}
    Q3 -->|"Tidak"| A404["404 Not Found"]
    Q3 -->|"Ya"| Q4{"Bentrok dengan data lain?<br/>mis. email sudah dipakai"}
    Q4 -->|"Ya"| A409["409 Conflict"]
    Q4 -->|"Tidak"| Q5{"Server error<br/>saat memproses?"}
    Q5 -->|"Ya"| A500["500 Internal Server Error"]
    Q5 -->|"Tidak"| Q6{"Membuat data baru?"}
    Q6 -->|"Ya"| A201["201 Created"]
    Q6 -->|"Tidak"| A200["200 OK"]
```

### 8.3 Format response yang baik

Response yang baik lengkap dan mudah dibaca. Umumnya berbentuk JSON dengan field berikut:

| Field | Tipe | Keterangan |
| --- | --- | --- |
| `status` | boolean | `true` jika berhasil, `false` jika gagal. Ini penyederhanaan dari kode HTTP yang bervariasi |
| `message` | string | Penjelasan singkat, misalnya `Berhasil mengambil data users` atau `User tidak ditemukan` |
| `data` | object / array | Data yang diminta client |
| `metadata` | object (opsional) | Info tambahan, misalnya pagination |

Contoh response sukses dan gagal:

```json
{
  "status": true,
  "message": "Berhasil mengambil data posts",
  "data": [{ "id": 1, "title": "Halo" }],
  "metadata": { "currentPage": 1, "limit": 10, "totalItems": 1, "totalPages": 1 }
}
```

```json
{
  "status": false,
  "message": "User tidak ditemukan"
}
```

Agar semua endpoint memakai format yang sama, kita buat helper `sendSuccess` di `src/utils/response.ts`:

```typescript
// src/utils/response.ts
import type { Response } from "express";

export const sendSuccess = (
  res: Response,
  status: number,
  message: string,
  data?: unknown,
  metadata?: Record<string, unknown>,
): void => {
  res.status(status).json({ status: true, message, data, metadata });
};
```

### 8.4 Error handling di Express 5

Ini salah satu perubahan terbesar dari Express 4 ke 5.

| | Express 4 | Express 5 |
| --- | --- | --- |
| Handler `async` yang melempar error | Server bisa **crash** / request menggantung, kecuali dibungkus `try/catch` + `next(err)` | Error **otomatis diteruskan** ke error handler |

```typescript
// Express 5: tidak perlu try/catch di setiap handler
router.get("/boom", async (_req, _res) => {
  throw new Error("boom"); // otomatis diteruskan ke error handler
});
```

Alurnya:

```mermaid
flowchart LR
    T["throw new HttpError(404, ...)<br/>atau Prisma melempar error P2025"] --> N["Express 5 otomatis<br/>memanggil next(err)"]
    N --> EH{"errorHandler"}
    EH -->|"HttpError"| R1["status sesuai HttpError"]
    EH -->|"Prisma P2002 (data unik bentrok)"| R2["409 Conflict"]
    EH -->|"Prisma P2025 (data tidak ada)"| R3["404 Not Found"]
    EH -->|"JSON request rusak"| R4["400 Bad Request"]
    EH -->|"lainnya"| R5["500 dan dicatat di server"]
```

Kita butuh dua file kecil. Pertama, class error buatan sendiri `src/utils/http-error.ts`:

```typescript
// src/utils/http-error.ts
export class HttpError extends Error {
  status: number;

  constructor(status: number, message: string) {
    super(message);
    this.name = "HttpError";
    this.status = status;
  }
}
```

Kedua, error middleware di `src/middlewares/error.ts`. Perhatikan `errorHandler` wajib punya **4 parameter**:

```typescript
// src/middlewares/error.ts
import type { NextFunction, Request, Response } from "express";
import { HttpError } from "../utils/http-error";

const fail = (res: Response, status: number, message: string): void => {
  res.status(status).json({ status: false, message });
};

// Dipanggil kalau tidak ada route yang cocok
export const notFound = (req: Request, res: Response): void => {
  fail(res, 404, `Endpoint ${req.method} ${req.originalUrl} tidak ditemukan`);
};

// Semua error (throw / reject di handler async) akhirnya sampai di sini
export const errorHandler = (
  err: unknown,
  _req: Request,
  res: Response,
  _next: NextFunction, // wajib ada 4 parameter agar dikenali sebagai error handler
): void => {
  if (err instanceof HttpError) {
    fail(res, err.status, err.message);
    return;
  }

  if (typeof err === "object" && err !== null) {
    // Error dari Prisma membawa properti `code`
    if ("code" in err) {
      if (err.code === "P2002") return fail(res, 409, "Data sudah ada (nilai unik bentrok)");
      if (err.code === "P2025") return fail(res, 404, "Data tidak ditemukan");
      if (err.code === "P2003") return fail(res, 400, "Relasi data tidak valid");
    }
    // Error dari express.json() (mis. JSON rusak) membawa `status` 4xx
    if ("status" in err && typeof err.status === "number" && err.status < 500) {
      return fail(res, err.status, "Request tidak valid (cek format JSON kalian)");
    }
  }

  console.error(err);
  fail(res, 500, "Terjadi kesalahan pada server");
};
```

Terakhir, helper kecil `src/utils/parse.ts` untuk memvalidasi ID dari URL:

```typescript
// src/utils/parse.ts
import { HttpError } from "./http-error";

// Ubah "12" -> 12, dan tolak nilai yang bukan bilangan bulat positif
export const parseId = (value: unknown): number => {
  const id = Number(value);
  if (!Number.isInteger(id) || id <= 0) {
    throw new HttpError(400, "ID harus berupa bilangan bulat positif");
  }
  return id;
};
```

Cara pakainya di controller nanti cukup satu baris:

```typescript
if (!user) throw new HttpError(404, "User tidak ditemukan");
```

---

## 9. PostgreSQL & Database Cloud

### 9.1 PostgreSQL

**PostgreSQL** (sering disebut Postgres) adalah sistem manajemen basis data relasional (RDBMS) open-source dengan fitur yang lebih lengkap dibanding database SQL pada umumnya.

Keunggulan Postgres:

- Transaksi **ACID** (Atomicity, Consistency, Isolation, Durability). [Selengkapnya tentang ACID](https://www.postgresql.org/docs/current/transaction-iso.html)
- Mendukung tipe data [JSONB](https://www.postgresql.org/docs/current/datatype-json.html)
- Performa dan skalabilitas tinggi, serta ekosistem ekstensi (misalnya PostGIS)

> PostgreSQL dipakai di aplikasi besar seperti Instagram, Spotify, Reddit, dan Discord.

#### ACID dengan contoh sehari-hari

Bayangkan transfer Rp100.000 dari rekening A ke B.

| Huruf | Arti | Pada contoh transfer |
| --- | --- | --- |
| **A**tomicity | Semua langkah berhasil, atau tidak ada yang terjadi | Saldo A berkurang **dan** saldo B bertambah. Tidak boleh hanya salah satunya |
| **C**onsistency | Data selalu memenuhi aturan | Total uang A + B tidak berubah, saldo tidak boleh negatif |
| **I**solation | Transaksi yang berjalan bersamaan tidak saling mengganggu | Dua transfer sekaligus tetap menghasilkan saldo yang benar |
| **D**urability | Data yang sudah tersimpan tidak hilang | Setelah sukses, data tetap ada walau server mati |

#### PostgreSQL vs MySQL

| Fitur | **PostgreSQL** | **MySQL** |
| --- | --- | --- |
| **Lisensi** | Open Source (PostgreSQL License) | Open Source (GPL, dimiliki Oracle) |
| **Kepatuhan SQL standar** | Sangat tinggi | Cukup baik, tetapi beberapa fitur tidak standar |
| **Dukungan JSON** | JSON & JSONB (sangat kuat) | JSON biasa (terbatas) |
| **Transaksi & concurrency** | MVCC (Multi-Version Concurrency Control) sangat stabil | Cukup baik, tergantung engine (InnoDB) |
| **Kinerja (write-heavy)** | Lebih kuat untuk transaksi besar | Lebih cepat untuk query sederhana |
| **Ekstensi & kustomisasi** | Mendukung ekstensi (misal: PostGIS) | Kurang fleksibel |
| **Penggunaan umum** | Aplikasi kompleks & analitik | Aplikasi web ringan-menengah |

### 9.2 Database lokal vs Database cloud

Selama ini kalian mungkin menginstal database langsung di laptop. **Database cloud** artinya database dijalankan dan dirawat oleh penyedia layanan di internet, kita tinggal terhubung lewat _connection string_.

```mermaid
flowchart TB
    subgraph LOKAL["Database lokal"]
        direction LR
        L1["Aplikasi di laptop kamu"] --- L2[("PostgreSQL<br/>diinstal sendiri")]
    end
    subgraph CLOUD["Database cloud (Neon)"]
        direction TB
        A1["Laptop A"] --> NET(("Internet"))
        A2["Laptop B (teman satu tim)"] --> NET
        A3["Server saat deploy"] --> NET
        NET --> NP["Connection pooler"]
        NP --> NC["Compute PostgreSQL<br/>(bisa tidur saat sepi)"]
        NC --> NS[("Storage terpisah<br/>data tetap aman")]
    end
```

| | Database lokal | Database cloud |
| --- | --- | --- |
| Instalasi | Harus install dan konfigurasi sendiri | Cukup buat akun dan project |
| Akses tim | Susah, hanya satu laptop | Semua anggota tim memakai database yang sama |
| Backup & perawatan | Tanggung jawab sendiri | Dikelola penyedia layanan |
| Saat aplikasi di-deploy | Harus dipindahkan | Connection string yang sama bisa dipakai |
| Kekurangan | Data hanya ada di satu mesin | Butuh internet, ada batas kuota di paket gratis |

---

## 10. Neon Database

**Neon** adalah layanan PostgreSQL serverless berbasis cloud. Paket gratisnya cukup untuk praktikum ini tanpa perlu menginstal Postgres di laptop.

### 10.1 Paket gratis (Free plan)

Angka berikut mengikuti halaman [Neon Plans](https://neon.com/docs/introduction/plans) per Oktober 2026. Modul tahun lalu menyebut 500 MB, sekarang sudah naik.

| Batas | Free plan |
| --- | --- |
| Jumlah project | 100 |
| Storage Postgres | **1 GB per project** (maksimal 20 GB total di semua project) |
| Compute | 100 CU-hours per project per bulan, autoscaling sampai 2 CU |
| Branch | 10 per project |
| Scale to zero | Otomatis setelah **5 menit idle** (tidak bisa dimatikan) |
| Transfer data keluar | 5 GB per project per bulan |
| Kartu kredit | Tidak diperlukan |

Jika kuota habis, database tidak dihapus: compute dihentikan sampai periode berikutnya, atau operasi yang menambah storage akan gagal sampai ada ruang kosong.

### 10.2 Scale to zero (kenapa request pertama terasa lambat)

Agar hemat, Neon mematikan compute saat tidak dipakai. Saat ada koneksi baru, compute dibangunkan lagi dan butuh **beberapa detik** (_cold start_).

```mermaid
stateDiagram-v2
    [*] --> Active: ada koneksi masuk
    Active --> Idle: tidak ada query
    Idle --> Active: query baru masuk
    Idle --> Suspended: idle 5 menit (scale to zero)
    Suspended --> Active: koneksi baru, cold start beberapa detik
```

Akibatnya, request pertama setelah lama diam bisa lambat atau bahkan gagal dengan error `P1001`. Solusinya ada di [Troubleshooting](#19-troubleshooting). Endpoint `/health` yang akan kita buat membantu "membangunkan" database.

### 10.3 Dua jenis connection string: pooled dan direct

Neon menyediakan dua alamat untuk database yang sama.

```mermaid
flowchart LR
    subgraph APP["Aplikasi Express"]
        PC["Prisma Client<br/>pakai DATABASE_URL"]
    end
    subgraph CLI["Prisma CLI"]
        MG["migrate, db push, studio<br/>pakai DATABASE_URL_UNPOOLED"]
    end
    PC -->|"host ada kata -pooler"| POOL["Neon connection pooler"]
    POOL --> DB[("PostgreSQL")]
    MG -->|"host langsung, tanpa -pooler"| DB
```

| | Pooled | Direct (unpooled) |
| --- | --- | --- |
| Ciri | Host mengandung **`-pooler`** | Host tanpa `-pooler` |
| Variabel di `.env` | `DATABASE_URL` | `DATABASE_URL_UNPOOLED` |
| Dipakai oleh | **Aplikasi** (Prisma Client) | **Prisma CLI** (migrate, studio) |
| Alasan | Mengelola banyak koneksi singkat dengan efisien | Operasi skema butuh koneksi langsung |

### 10.4 Cara membuat project Neon

1. Buka [neon.com](https://neon.com) lalu daftar. Bisa memakai akun Google, GitHub, atau Microsoft.
<img src="./images/neon-login.png">
2. Buat **project baru**. Beri nama (misalnya `modul-4`) dan pilih **region terdekat**, misalnya Singapore, agar latensi kecil.
<img src="./images/neon-newproject.png">
3. Setelah project jadi, kalian masuk ke dashboard. Klik tombol **Connect**.
<img src="./images/neon-connectbranch.png">
4. Pada jendela _Connect to your database_, salin **dua** connection string:
   - dengan opsi **Connection pooling** aktif → ini `DATABASE_URL`
   - dengan opsi **Connection pooling** dimatikan → ini `DATABASE_URL_UNPOOLED`
5. Simpan keduanya, kita akan memasukkannya ke file `.env`.

<!-- TODO (asisten): tambahkan screenshot terbaru Neon Console: (a) buat project + pilih region, (b) dashboard, (c) modal Connect dengan toggle pooling -->

> [!CAUTION]
> Connection string berisi **password**. Jangan dibagikan atau di-commit. Jika terlanjur bocor, reset password di Neon Console, lalu perbarui `.env`.

---

## 11. Prisma ORM

**Prisma** adalah library ORM (Object-Relational Mapping) untuk Node.js dan TypeScript. ORM menjadi penghubung antara kode kita dan database relasional seperti PostgreSQL, MySQL, dan SQLite. Kita tidak perlu menulis SQL manual, cukup memakai method berbasis objek.

### Kenapa pakai Prisma?

```typescript
// SQL manual
const result = await client.query("SELECT * FROM users WHERE id = $1", [id]);

// Prisma
const user = await prisma.user.findUnique({ where: { id } });
```

Keuntungan lain:

- **Type-safe.** Editor memberi autocomplete untuk nama tabel dan kolom, dan salah ketik langsung ketahuan.
- **Lebih aman.** Query dibuat dengan parameter sehingga terhindar dari **SQL Injection**.
- **Migrasi terkelola.** Perubahan struktur tabel tercatat sebagai file SQL yang bisa di-commit.

### Tiga komponen utama Prisma

| Komponen | File / perintah | Fungsi |
| --- | --- | --- |
| **Prisma Schema** | `prisma/schema.prisma` | Tempat mendefinisikan model data. _Single source of truth_ untuk struktur database |
| **Prisma Client** | `prisma generate` | Query builder type-safe yang dibuat otomatis dari schema. Dipakai untuk baca/tulis data |
| **Prisma Migrate** | `prisma migrate dev` | Membuat file migrasi SQL dari perubahan schema dan menjaga database tetap sinkron |

```mermaid
flowchart LR
    S["schema.prisma<br/>model data"] -->|"prisma migrate dev"| M["prisma/migrations/*.sql"]
    M -->|"dijalankan ke"| DB[("Neon PostgreSQL")]
    S -->|"prisma generate"| G["src/generated/prisma<br/>Prisma Client type-safe"]
    G -->|"dipakai di kode"| APP["controller:<br/>prisma.user.findMany()"]
    AD["Driver adapter<br/>adapter-neon"] --> G
    CFG["prisma.config.ts<br/>URL untuk CLI"] -.-> M
    ENV[".env"] -.-> CFG
    ENV -.-> AD
```

### Perubahan besar di Prisma 7

Banyak tutorial di internet masih memakai Prisma 6 atau lebih lama, jadi hati-hati saat mencari referensi.

| Hal | Prisma 6 dan sebelumnya | **Prisma 7 (dipakai modul ini)** |
| --- | --- | --- |
| Generator | `provider = "prisma-client-js"` | `provider = "prisma-client"` dan **`output` wajib** |
| Lokasi Prisma Client | `node_modules/@prisma/client` | Dibuat di project kita (`src/generated/prisma`) |
| Cara import | `from "@prisma/client"` | `from "../generated/prisma/client"` |
| URL database | `url = env("DATABASE_URL")` di `schema.prisma` | Di **`prisma.config.ts`** (untuk CLI) dan **driver adapter** (untuk aplikasi) |
| Driver adapter | Opsional | **Wajib** (`@prisma/adapter-neon` untuk Neon) |
| File `.env` | Dibaca otomatis | **Harus dimuat sendiri** dengan `dotenv` |
| `prisma generate` | Ikut dijalankan oleh `migrate dev` | **Harus dijalankan manual** |
| Format modul | CommonJS | **ESM** (`"type": "module"`) |
| Query engine | Rust | TypeScript (lebih ringan dan cepat) |
| Syarat versi | | Node 20.19+ / 22.12+ / 24+ dan TypeScript 5.4+ |

> [!WARNING]
> **Pin versi Prisma ke 7.** Per 25 Oktober 2026, tag `latest` di npm untuk paket `prisma` menunjuk **Prisma 8 (masih release candidate)**, yang memakai schema dan perintah berbeda. Jika kalian menjalankan `npm install prisma` tanpa versi, kalian bisa mendapat versi yang tidak cocok dengan modul ini. Selalu tulis **`prisma@7`**.

---

## 12. Menghubungkan Express + Prisma + Neon

### Langkah 1. Instal dependensi

```bash
npm install @prisma/client@7 @prisma/adapter-neon@7 dotenv bcryptjs
npm install -D prisma@7
```

| Package | Fungsi |
| --- | --- |
| `@prisma/client` | Prisma Client |
| `@prisma/adapter-neon` | Driver adapter untuk Neon (sudah termasuk driver serverless Neon) |
| `dotenv` | Memuat isi `.env` ke `process.env` |
| `bcryptjs` | Hash password |
| `prisma` (dev) | Prisma CLI (`generate`, `migrate`, `studio`) |

> Pastikan `prisma`, `@prisma/client`, dan `@prisma/adapter-neon` berada di **mayor versi yang sama** (7).

### Langkah 2. Inisialisasi Prisma

```bash
npx prisma init
```

Perintah ini membuat folder `prisma/` berisi `schema.prisma`, file `prisma.config.ts`, dan `.env`. Isinya akan kita **timpa** di langkah berikut.

### Langkah 3. Isi `.env`

Tempelkan dua connection string dari Neon (lihat [10.4](#104-cara-membuat-project-neon)):

```env
# Pooled (host ada -pooler): dipakai aplikasi
DATABASE_URL="postgresql://USER:PASSWORD@ep-xxxx-pooler.REGION.aws.neon.tech/DBNAME?sslmode=require"

# Direct (tanpa -pooler): dipakai Prisma CLI
DATABASE_URL_UNPOOLED="postgresql://USER:PASSWORD@ep-xxxx.REGION.aws.neon.tech/DBNAME?sslmode=require"

PORT=3000
```

### Langkah 4. Tulis schema

Ganti isi `prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client"
  output   = "../src/generated/prisma"
}

datasource db {
  provider = "postgresql"
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  password  String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  posts     Post[]
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  views     Int      @default(0)
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([authorId])
}
```

Hubungan antar tabelnya:

```mermaid
erDiagram
    USER ||--o{ POST : "menulis"
    USER {
        int id PK
        string email UK
        string name
        string password
        datetime createdAt
        datetime updatedAt
    }
    POST {
        int id PK
        string title
        string content
        boolean published
        int views
        int authorId FK
        datetime createdAt
        datetime updatedAt
    }
```

Satu **User** bisa memiliki banyak **Post** (relasi one-to-many), dan setiap **Post** dimiliki tepat satu **User**.

**Penjelasan schema:**

| Atribut | Arti |
| --- | --- |
| `@id` | Primary key |
| `@default(autoincrement())` | ID bertambah otomatis |
| `@unique` | Nilai tidak boleh kembar |
| `@default(now())` | Diisi waktu saat data dibuat |
| `@updatedAt` | Diperbarui otomatis saat data diubah |
| `String?` | Tanda `?` artinya boleh kosong (nullable) |
| `@relation(fields, references)` | Mendefinisikan foreign key: `authorId` menunjuk ke `User.id` |
| `onDelete: Cascade` | Jika user dihapus, semua post miliknya **ikut terhapus** oleh database |
| `@@index([authorId])` | Index agar pencarian post berdasarkan penulis cepat |

> **Perhatikan:** tidak ada lagi `url = env(...)` pada blok `datasource`. Di Prisma 7, URL diatur di `prisma.config.ts`.

### Langkah 5. Konfigurasi Prisma CLI

Buat/timpa `prisma.config.ts` di **root project** (sejajar `package.json`):

```typescript
// prisma.config.ts
import "dotenv/config";
import { defineConfig, env } from "prisma/config";

export default defineConfig({
  schema: "prisma/schema.prisma",
  migrations: { path: "prisma/migrations" },
  datasource: { url: env("DATABASE_URL_UNPOOLED") },
});
```

File ini dibaca oleh Prisma CLI. Perhatikan CLI memakai URL **direct** (`DATABASE_URL_UNPOOLED`).

### Langkah 6. Migrasi database

```bash
npx prisma migrate dev --name init
```

Perintah ini akan:

1. Membandingkan `schema.prisma` dengan kondisi database.
2. Membuat file migrasi SQL di `prisma/migrations/`.
3. Menjalankan migrasi tersebut ke database Neon.

```mermaid
sequenceDiagram
    participant Dev as Kamu
    participant CLI as Prisma CLI
    participant DB as Neon (direct URL)

    Dev->>CLI: ubah schema.prisma
    Dev->>CLI: npx prisma migrate dev --name init
    CLI->>DB: bandingkan schema dengan kondisi database
    CLI->>CLI: buat file SQL di prisma/migrations
    CLI->>DB: jalankan SQL (CREATE TABLE ...)
    DB-->>CLI: berhasil, dicatat di tabel _prisma_migrations
```

Cek tabel `User` dan `Post` di Neon Console (menu **Tables**) atau lewat Prisma Studio (langkah 8).

<!-- TODO (asisten): screenshot tabel di Neon Console -->

> Untuk eksperimen cepat tanpa file migrasi, ada alternatif `npx prisma db push`. Namun untuk project yang dikerjakan tim, gunakan `migrate dev` supaya riwayat perubahan tersimpan.

### Langkah 7. Generate Prisma Client

Di Prisma 7, ini **tidak lagi otomatis**. Jalankan setiap kali selesai mengubah schema atau setelah `git clone`:

```bash
npx prisma generate
```

Hasilnya muncul di `src/generated/prisma/`. Folder ini masuk `.gitignore`, jadi anggota tim yang meng-clone project harus menjalankan perintah ini sendiri.

### Langkah 8. (Opsional) Prisma Studio

Prisma Studio adalah antarmuka web untuk melihat dan mengedit isi database:

```bash
npx prisma studio
```

### Langkah 9. Buat instance Prisma Client

Buat `src/config/prisma.ts`:

```typescript
// src/config/prisma.ts
import "dotenv/config";
import { PrismaNeon } from "@prisma/adapter-neon";
import { PrismaClient } from "../generated/prisma/client";

const adapter = new PrismaNeon({
  connectionString: process.env.DATABASE_URL!, // connection string POOLED
});

const prisma = new PrismaClient({
  adapter,
  log: ["warn", "error"],
});

export default prisma;
```

> **Memakai PostgreSQL biasa (lokal atau penyedia lain)?** Ganti adapternya. Instal `@prisma/adapter-pg`, lalu:
>
> ```typescript
> import { PrismaPg } from "@prisma/adapter-pg";
> const adapter = new PrismaPg({ connectionString: process.env.DATABASE_URL! });
> ```
>
> Bagian lain kode tidak berubah.

### Langkah 10. Server utama

Berikut `src/index.ts` versi lengkap. Sebagian besar sudah kalian kenal; bagian baru adalah `logger`, `/health`, error middleware, dan _graceful shutdown_.

```typescript
// src/index.ts
import "dotenv/config";
import express from "express";
import prisma from "./config/prisma";
import { errorHandler, notFound } from "./middlewares/error";
import { logger } from "./middlewares/logger";
import router from "./routes";

const app = express();

// Middleware global (urutannya penting!)
app.use(logger);
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.get("/", (_req, res) => {
  res.json({
    message: "Express + Prisma + Neon API",
    endpoints: { users: "/api/users", posts: "/api/posts", health: "/health" },
  });
});

// Cek koneksi database (sekaligus membangunkan Neon dari scale-to-zero)
app.get("/health", async (_req, res) => {
  await prisma.$queryRaw`SELECT 1`;
  res.json({ status: true, message: "Server dan database OK" });
});

app.use("/api", router);

// Dua middleware ini harus paling bawah
app.use(notFound);
app.use(errorHandler);

const PORT = Number(process.env.PORT) || 3000;
const server = app.listen(PORT, (error?: Error) => {
  if (error) throw error;
  console.log(`Server berjalan di http://localhost:${PORT}`);
});

// Graceful shutdown (Ctrl+C)
const shutdown = async () => {
  server.close();
  await prisma.$disconnect();
  console.log("Database disconnected");
  process.exit(0);
};
process.on("SIGINT", shutdown);
process.on("SIGTERM", shutdown);
```

Beberapa hal yang perlu diperhatikan:

- `import "dotenv/config"` memuat `.env` sebelum kode lain berjalan.
- `/health` menjalankan `SELECT 1` ke database. Cocok untuk mengetes koneksi dan membangunkan Neon dari tidur.
- `notFound` dan `errorHandler` **harus paling bawah**, setelah semua route.
- _Graceful shutdown_: saat server dihentikan (Ctrl+C), koneksi database ditutup rapi.

### Langkah 11. Jalankan dan uji

```bash
npm run dev
```

Buka `http://localhost:3000/health`. Jika muncul `Server dan database OK`, koneksi ke Neon berhasil.

---

## 13. CRUD Menggunakan Prisma

### Struktur folder akhir

```
modul-4/
├── prisma/
│   ├── migrations/
│   └── schema.prisma
├── src/
│   ├── config/
│   │   └── prisma.ts
│   ├── controllers/
│   │   ├── userController.ts
│   │   └── postController.ts
│   ├── generated/              ← hasil `prisma generate` (tidak di-commit)
│   ├── middlewares/
│   │   ├── error.ts
│   │   └── logger.ts
│   ├── routes/
│   │   ├── index.ts
│   │   ├── food.routes.ts
│   │   ├── user.routes.ts
│   │   └── post.routes.ts
│   ├── utils/
│   │   ├── http-error.ts
│   │   ├── parse.ts
│   │   └── response.ts
│   └── index.ts
├── .env                        ← tidak di-commit
├── .gitignore
├── package.json
├── prisma.config.ts
└── tsconfig.json
```

Pola yang dipakai: **Route** menentukan _URL dan method_, **Controller** berisi _logika_, dan **Prisma** berbicara dengan database.

```mermaid
flowchart LR
    R["routes/*.routes.ts<br/>URL + method"] --> K["controllers/*Controller.ts<br/>validasi + logika"]
    K --> P["config/prisma.ts<br/>Prisma Client"]
    P --> D[("Neon")]
    K -.->|"throw error"| E["middlewares/error.ts"]
```

### Pemetaan CRUD: HTTP, Prisma, dan SQL

| Operasi | Endpoint | Method Prisma | SQL yang dihasilkan (kira-kira) |
| --- | --- | --- | --- |
| Create | `POST /api/users` | `prisma.user.create()` | `INSERT INTO "User" ...` |
| Read (banyak) | `GET /api/users` | `prisma.user.findMany()` | `SELECT ... FROM "User"` |
| Read (satu) | `GET /api/users/:id` | `prisma.user.findUnique()` | `SELECT ... WHERE id = $1` |
| Update | `PATCH /api/users/:id` | `prisma.user.update()` | `UPDATE "User" SET ... WHERE id = $1` |
| Delete | `DELETE /api/users/:id` | `prisma.user.delete()` | `DELETE FROM "User" WHERE id = $1` |

### 13.1 Controller User

Buat `src/controllers/userController.ts`. Kita jelaskan per bagian di bawahnya.

```typescript
// src/controllers/userController.ts
import type { Request, Response } from "express";
import bcrypt from "bcryptjs";
import prisma from "../config/prisma";
import { HttpError } from "../utils/http-error";
import { parseId } from "../utils/parse";
import { sendSuccess } from "../utils/response";

export const createUser = async (req: Request, res: Response) => {
  // Express 5: req.body = undefined jika body tidak ter-parse, jadi beri fallback {}
  const { email, name, password } = req.body ?? {};

  if (!email || !password) {
    throw new HttpError(400, "Email dan password wajib diisi");
  }

  const existingUser = await prisma.user.findUnique({ where: { email } });
  if (existingUser) {
    throw new HttpError(409, "Email sudah terdaftar");
  }

  const user = await prisma.user.create({
    data: {
      email,
      name,
      password: await bcrypt.hash(password, 10), // jangan pernah simpan password polos
    },
    select: { id: true, email: true, name: true, createdAt: true },
  });

  sendSuccess(res, 201, "User berhasil dibuat", user);
};

export const getAllUsers = async (_req: Request, res: Response) => {
  const users = await prisma.user.findMany({
    select: {
      id: true,
      email: true,
      name: true,
      createdAt: true,
      _count: { select: { posts: true } },
    },
  });

  sendSuccess(res, 200, "Berhasil mengambil data users", users);
};

export const getUserById = async (req: Request, res: Response) => {
  const id = parseId(req.params.id);

  const user = await prisma.user.findUnique({
    where: { id },
    select: {
      id: true,
      email: true,
      name: true,
      createdAt: true,
      posts: {
        select: { id: true, title: true, published: true, createdAt: true },
      },
    },
  });

  if (!user) throw new HttpError(404, "User tidak ditemukan");

  sendSuccess(res, 200, "Berhasil mengambil data user", user);
};

export const updateUser = async (req: Request, res: Response) => {
  const id = parseId(req.params.id);
  const { email, name } = req.body ?? {};

  // Jika id tidak ada, Prisma melempar error P2025 -> ditangani errorHandler (404)
  const user = await prisma.user.update({
    where: { id },
    data: {
      ...(email && { email }),
      ...(name && { name }),
    },
    select: { id: true, email: true, name: true, updatedAt: true },
  });

  sendSuccess(res, 200, "User berhasil diupdate", user);
};

export const deleteUser = async (req: Request, res: Response) => {
  const id = parseId(req.params.id);

  // Post milik user ikut terhapus karena `onDelete: Cascade` di schema
  await prisma.user.delete({ where: { id } });

  sendSuccess(res, 200, "User berhasil dihapus");
};
```

**CREATE.** `createUser` memvalidasi input, mengecek apakah email sudah ada (`409 Conflict` jika ya), meng-hash password, lalu menyimpan. Password **tidak pernah** dikembalikan karena kita memakai `select`.

**READ.** `getAllUsers` memakai `_count` untuk menghitung jumlah post tiap user. `getUserById` menyertakan daftar post milik user itu (relasi).

**UPDATE.** `updateUser` memakai pola `...(email && { email })`: field hanya ikut diubah jika dikirim. Jika ID tidak ada, Prisma melempar error `P2025` yang diubah `errorHandler` menjadi `404`. Kita tidak perlu mengecek manual.

**DELETE.** Karena schema memakai `onDelete: Cascade`, menghapus user otomatis menghapus semua post miliknya.

### 13.2 Controller Post

Buat `src/controllers/postController.ts`:

```typescript
// src/controllers/postController.ts
import type { Request, Response } from "express";
import prisma from "../config/prisma";
import { HttpError } from "../utils/http-error";
import { parseId } from "../utils/parse";
import { sendSuccess } from "../utils/response";

const authorSelect = { select: { id: true, name: true, email: true } };

export const createPost = async (req: Request, res: Response) => {
  const { title, content, authorId } = req.body ?? {};

  if (!title || !authorId) {
    throw new HttpError(400, "Title dan authorId wajib diisi");
  }

  const author = await prisma.user.findUnique({
    where: { id: parseId(authorId) },
  });
  if (!author) throw new HttpError(404, "User tidak ditemukan");

  const post = await prisma.post.create({
    data: { title, content, authorId: author.id },
    include: { author: authorSelect },
  });

  sendSuccess(res, 201, "Post berhasil dibuat", post);
};

// GET /api/posts?page=1&limit=5&search=eco&published=true
export const getAllPosts = async (req: Request, res: Response) => {
  const page = Math.max(Number(req.query.page) || 1, 1);
  const limit = Math.min(Math.max(Number(req.query.limit) || 10, 1), 100);
  const search = typeof req.query.search === "string" ? req.query.search : "";
  const published =
    req.query.published === undefined
      ? undefined
      : req.query.published === "true";

  const where = {
    ...(search && { title: { contains: search, mode: "insensitive" as const } }),
    ...(published !== undefined && { published }),
  };

  const [posts, total] = await prisma.$transaction([
    prisma.post.findMany({
      where,
      skip: (page - 1) * limit,
      take: limit,
      orderBy: { createdAt: "desc" },
      include: { author: authorSelect },
    }),
    prisma.post.count({ where }),
  ]);

  sendSuccess(res, 200, "Berhasil mengambil data posts", posts, {
    currentPage: page,
    limit,
    totalItems: total,
    totalPages: Math.ceil(total / limit),
  });
};

export const getPostStats = async (_req: Request, res: Response) => {
  const [overall, perAuthor] = await Promise.all([
    prisma.post.aggregate({
      _count: { id: true },
      _sum: { views: true },
      _avg: { views: true },
      _max: { views: true },
    }),
    prisma.post.groupBy({
      by: ["authorId"],
      _count: { id: true },
      orderBy: { _count: { id: "desc" } },
    }),
  ]);

  sendSuccess(res, 200, "Berhasil mengambil statistik posts", {
    overall,
    perAuthor,
  });
};

export const getPostById = async (req: Request, res: Response) => {
  const id = parseId(req.params.id);

  const post = await prisma.post.findUnique({
    where: { id },
    include: { author: authorSelect },
  });
  if (!post) throw new HttpError(404, "Post tidak ditemukan");

  sendSuccess(res, 200, "Berhasil mengambil data post", post);
};

export const updatePost = async (req: Request, res: Response) => {
  const id = parseId(req.params.id);
  const { title, content, published } = req.body ?? {};

  const post = await prisma.post.update({
    where: { id },
    data: {
      ...(title && { title }),
      ...(content !== undefined && { content }),
      ...(published !== undefined && { published }),
    },
    include: { author: authorSelect },
  });

  sendSuccess(res, 200, "Post berhasil diupdate", post);
};

export const deletePost = async (req: Request, res: Response) => {
  const id = parseId(req.params.id);

  await prisma.post.delete({ where: { id } });

  sendSuccess(res, 200, "Post berhasil dihapus");
};
```

Alur `createPost` (termasuk cabang error):

```mermaid
sequenceDiagram
    participant C as Client
    participant K as createPost
    participant P as Prisma
    participant E as errorHandler

    C->>K: POST /api/posts (title, authorId)
    alt title atau authorId kosong
        K-->>E: throw HttpError 400
        E-->>C: 400 Bad Request
    else input valid
        K->>P: user.findUnique(authorId)
        alt user tidak ditemukan
            K-->>E: throw HttpError 404
            E-->>C: 404 Not Found
        else user ada
            K->>P: post.create(...)
            P-->>K: post baru
            K-->>C: 201 Created
        end
    end
```

`getAllPosts` dan `getPostStats` memakai filtering, pagination, aggregate, dan transaction. Semuanya dijelaskan di bagian 14 sampai 16.

### 13.3 Routes

Buat `src/routes/user.routes.ts`:

```typescript
// src/routes/user.routes.ts
import { Router } from "express";
import {
  createUser,
  deleteUser,
  getAllUsers,
  getUserById,
  updateUser,
} from "../controllers/userController";

const router = Router();

router.post("/", createUser);
router.get("/", getAllUsers);
router.get("/:id", getUserById);
router.patch("/:id", updateUser);
router.delete("/:id", deleteUser);

export default router;
```

Buat `src/routes/post.routes.ts`:

```typescript
// src/routes/post.routes.ts
import { Router } from "express";
import {
  createPost,
  deletePost,
  getAllPosts,
  getPostById,
  getPostStats,
  updatePost,
} from "../controllers/postController";

const router = Router();

router.post("/", createPost);
router.get("/", getAllPosts);
router.get("/stats", getPostStats); // HARUS di atas "/:id", kalau tidak "stats" dianggap sebagai id
router.get("/:id", getPostById);
router.patch("/:id", updatePost);
router.delete("/:id", deletePost);

export default router;
```

Lalu perbarui `src/routes/index.ts` agar memuat ketiganya:

```typescript
// src/routes/index.ts
import { Router } from "express";
import foodRouter from "./food.routes";
import postRouter from "./post.routes";
import userRouter from "./user.routes";

const router = Router();

router.use("/food", foodRouter);
router.use("/users", userRouter);
router.use("/posts", postRouter);

export default router;
```

### 13.4 Daftar endpoint

| Method | URL | Fungsi |
| --- | --- | --- |
| `GET` | `/health` | Cek server dan database |
| `POST` | `/api/users` | Buat user |
| `GET` | `/api/users` | Semua user |
| `GET` | `/api/users/:id` | Detail user + post-nya |
| `PATCH` | `/api/users/:id` | Ubah user |
| `DELETE` | `/api/users/:id` | Hapus user (post ikut terhapus) |
| `POST` | `/api/posts` | Buat post |
| `GET` | `/api/posts?page=&limit=&search=&published=` | Daftar post (filter + pagination) |
| `GET` | `/api/posts/stats` | Statistik post |
| `GET` | `/api/posts/:id` | Detail post |
| `PATCH` | `/api/posts/:id` | Ubah post |
| `DELETE` | `/api/posts/:id` | Hapus post |

Contoh body untuk `POST /api/users`:

```json
{
  "email": "budi@santoso.id",
  "name": "Budi Santoso",
  "password": "rahasia123"
}
```

Contoh body untuk `POST /api/posts`:

```json
{
  "title": "Belajar Express dan Prisma",
  "content": "Seru ternyata!",
  "authorId": 1
}
```

---

## 14. Filtering & Pagination

### 14.1 Filtering

**Filtering** menyaring data di database sebelum dikembalikan. Prisma menyediakan parameter `where`, mirip klausa `WHERE` di SQL.

```typescript
// Semua post yang sudah dipublish
const publishedPosts = await prisma.post.findMany({
  where: { published: true },
});
```

```typescript
// User dengan nama mengandung "budi" (tidak peduli huruf besar/kecil)
const filteredUsers = await prisma.user.findMany({
  where: {
    name: { contains: "budi", mode: "insensitive" },
  },
});
```

`mode: "insensitive"` membuat pencarian tidak membedakan huruf besar dan kecil. "budi" cocok dengan "Budi", "BUdi", atau "bUDI".

**Pemetaan operator Prisma ke SQL:**

| Prisma | Arti | SQL (Postgres) |
| --- | --- | --- |
| `{ published: true }` | sama dengan | `published = true` |
| `{ title: { contains: "x" } }` | mengandung | `title LIKE '%x%'` |
| `{ title: { contains: "x", mode: "insensitive" } }` | mengandung, abaikan huruf besar/kecil | `title ILIKE '%x%'` |
| `{ title: { startsWith: "x" } }` | diawali | `title LIKE 'x%'` |
| `{ id: { in: [1, 2, 3] } }` | salah satu dari | `id IN (1, 2, 3)` |
| `{ views: { gt: 100 } }` | lebih dari (`gte`, `lt`, `lte` juga ada) | `views > 100` |
| `{ NOT: { authorId: 1 } }` | bukan | `NOT authorId = 1` |
| `AND: [...]` / `OR: [...]` | dan / atau | `... AND ...` / `... OR ...` |

Kondisi gabungan **AND**, **OR**, dan **NOT**:

```typescript
// Published DAN judul mengandung "tutorial"
const complexFilter = await prisma.post.findMany({
  where: {
    AND: [{ published: true }, { title: { contains: "tutorial" } }],
  },
});
```

```typescript
// Judul mengandung "AI" ATAU "Machine Learning", tetapi BUKAN dari author 1
const posts = await prisma.post.findMany({
  where: {
    OR: [
      { title: { contains: "AI" } },
      { title: { contains: "Machine Learning" } },
    ],
    NOT: { authorId: 1 },
  },
});
```

Filtering lewat **relasi**, misalnya semua post dari user dengan email tertentu:

```typescript
const postsByEmail = await prisma.post.findMany({
  where: {
    author: { email: "budi@santoso.id" },
  },
});
```

Dari sisi sebaliknya, user yang **punya minimal satu post yang sudah dipublish**:

```typescript
const activeAuthors = await prisma.user.findMany({
  where: { posts: { some: { published: true } } },
});
```

### 14.2 Pagination

Jika datanya ribuan, tidak mungkin dikirim sekaligus. **Pagination** membaginya per halaman memakai dua properti:

- `skip`: melewati sejumlah data
- `take`: jumlah data yang diambil

Rumusnya: **`skip = (page - 1) × limit`**. Contoh untuk 23 data dengan `limit = 10`:

```
Data:  [01][02][03][04][05][06][07][08][09][10] [11][12][13][14][15][16][17][18][19][20] [21][22][23]
        └──────────── halaman 1 ─────────────┘  └──────────── halaman 2 ─────────────┘  └─ halaman 3 ─┘
```

| page | limit | skip | take | Data yang diambil | totalPages |
| --- | --- | --- | --- | --- | --- |
| 1 | 10 | 0 | 10 | data 1 - 10 | 3 |
| 2 | 10 | 10 | 10 | data 11 - 20 | 3 |
| 3 | 10 | 20 | 10 | data 21 - 23 (3 data) | 3 |

`totalPages = Math.ceil(total / limit)` → `Math.ceil(23 / 10) = 3`.

```typescript
const page = Math.max(Number(req.query.page) || 1, 1);       // minimal halaman 1
const limit = Math.min(Math.max(Number(req.query.limit) || 10, 1), 100); // 1 sampai 100

const posts = await prisma.post.findMany({
  skip: (page - 1) * limit,
  take: limit,
  orderBy: { createdAt: "desc" },
});

const totalPosts = await prisma.post.count();
```

> Kita membatasi `limit` maksimal 100 agar tidak ada yang meminta 1 juta data sekaligus.

### 14.3 Filtering + Pagination

Dua teknik ini digabung di `getAllPosts` (kodenya sudah ada di [controller post](#132-controller-post)), sehingga satu endpoint bisa melayani pencarian sekaligus pembagian halaman:

```
GET /api/posts?page=2&limit=5&search=eco&published=true
```

```mermaid
flowchart LR
    Q["req.query<br/>page, limit, search, published"] --> V["validasi dan<br/>nilai default"]
    V --> W["susun objek where"]
    W --> T["$transaction:<br/>findMany + count"]
    T --> M["hitung totalPages"]
    M --> R["response + metadata"]
```

| Query | Default | Pengaruh |
| --- | --- | --- |
| `page` | `1` | Halaman ke berapa (`skip = (page - 1) × limit`) |
| `limit` | `10` (maksimal `100`) | Jumlah data per halaman (`take`) |
| `search` | kosong | Judul mengandung kata ini, tidak peduli huruf besar/kecil |
| `published` | tidak difilter | `true` atau `false` untuk menyaring status publish |

Dua hal yang mudah terlewat:

- `count` **harus memakai `where` yang sama** dengan `findMany`. Jika tidak, `totalPages` akan salah karena menghitung semua data.
- `findMany` dan `count` dijalankan dalam satu `$transaction` supaya keduanya membaca keadaan database yang sama.

Response-nya membawa `metadata` pagination:

```json
{
  "status": true,
  "message": "Berhasil mengambil data posts",
  "data": [ ... ],
  "metadata": { "currentPage": 2, "limit": 5, "totalItems": 23, "totalPages": 5 }
}
```

> **Catatan:** `skip` yang sangat besar membuat query makin lambat. Untuk data berjuta-juta, ada teknik **cursor-based pagination** (`cursor` + `take`) yang bisa kalian pelajari di dokumentasi Prisma.

---

## 15. Aggregate Function

**Aggregate** melakukan perhitungan atas banyak baris sekaligus, mirip `COUNT`, `AVG`, `SUM`, `MIN`, `MAX` di SQL.

| Fungsi Prisma | Arti | Berlaku untuk |
| --- | --- | --- |
| `_count` | Menghitung jumlah record | Semua kolom |
| `_avg` | Rata-rata | Kolom angka |
| `_sum` | Jumlah | Kolom angka |
| `_min` | Nilai terkecil | Angka, tanggal, teks |
| `_max` | Nilai terbesar | Angka, tanggal, teks |

Menghitung total user:

```typescript
const totalUsers = await prisma.user.count();
console.log(`Total User: ${totalUsers}`);
```

Aggregate lengkap pada kolom `views` milik `Post`:

```typescript
const result = await prisma.post.aggregate({
  _count: { id: true },
  _sum: { views: true },
  _avg: { views: true },
  _max: { views: true },
});
```

Contoh hasil:

```json
{
  "_count": { "id": 10 },
  "_sum": { "views": 250 },
  "_avg": { "views": 25 },
  "_max": { "views": 90 }
}
```

> Jika tabel kosong, `_sum`, `_avg`, dan `_max` bernilai `null`, bukan 0.

### Group By

`groupBy` mengelompokkan baris berdasarkan kolom tertentu lalu menghitung agregat di tiap kelompok. Contoh: jumlah post per author.

```typescript
const groupedPosts = await prisma.post.groupBy({
  by: ["authorId"],
  _count: { id: true },
  orderBy: { _count: { id: "desc" } },
});
```

Visualisasinya:

```
Tabel Post (sebelum)              groupBy authorId                 Hasil
┌────┬──────────┬────────┐        ┌─────────────┐
│ id │ title    │authorId│        │ authorId=1  │ → 3 post   →  { authorId: 1, _count: { id: 3 } }
├────┼──────────┼────────┤        │  (id 1,2,5) │
│ 1  │ A        │   1    │        ├─────────────┤
│ 2  │ B        │   1    │        │ authorId=2  │ → 2 post   →  { authorId: 2, _count: { id: 2 } }
│ 3  │ C        │   2    │        │  (id 3,4)   │
│ 4  │ D        │   2    │        └─────────────┘
│ 5  │ E        │   1    │
└────┴──────────┴────────┘
```

```json
[
  { "authorId": 1, "_count": { "id": 3 } },
  { "authorId": 2, "_count": { "id": 2 } }
]
```

### Aggregate dengan kondisi

Tambahkan `where` untuk perhitungan bersyarat:

```typescript
const totalPublished = await prisma.post.count({
  where: { published: true },
});
```

### Endpoint `GET /api/posts/stats`

Di controller sudah ada `getPostStats` yang menjalankan `aggregate` dan `groupBy` bersamaan dengan `Promise.all` (keduanya tidak saling bergantung, jadi boleh dijalankan paralel).

> [!NOTE]
> **Jangan `groupBy` pada `createdAt` untuk statistik per hari.** `createdAt` menyimpan waktu sampai milidetik, sehingga hampir setiap baris menjadi kelompoknya sendiri. Untuk "jumlah post per tanggal", gunakan raw query dengan `date_trunc` (lihat [bagian 17](#17-raw-query)).

---

## 16. Batch Queries & Transaction

**Transaction** adalah rangkaian query yang dijalankan sebagai **satu kesatuan**: semuanya berhasil, atau semuanya dibatalkan (prinsip _Atomicity_ pada ACID).

```mermaid
flowchart TD
    B(["BEGIN"]) --> Q1["Query 1: buat user"]
    Q1 --> Q2["Query 2: buat post 1"]
    Q2 --> Q3["Query 3: buat post 2"]
    Q3 --> OK{"Semua berhasil?"}
    OK -->|"Ya"| C(["COMMIT<br/>semua data tersimpan"])
    OK -->|"Ada yang gagal"| R(["ROLLBACK<br/>tidak ada yang tersimpan"])
```

Fitur Prisma yang memakai transaksi:

- Nested writes
- Batch / Bulk writes
- `$transaction`

### 16.1 Nested Writes

Membuat beberapa record berelasi dalam satu operasi. Misalnya membuat user sekaligus dua post-nya. Prisma menjamin ketiganya sukses atau gagal bersama, jadi tidak mungkin user terbuat tetapi post-nya tidak.

```typescript
const newUser = await prisma.user.create({
  data: {
    email: "budi@santoso.id",
    password: await bcrypt.hash("rahasia123", 10),
    posts: {
      create: [{ title: "Judul Post 1" }, { title: "Judul Post 2" }],
    },
  },
  include: { posts: true },
});
```

### 16.2 Batch / Bulk Writes

Menulis banyak record bertipe sama dalam satu transaksi:

- `createMany()`
- `createManyAndReturn()`
- `updateMany()`
- `updateManyAndReturn()`
- `deleteMany()`

Misalnya menambah `views` semua post milik author 1 sebanyak 10:

```typescript
const result = await prisma.post.updateMany({
  where: { authorId: 1 },
  data: { views: { increment: 10 } },
});

console.log(result.count); // jumlah post yang ter-update
```

`updateMany()` **tidak mengembalikan datanya**, hanya objek `{ count: n }` berisi jumlah record yang berubah. Jika datanya ikut dibutuhkan, gunakan `updateManyAndReturn()`.

### 16.3 `$transaction`

`$transaction` menjalankan beberapa operasi Prisma yang **berbeda** dalam satu transaksi database. Hasil semua query konsisten dengan satu keadaan database yang sama.

Contoh: mengambil post yang mengandung kata `kucing` sekaligus menghitung total post.

```typescript
const [posts, totalPosts] = await prisma.$transaction([
  prisma.post.findMany({ where: { title: { contains: "kucing" } } }),
  prisma.post.count(),
]);
```

Bentuk di atas menerima **array** query. Jika langkah berikutnya bergantung pada hasil langkah sebelumnya, gunakan bentuk **interactive transaction**:

```typescript
const post = await prisma.$transaction(async (tx) => {
  const author = await tx.user.findUniqueOrThrow({ where: { id: 1 } });
  return tx.post.create({ data: { title: "Halo", authorId: author.id } });
});
```

Di dalam callback gunakan `tx`, **bukan** `prisma`. Penjelasan lebih lengkap ada di [dokumentasi transaksi Prisma](https://www.prisma.io/docs/orm/prisma-client/queries/transactions).

---

## 17. Raw Query

Kadang query kompleks lebih mudah ditulis dengan SQL langsung. Prisma menyediakan empat fungsi:

| Fungsi | Mengembalikan | Cara pakai |
| --- | --- | --- |
| `$queryRaw` | Baris data hasil query | Tagged template (aman dari SQL injection) |
| `$executeRaw` | **Jumlah baris** yang terpengaruh (`INSERT`/`UPDATE`/`DELETE`) | Tagged template |
| `$queryRawUnsafe` | Baris data hasil query | Menerima **string biasa** |
| `$executeRawUnsafe` | Jumlah baris yang terpengaruh | Menerima **string biasa** |

> [!CAUTION]
> Varian `Unsafe` menerima string yang bisa disusun dari input user, sehingga **rentan SQL Injection**. Gunakan hanya jika benar-benar perlu, dan jangan pernah menyambung input user langsung ke string.

Dengan tagged template (perhatikan **backtick** dan nilai `${...}` yang otomatis menjadi parameter aman):

```typescript
import type { User } from "../generated/prisma/client";

const result = await prisma.$queryRaw<User[]>`SELECT * FROM "User"`;
// result bertipe User[]
```

> Nama tabel `"User"` ditulis dengan **tanda kutip ganda** karena Postgres membedakan huruf besar/kecil. Tanpa kutip, `User` dibaca `user` (kata khusus di Postgres) dan query gagal.

Variabel sebagai parameter:

```typescript
const email = "budi@santoso.id";
const rows = await prisma.$queryRaw<User[]>`SELECT * FROM "User" WHERE email = ${email}`;
```

### Variabel tidak boleh di dalam tanda kutip SQL

`${name}` diubah Prisma menjadi **parameter**, bukan disisipkan sebagai teks. Jadi jika diletakkan di dalam string literal SQL, ia tidak akan menjadi `Ini Budi bukan Buni`:

```typescript
const name = "Budi";
await prisma.$queryRaw`SELECT 'Ini ${name} bukan Buni';`; // salah
```

Ada dua cara yang benar. Pertama, jadikan seluruh string sebagai variabel:

```typescript
const text = "Ini Budi bukan Buni";
await prisma.$queryRaw`SELECT ${text};`;
```

Kedua, gabungkan di sisi SQL dengan operator `||`:

```typescript
const name = "Budi";
await prisma.$queryRaw`SELECT 'Ini ' || ${name} || ' bukan Buni';`;
```

### Contoh nyata: jumlah post per tanggal

Ini yang tidak bisa dilakukan `groupBy` biasa (lihat catatan di bagian 15):

```typescript
const daily = await prisma.$queryRaw<{ day: Date; total: number }[]>`
  SELECT date_trunc('day', "createdAt") AS day, COUNT(*)::int AS total
  FROM "Post"
  GROUP BY day
  ORDER BY day
`;
```

> [!TIP]
> `COUNT(*)` di Postgres bertipe `bigint`, dan JavaScript tidak bisa mengubah `BigInt` menjadi JSON (error `Do not know how to serialize a BigInt`). Karena itu kita tulis `COUNT(*)::int`.

---

## 18. Testing API

Gunakan salah satu aplikasi berikut untuk mengirim request:

- [Postman](https://www.postman.com/downloads/) (sangat disarankan)
- [Apidog](https://apidog.com/)
- [Hoppscotch](https://hoppscotch.io/) (tanpa instalasi, langsung di browser)

Untuk request dengan body (`POST`/`PATCH`), pilih **Body → raw → JSON**, atau pastikan header `Content-Type: application/json`.

### Skenario uji yang disarankan

| # | Request | Hasil yang diharapkan |
| --- | --- | --- |
| 1 | `GET /health` | `200` Server dan database OK |
| 2 | `POST /api/users` dengan email, name, password | `201`, password **tidak** muncul di response |
| 3 | `POST /api/users` dengan email yang sama | `409 Conflict` |
| 4 | `POST /api/users` tanpa body | `400`, pesan "wajib diisi" |
| 5 | `POST /api/posts` dengan `authorId` yang valid | `201` |
| 6 | `POST /api/posts` dengan `authorId: 9999` | `404` User tidak ditemukan |
| 7 | `GET /api/posts?page=1&limit=2` | `200` dengan `metadata` pagination |
| 8 | `GET /api/posts?search=express` | Hanya post yang judulnya mengandung "express" |
| 9 | `GET /api/posts/stats` | `200` berisi aggregate dan groupBy |
| 10 | `GET /api/users/abc` | `400` ID harus bilangan bulat positif |
| 11 | `PATCH /api/posts/1` dengan `{ "published": true }` | `200`, `published` menjadi true |
| 12 | `DELETE /api/users/1` lalu `GET /api/posts` | Post milik user 1 ikut hilang (cascade) |
| 13 | `GET /ngawur` | `404` Endpoint tidak ditemukan |

Atau lewat terminal dengan `curl`:

```bash
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"email":"budi@santoso.id","name":"Budi","password":"rahasia123"}'

curl "http://localhost:3000/api/posts?page=1&limit=5"
```

---

## 19. Troubleshooting

| Gejala | Penyebab | Solusi |
| --- | --- | --- |
| `Cannot find module '../generated/prisma/client'` | Belum `prisma generate` (di Prisma 7 tidak otomatis), atau habis `git clone` | `npx prisma generate` |
| `P1001: Can't reach database server` | Neon sedang bangun dari _scale to zero_, internet putus, atau URL salah | Coba lagi beberapa detik kemudian. Tambahkan `&connect_timeout=15` di akhir URL. Cek koneksi internet |
| `P1010` / error SSL | Sertifikat atau parameter SSL | Pastikan URL berakhiran `?sslmode=require` |
| `Environment variable not found: DATABASE_URL_UNPOOLED` | `.env` tidak ada/salah nama/belum di root, dan Prisma 7 **tidak membaca `.env` otomatis** | Cek nama variabel, dan pastikan `prisma.config.ts` memakai `import "dotenv/config"` |
| `process.env.DATABASE_URL` bernilai `undefined` | `dotenv` belum dimuat | Pastikan `import "dotenv/config"` ada di `src/config/prisma.ts` dan `src/index.ts` |
| `EADDRINUSE: address already in use :::3000` | Ada server lain di port 3000 | Hentikan proses lama (Ctrl+C) atau ganti `PORT` di `.env` |
| `TypeError: Cannot destructure property ... of 'req.body' as it is undefined` | Di Express 5, `req.body` bisa `undefined` | Tulis `req.body ?? {}`, dan pastikan `express.json()` terpasang sebelum route |
| Request menggantung terus | Middleware lupa memanggil `next()` | Panggil `next()` atau kirim response |
| `Cannot set headers after they are sent to the client` | Satu request dijawab lebih dari sekali (mis. `res.json()` dua kali) | Pastikan tiap jalur hanya mengirim satu response, lihat [8.1](#81-objek-response-dan-method-nya) |
| `GET /api/posts/stats` dianggap `id = "stats"` | Route `/:id` ditulis sebelum `/stats` | Pindahkan route statis ke atas route dinamis |
| `Do not know how to serialize a BigInt` | `COUNT(*)` di raw query bertipe `bigint` | Gunakan `COUNT(*)::int` |
| `npx prisma` memakai versi 8 atau perintahnya tidak dikenali | Terinstal `prisma@latest` | `npm install -D prisma@7` |
| Error tipe di editor padahal kode benar | Prisma Client belum di-generate atau sudah usang | `npx prisma generate`, lalu restart TypeScript server di editor |
| Request pertama lambat (3 sampai 10 detik) | _Cold start_ Neon | Normal pada Free plan. Panggil `/health` dulu sebelum demo |

---

## 20. Challenge

Kerjakan di project yang sama untuk memperdalam materi.

1. **Model baru.** Tambahkan model `Comment` (relasi ke `Post` dan `User`, `onDelete: Cascade`). Jalankan `migrate dev`, `generate`, lalu buat CRUD-nya.
2. **Endpoint turunan.** Buat `GET /api/users/:id/posts` yang mengembalikan post milik satu user, lengkap dengan pagination dan filter `published`.
3. **Statistik per hari.** Buat `GET /api/posts/stats/daily` menggunakan raw query `date_trunc`.
4. **Bulk create.** Buat endpoint `POST /api/posts/bulk` yang menerima array post dan menyimpannya dengan `createMany`.
5. **Eksperimen Neon.** Buat sebuah _branch_ baru di Neon Console, arahkan `.env` ke branch tersebut, lalu jalankan migrasi. Amati bahwa data di branch utama tidak terpengaruh.
6. **Bonus.** Tambahkan validasi input yang lebih rapi (misalnya format email dan panjang password) dan tampilkan pesan error yang jelas.

---

## 21. Cheatsheet & Referensi

### Perintah yang sering dipakai

| Tujuan | Perintah |
| --- | --- |
| Jalankan server (auto-restart) | `npm run dev` |
| Cek tipe | `npm run typecheck` |
| Buat migrasi + jalankan ke database | `npx prisma migrate dev --name <nama>` |
| Generate Prisma Client | `npx prisma generate` |
| Dorong schema tanpa migrasi (eksperimen) | `npx prisma db push` |
| Lihat database di browser | `npx prisma studio` |
| Cek versi Prisma | `npx prisma -v` |

### Alur kerja saat mengubah struktur tabel

```mermaid
flowchart LR
    A["1. Edit<br/>schema.prisma"] --> B["2. npx prisma migrate dev<br/>--name nama_perubahan"]
    B --> C["3. npx prisma generate"]
    C --> D["4. Pakai field baru<br/>di controller"]
```

### Referensi

- [Express.js Documentation](https://expressjs.com/) dan [Panduan Migrasi ke Express 5](https://expressjs.com/en/guide/migrating-5/)
- [Prisma Documentation](https://www.prisma.io/docs) dan [Upgrade ke Prisma ORM 7](https://www.prisma.io/docs/guides/upgrade-prisma-orm/v7)
- [Neon: Connect from Prisma](https://neon.com/docs/guides/prisma) dan [Neon Plans](https://neon.com/docs/introduction/plans)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/) dan [Pengumuman TypeScript 7](https://devblogs.microsoft.com/typescript/)
- [Node.js Release Schedule](https://nodejs.org/en/about/releases/)
- [Prisma ORM release status](https://www.prisma.io/docs/orm/release-status)
