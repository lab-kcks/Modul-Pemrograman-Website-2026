# TypeScript & Vibe Coding dengan Coding Agent Harness

> Lanjutan dari **Modul 2 – JavaScript & TypeScript**.
> **Bagian A** menjelaskan konsep TypeScript secara menyeluruh. **Bagian B** menjelaskan cara memakai AI coding agent (*harness*) seperti Claude Code, Codex CLI, Gemini CLI, OpenCode, Oh My Pi, dan lainnya untuk mengerjakan proyek TypeScript dengan gaya *vibe coding*, tanpa kehilangan kontrol atas kode.

## Daftar Isi

**Bagian A – TypeScript**

1. [Apa itu TypeScript dan Bagaimana Cara Kerjanya](#1-apa-itu-typescript-dan-bagaimana-cara-kerjanya)
2. [Setup Proyek dan `tsconfig.json`](#2-setup-proyek-dan-tsconfigjson)
3. [Tipe Dasar dan Type Inference](#3-tipe-dasar-dan-type-inference)
4. [Fungsi](#4-fungsi)
5. [Object Types: `type`, `interface`, dan Kawan-kawan](#5-object-types-type-interface-dan-kawan-kawan)
6. [Union, Narrowing, dan Literal Types](#6-union-narrowing-dan-literal-types)
7. [Enum vs Union Literal](#7-enum-vs-union-literal)
8. [`null`, `undefined`, dan Optional Chaining](#8-null-undefined-dan-optional-chaining)
9. [`any`, `unknown`, `never`, dan Type Assertion](#9-any-unknown-never-dan-type-assertion)
10. [Generics](#10-generics)
11. [Utility Types, `keyof`, dan `typeof`](#11-utility-types-keyof-dan-typeof)
12. [Async/Await dan Error Handling di TypeScript](#12-asyncawait-dan-error-handling-di-typescript)
13. [Modul dan Organisasi File](#13-modul-dan-organisasi-file)
14. [Menjalankan Kode TypeScript](#14-menjalankan-kode-typescript)
15. [Membaca Pesan Error TypeScript](#15-membaca-pesan-error-typescript)
16. [Latihan TypeScript](#16-latihan-typescript)

**Bagian B – Vibe Coding dengan Harness**

17. [Apa itu Vibe Coding](#17-apa-itu-vibe-coding)
18. [Apa itu Harness](#18-apa-itu-harness)
19. [Daftar Harness Populer](#19-daftar-harness-populer)
20. [Perbandingan Harness](#20-perbandingan-harness)
21. [File Instruksi Proyek: `AGENTS.md` dan Teman-temannya](#21-file-instruksi-proyek-agentsmd-dan-teman-temannya)
22. [MCP (Model Context Protocol)](#22-mcp-model-context-protocol)
23. [Plugin: Memperluas Harness](#23-plugin-memperluas-harness)
24. [Kenapa TypeScript Cocok untuk Vibe Coding](#24-kenapa-typescript-cocok-untuk-vibe-coding)
25. [Hands-on: Task Tracker CLI](#25-hands-on-task-tracker-cli)
26. [Best Practice dan Jebakan Umum](#26-best-practice-dan-jebakan-umum)
27. [Latihan Harness](#27-latihan-harness)
28. [Referensi](#28-referensi)

---

# Bagian A – TypeScript

## 1. Apa itu TypeScript dan Bagaimana Cara Kerjanya

**TypeScript (TS)** adalah *superset* dari JavaScript: semua kode JavaScript yang valid juga valid di TypeScript. Tambahannya adalah **sistem tipe statis** yang memeriksa kode kalian **saat menulis/mengompilasi**, bukan saat program berjalan.

```text
 kode.ts  ──►  compiler (tsc)  ──►  kode.js  ──►  dijalankan di Node.js / Bun / browser
              │
              ├─ cek tipe  (error muncul di sini)
              └─ hapus semua anotasi tipe  (type erasure)
```

Poin penting yang sering salah dipahami:

| Fakta | Artinya |
| --- | --- |
| **Tipe hanya ada saat compile time** | Setelah dikompilasi, semua tipe dihapus. Di runtime yang berjalan tetap JavaScript biasa. |
| **TS tidak memvalidasi data saat runtime** | Kalau data dari API/file/pengguna bentuknya salah, TS tidak akan tahu. Validasi runtime perlu dilakukan terpisah. |
| **Browser dan Node tidak menjalankan `.ts` langsung** | Perlu *transpile* ke `.js` dulu (atau pakai runtime/tool yang melakukannya, lihat [bagian 14](#14-menjalankan-kode-typescript)). |
| **TS memakai *structural typing*** | Dua tipe dianggap cocok kalau **bentuknya** cocok, bukan karena namanya sama. |

### JavaScript vs TypeScript

| Aspek | JavaScript | TypeScript |
| --- | --- | --- |
| Tipe | Dinamis (dicek saat runtime) | Statis (dicek saat compile) |
| Error tipe | Baru ketahuan saat kode dijalankan | Ketahuan di editor/saat `tsc` |
| Tooling | Terbatas | Autocomplete, rename aman, navigasi kode lebih kuat |
| Kurva belajar | Lebih landai | Perlu belajar sintaks tipe tambahan |
| Cocok untuk | Skrip kecil, prototipe cepat | Proyek menengah–besar, kerja tim |

### Contoh bug yang ditangkap TypeScript

```js
// JavaScript: jalan tanpa error, hasilnya salah
function total(price, qty) {
  return price * qty;
}
total("10000", 3); // 30000 (string dipaksa jadi angka) — kebetulan benar
total("sepuluh", 3); // NaN — baru ketahuan setelah program jalan
```

```ts
// TypeScript: error langsung di editor
function total(price: number, qty: number): number {
  return price * qty;
}
total("sepuluh", 3);
// Error: Argument of type 'string' is not assignable to parameter of type 'number'.
```

### Structural typing

```ts
interface Point {
  x: number;
  y: number;
}

function show(p: Point) {
  console.log(p.x, p.y);
}

const dot = { x: 1, y: 2, label: "A" };
show(dot); // OK — bentuknya cocok (punya x dan y), properti ekstra tidak masalah

show({ x: 1, y: 2, label: "A" });
// Error: object literal yang ditulis langsung hanya boleh berisi properti yang dikenal
```

---

## 2. Setup Proyek dan `tsconfig.json`

```bash
mkdir proyek-ts && cd proyek-ts
npm init -y
npm install typescript @types/node --save-dev
npx tsc --init
mkdir src
```

`@types/node` memberi definisi tipe untuk API Node.js (`process`, `fs`, dsb.).

`tsconfig.json` yang disarankan untuk belajar dan proyek baru:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "rootDir": "src",
    "outDir": "dist",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

| Opsi | Fungsi |
| --- | --- |
| `target` | Versi JavaScript hasil kompilasi. |
| `module` / `moduleResolution` | Cara modul `import`/`export` diproses. `NodeNext` cocok untuk Node.js modern. |
| `rootDir` / `outDir` | Folder sumber `.ts` dan folder hasil `.js`. |
| `strict` | **Paling penting.** Mengaktifkan sekumpulan pemeriksaan ketat, termasuk `strictNullChecks` dan `noImplicitAny`. Selalu nyalakan. |
| `noUncheckedIndexedAccess` | Akses `arr[i]` bertipe `T \| undefined`, memaksa kalian menangani kemungkinan kosong. |
| `skipLibCheck` | Lewati pengecekan file `.d.ts` pustaka; mempercepat compile. |

> Catatan: jika kalian menambahkan `"type": "module"` di `package.json`, import relatif harus memakai ekstensi `.js`, misalnya `import { x } from "./storage.js"`.

Script yang berguna di `package.json`:

```json
{
  "scripts": {
    "build": "tsc",
    "typecheck": "tsc --noEmit",
    "start": "node dist/index.js"
  }
}
```

`tsc --noEmit` hanya mengecek tipe tanpa menghasilkan file; cepat dan sangat berguna sebagai "penguji otomatis".

---

## 3. Tipe Dasar dan Type Inference

### 3.1 Primitive types

```ts
let text: string = "Hello World!";
let num: number = 58.0;          // tidak ada int/float terpisah
let isTrue: boolean = false;
let big: bigint = 9007199254740993n;
let id: symbol = Symbol("id");
```

### 3.2 Type inference (tipe ditebak otomatis)

Tidak semua variabel perlu anotasi. TypeScript menyimpulkan tipe dari nilai awal:

```ts
let a = 10;          // number
let b = "hi";        // string
const c = "hi";      // tipe literal "hi" (karena const tidak bisa berubah)
let list = [1, 2, 3]; // number[]

a = "sepuluh";       // Error: string tidak bisa di-assign ke number
```

**Aturan praktis:** beri anotasi eksplisit pada **parameter fungsi**, **return type fungsi publik**, dan **struktur data**. Biarkan variabel lokal sederhana disimpulkan otomatis.

### 3.3 Array dan Tuple

```ts
let colors: string[] = ["Red", "Green", "Blue"];
let numbers: Array<number> = [1, 3, 5];     // sintaks generic, hasilnya sama

// Tuple: array dengan panjang dan tipe tiap posisi yang tetap
let pair: [string, number] = ["umur", 25];
let [label, value] = pair;                  // label: string, value: number
```

### 3.4 Tipe khusus

| Tipe | Arti | Contoh |
| --- | --- | --- |
| `void` | Fungsi tidak mengembalikan nilai berarti | `function log(): void {}` |
| `null` / `undefined` | Dua nilai "kosong" yang berbeda | lihat [bagian 8](#8-null-undefined-dan-optional-chaining) |
| `any` | Menonaktifkan pengecekan tipe | hindari |
| `unknown` | "Belum diketahui", harus dicek dulu sebelum dipakai | lihat [bagian 9](#9-any-unknown-never-dan-type-assertion) |
| `never` | Nilai yang tidak mungkin ada | lihat [bagian 9](#9-any-unknown-never-dan-type-assertion) |

---

## 4. Fungsi

```ts
// Parameter + return type
function greet(name: string): string {
  return `Hello, ${name}!`;
}

// Arrow function
const double = (n: number): number => n * 2;

// Parameter opsional (?) dan default value
function sapa(nama: string, sapaan = "Halo", gelar?: string): string {
  return `${sapaan}, ${gelar ? gelar + " " : ""}${nama}!`;
}
sapa("Rye");                   // "Halo, Rye!"
sapa("Rye", "Hai", "Mas");     // "Hai, Mas Rye!"

// Rest parameter
function sum(...nums: number[]): number {
  return nums.reduce((acc, n) => acc + n, 0);
}

// Tipe fungsi (cocok untuk callback)
type Operation = (a: number, b: number) => number;
const add: Operation = (a, b) => a + b;   // a dan b otomatis number

function apply(op: Operation, x: number, y: number): number {
  return op(x, y);
}

// Fungsi tanpa return nilai
function logMessage(msg: string): void {
  console.log(msg);
}
```

Catatan:

- Parameter opsional (`gelar?`) harus berada **setelah** parameter wajib.
- Tanpa anotasi, parameter fungsi bertipe `any` implisit, dan mode `strict` akan menolaknya (error `noImplicitAny`).
- Return type biasanya bisa disimpulkan, tapi menuliskannya pada fungsi publik membuat kontrak lebih jelas dan error lebih mudah dilacak.

---

## 5. Object Types: `type`, `interface`, dan Kawan-kawan

### 5.1 Object type inline

```ts
const user: { name: string; age: number } = {
  name: "John Doe",
  age: 25,
};
```

### 5.2 Type alias dan interface

```ts
// Type alias
type Location = {
  name: string;
  address: string;
  coordinate: { lat: number; lng: number };
};

// Interface
interface Product {
  name: string;
  price: number;
  isAvailable: boolean;
}
```

### 5.3 Optional dan readonly

```ts
interface Account {
  readonly id: number;   // tidak bisa diubah setelah dibuat
  username: string;
  nickname?: string;     // opsional: bisa string atau undefined
}

const acc: Account = { id: 1, username: "rye" };
acc.username = "rye2";   // OK
acc.id = 2;              // Error: cannot assign to 'id' because it is a read-only property
```

### 5.4 Index signature dan `Record`

Untuk objek dengan kunci dinamis:

```ts
interface Scores {
  [studentName: string]: number;
}
const scores: Scores = { budi: 90, ani: 85 };

// Bentuk yang lebih ringkas
const scores2: Record<string, number> = { budi: 90, ani: 85 };
```

### 5.5 Extending (pewarisan)

```ts
// Interface: extends
interface BaseObject {
  name: string;
  address: string;
}
interface ExtendedObject extends BaseObject {
  type: "HOME" | "OFFICE";
}

// Type alias: intersection (&)
type BaseObject2 = { name: string; address: string };
type ExtendedObject2 = BaseObject2 & { type: "HOME" | "OFFICE" };
```

### 5.6 `type` vs `interface`

| Aspek | `interface` | `type` |
| --- | --- | --- |
| Mendefinisikan bentuk objek | Ya | Ya |
| Extend | `extends` | Intersection `&` |
| **Declaration merging** (deklarasi nama sama digabung) | **Bisa** | Tidak bisa |
| Union, tuple, tipe primitif/alias | Tidak | **Bisa** |
| Mapped/conditional types | Tidak | **Bisa** |

```ts
// Declaration merging — hanya interface
interface Room { name: string; num: number }
interface Room { floor: number }
// Room sekarang punya name, num, dan floor

// Hanya type yang bisa union
type ID = string | number;
```

Pedoman umum: pakai **`interface`** untuk bentuk objek/kontrak yang mungkin di-extend; pakai **`type`** untuk union, tuple, dan tipe turunan. Yang terpenting: **konsisten dalam satu proyek**.

---

## 6. Union, Narrowing, dan Literal Types

### 6.1 Union

```ts
function showUserId(id: string | number) {
  console.log("User ID:", id);
}
showUserId("ff1a33c0-e73c");
showUserId(482);
```

Pada union, hanya anggota yang **dimiliki semua tipe** yang bisa dipakai langsung:

```ts
function show(id: string | number) {
  id.toUpperCase();
  // Error: Property 'toUpperCase' does not exist on type 'string | number'.
}
```

### 6.2 Narrowing (mempersempit tipe)

TypeScript memahami pengecekan kalian dan mempersempit tipe di dalam blok tersebut:

```ts
function show(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // id: string
  } else {
    console.log(id.toFixed(2));    // id: number
  }
}

function welcome(value: string[] | string) {
  if (Array.isArray(value)) {
    console.log("Hello, " + value.join(" and ")); // string[]
  } else {
    console.log("Welcome lone traveler " + value); // string
  }
}
```

Teknik narrowing lain:

| Teknik | Contoh |
| --- | --- |
| `typeof` | `typeof x === "number"` |
| `Array.isArray` | `Array.isArray(x)` |
| `instanceof` | `err instanceof Error` |
| `in` | `"meow" in animal` |
| Truthiness | `if (x) { ... }` |
| Kesamaan | `if (x === undefined) { ... }` |

### 6.3 Literal types

```ts
type TrafficLight = "red" | "yellow" | "green";

let light: TrafficLight = "yellow"; // OK
light = "blue";                     // Error: "blue" tidak termasuk
```

Berguna untuk mencegah salah ketik dan membatasi nilai yang valid.

### 6.4 Discriminated union

Pola paling berguna untuk memodelkan "beberapa kemungkinan bentuk data": tiap anggota punya properti penanda (`kind`) bertipe literal.

```ts
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number };

function area(s: Shape): number {
  switch (s.kind) {
    case "circle":
      return Math.PI * s.radius ** 2; // s: circle
    case "square":
      return s.side ** 2;             // s: square
  }
}
```

### 6.5 Custom type guard

```ts
interface Cat { meow(): void }
interface Dog { bark(): void }

function isCat(animal: Cat | Dog): animal is Cat {
  return "meow" in animal;
}

function speak(animal: Cat | Dog) {
  if (isCat(animal)) animal.meow(); // animal: Cat
  else animal.bark();               // animal: Dog
}
```

### 6.6 `as const`

```ts
const ROLES = ["admin", "user", "guest"] as const;
type Role = (typeof ROLES)[number]; // "admin" | "user" | "guest"

const config = { host: "localhost", port: 3000 } as const;
// config.port bertipe 3000 (literal) dan bersifat readonly
```

---

## 7. Enum vs Union Literal

```ts
// Numeric enum (mulai dari 0)
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right, // 3
}

// String enum (lebih mudah di-debug)
enum HttpStatus {
  OK = "OK",
  NotFound = "NOT_FOUND",
  InternalServerError = "INTERNAL_SERVER_ERROR",
}

function handle(status: HttpStatus) {
  if (status === HttpStatus.OK) console.log("Respons berhasil!");
}
handle(HttpStatus.OK);
```

Alternatif tanpa enum:

```ts
type HttpStatus2 = "OK" | "NOT_FOUND" | "INTERNAL_SERVER_ERROR";
```

| Aspek | `enum` | Union literal |
| --- | --- | --- |
| Menghasilkan kode JavaScript | **Ya** (objek di runtime) | Tidak (hilang saat compile) |
| Perlu di-import untuk dipakai | Ya | Tidak |
| Cocok untuk | Kumpulan konstanta bernama | Membatasi nilai string/angka |

Banyak tim modern memilih union literal karena lebih ringan; enum tetap valid dan umum dipakai. Pilih satu gaya, jangan dicampur sembarangan.

---

## 8. `null`, `undefined`, dan Optional Chaining

Dengan `strict` (tepatnya `strictNullChecks`), `null` dan `undefined` adalah tipe tersendiri:

```ts
let nama: string = "Budi";
nama = null;                     // Error

let namaOrNull: string | null = "Andi";
namaOrNull = null;               // OK
```

Operator yang wajib dikenal:

```ts
interface User {
  name: string;
  address?: { city: string };
}

const user: User = { name: "Rye" };

// Optional chaining ?. — berhenti dan mengembalikan undefined jika ada yang kosong
const city = user.address?.city;       // string | undefined

// Nullish coalescing ?? — nilai default hanya untuk null/undefined
const display = user.address?.city ?? "Tidak diketahui";

// Perbedaan dengan ||
const count = 0;
console.log(count || 10);  // 10  (0 dianggap falsy)
console.log(count ?? 10);  // 0   (0 bukan null/undefined)
```

**Non-null assertion (`!`)** memberitahu TS "percayalah, ini tidak null":

```ts
const el = document.getElementById("title")!; // hati-hati
```

Jika ternyata `null`, program error saat runtime. Lebih aman lakukan pengecekan:

```ts
const el = document.getElementById("title");
if (el) {
  el.textContent = "Judul baru";
}
```

---

## 9. `any`, `unknown`, `never`, dan Type Assertion

### 9.1 `any` — hindari

```ts
let obj: any = { count: 0 };
obj.foo();      // tidak error saat compile, meledak saat runtime
obj = "hello";
```

`any` mematikan fitur utama TypeScript dan menular ke kode lain yang menyentuhnya.

### 9.2 `unknown` — alternatif aman

`unknown` berarti "bisa apa saja, tapi harus dicek dulu sebelum dipakai".

```ts
const raw: unknown = JSON.parse('{"name":"Rye"}');

raw.name;
// Error: 'raw' is of type 'unknown'

if (typeof raw === "object" && raw !== null && "name" in raw) {
  console.log(raw.name); // OK setelah dicek
}
```

Gunakan `unknown` untuk data dari luar (API, file, input pengguna).

### 9.3 `never` — pengecekan kelengkapan

```ts
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number };

function area(s: Shape): number {
  switch (s.kind) {
    case "circle":
      return Math.PI * s.radius ** 2;
    case "square":
      return s.side ** 2;
    default: {
      const _exhaustive: never = s; // Error jika ada kasus yang belum ditangani
      return _exhaustive;
    }
  }
}
```

Kalau kelak ditambah `{ kind: "triangle" ... }` ke `Shape`, compiler langsung menunjukkan `switch` mana yang belum diperbarui.

### 9.4 Type assertion (`as`)

```ts
const canvas = document.getElementById("main_canvas") as HTMLCanvasElement;
canvas.getContext("2d");
```

Assertion **tidak mengubah apa pun saat runtime**; ia hanya membungkam compiler. Kalau salah, error muncul di browser, bukan saat compile. Pakai seperlunya, dan lebih baik sertai pengecekan:

```ts
const el = document.getElementById("main_canvas");
if (el instanceof HTMLCanvasElement) {
  el.getContext("2d"); // aman tanpa assertion
}
```

---

## 10. Generics

Generics memungkinkan satu fungsi/tipe bekerja untuk banyak tipe **tanpa kehilangan informasi tipe**.

```ts
// Tanpa generics: tipe hilang
function identityAny(value: any): any {
  return value;
}

// Dengan generics: tipe terjaga
function identity<T>(value: T): T {
  return value;
}

const str = identity<string>("Hello"); // string
const num = identity(123);             // number (disimpulkan otomatis)
```

### Generic pada array dan interface

```ts
function first<T>(items: T[]): T | undefined {
  return items[0];
}
first([1, 2, 3]);     // number | undefined
first(["a", "b"]);    // string | undefined

interface ApiResponse<T> {
  data: T;
  error?: string;
}

const res: ApiResponse<{ id: number; name: string }> = {
  data: { id: 1, name: "Rye" },
};
```

### Constraint (`extends`)

Membatasi tipe apa yang boleh dipakai:

```ts
function getLength<T extends { length: number }>(x: T): number {
  return x.length;
}
getLength("halo");     // 4
getLength([1, 2, 3]);  // 3
getLength(42);         // Error: number tidak punya 'length'
```

### Generic dengan `keyof`

```ts
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const u = { id: 1, name: "Rye" };
getProp(u, "name");  // string
getProp(u, "email"); // Error: "email" bukan kunci dari u
```

---

## 11. Utility Types, `keyof`, dan `typeof`

### 11.1 Utility types bawaan

Dasar: sebuah tipe `User`.

```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}
```

| Utility | Hasil | Contoh |
| --- | --- | --- |
| `Partial<T>` | Semua properti jadi opsional | `Partial<User>` |
| `Required<T>` | Semua properti jadi wajib | `Required<Partial<User>>` |
| `Readonly<T>` | Semua properti jadi readonly | `Readonly<User>` |
| `Pick<T, K>` | Ambil sebagian properti | `Pick<User, "id" \| "name">` |
| `Omit<T, K>` | Buang sebagian properti | `Omit<User, "password">` |
| `Record<K, V>` | Objek dengan kunci `K` dan nilai `V` | `Record<string, number>` |
| `ReturnType<F>` | Tipe return dari fungsi `F` | `ReturnType<typeof getUser>` |
| `Awaited<T>` | Tipe hasil dari `Promise` | `Awaited<Promise<string>>` |

```ts
function updateUser(id: number, data: Partial<User>) {
  // data boleh berisi sebagian field saja
}
updateUser(1, { name: "Nama Baru" });

type PublicUser = Omit<User, "password">;
type UserProfile = Pick<User, "id" | "name" | "email">;

function createUser(): User {
  return { id: 1, name: "Rye", email: "rye@mail.com", password: "x" };
}
type Created = ReturnType<typeof createUser>; // User
```

### 11.2 `keyof`, `typeof`, dan indexed access

```ts
type UserKeys = keyof User;       // "id" | "name" | "email" | "password"
type UserId = User["id"];         // number

const defaultUser = { id: 0, name: "Guest" };
type DefaultUser = typeof defaultUser; // { id: number; name: string }
```

`typeof` di posisi **tipe** mengambil tipe dari sebuah nilai; berbeda dengan `typeof` di posisi nilai (`typeof x === "string"`).

### 11.3 Mapped type (pengantar)

Utility types di atas dibangun dari mapped type:

```ts
type MyPartial<T> = {
  [K in keyof T]?: T[K];
};
```

Artinya: untuk setiap kunci `K` di `T`, buat properti opsional dengan tipe yang sama. Tidak perlu hafal sekarang; cukup tahu bahwa utility types bukan sihir.

---

## 12. Async/Await dan Error Handling di TypeScript

Konsep `Promise`, `async/await`, dan `try/catch` sama seperti di JavaScript (lihat Modul 2). Tambahannya hanya tipe:

```ts
interface User {
  id: number;
  name: string;
}

async function fetchUser(id: number): Promise<User> {
  const res = await fetch(`https://api.example.com/users/${id}`);
  if (!res.ok) {
    throw new Error(`HTTP ${res.status}`);
  }
  const data = (await res.json()) as User; // assertion: kita percaya bentuk datanya
  return data;
}
```

Hal penting:

- Fungsi `async` selalu mengembalikan `Promise<T>`; tulis return type-nya sebagai `Promise<T>`.
- `as User` **tidak memvalidasi** isi respons. Untuk data dari luar yang tidak sepenuhnya dipercaya, validasi bentuknya saat runtime (misalnya dengan library validasi skema seperti Zod, atau pengecekan manual).
- Variabel di `catch` bertipe `unknown` di mode `strict`, jadi harus dicek dulu:

```ts
try {
  const user = await fetchUser(1);
  console.log(user.name);
} catch (err) {
  if (err instanceof Error) {
    console.error("Gagal:", err.message);
  } else {
    console.error("Error tidak dikenal:", err);
  }
}
```

---

## 13. Modul dan Organisasi File

### 13.1 Export dan import

```ts
// src/types/user.ts
export interface User {
  id: number;
  name: string;
}

// src/services/user.ts
import type { User } from "../types/user"; // import khusus tipe (hilang saat compile)

export function formatUser(u: User): string {
  return `#${u.id} ${u.name}`;
}
```

`import type` memastikan yang diimpor hanya dipakai sebagai tipe dan tidak ikut ke JavaScript hasil kompilasi.

### 13.2 Pisahkan tipe ke folder khusus

Mencampur banyak `type`/`interface` dengan fungsi di satu file membuat file sulit dirawat. Struktur yang disarankan:

```text
src/
 ├─ types/
 │   ├─ animal.interface.ts
 │   ├─ post.interface.ts
 │   ├─ user.interface.ts
 │   └─ index.ts          // barrel file
 ├─ services/
 │   └─ user.ts
 └─ index.ts
```

`src/types/index.ts` (barrel) supaya impor cukup satu baris:

```ts
export * from "./animal.interface";
export * from "./post.interface";
export * from "./user.interface";
```

```ts
import type { User, Post } from "./types";
```

### 13.3 Definisi tipe pustaka

Pustaka JavaScript tanpa tipe bawaan biasanya punya paket tipe terpisah di npm dengan awalan `@types/`:

```bash
npm install --save-dev @types/node
```

---

## 14. Menjalankan Kode TypeScript

| Cara | Perintah | Catatan |
| --- | --- | --- |
| Kompilasi lalu jalankan (cara modul) | `npx tsc` lalu `node dist/index.js` | Standar untuk build produksi. |
| Kompilasi satu file cepat | `npx tsc src/index.ts` lalu `node src/index.js` | Seperti di modul; hasil `.js` di folder yang sama. |
| Jalankan langsung dengan `tsx` | `npx tsx src/index.ts` | Praktis untuk development; **tidak mengecek tipe**. |
| Jalankan langsung dengan Bun | `bun src/index.ts` | Bun menjalankan TypeScript secara native; juga tidak mengecek tipe. |
| Cek tipe saja | `npx tsc --noEmit` | Pasangkan dengan salah satu cara di atas. |

Penting: runtime yang "langsung menjalankan `.ts`" hanya membuang tipe, **tidak memeriksanya**. Selalu jalankan `tsc --noEmit` (atau biarkan editor/CI melakukannya) untuk benar-benar mendapat manfaat TypeScript.

---

## 15. Membaca Pesan Error TypeScript

Pesan error TypeScript sering panjang, tapi polanya terbatas. Baca dari **baris terakhir** (penyebab paling spesifik) lalu naik.

| Kode | Pesan (ringkas) | Biasanya karena | Cara memperbaiki |
| --- | --- | --- | --- |
| TS2322 | Type 'X' is not assignable to type 'Y' | Nilai tidak sesuai tipe target | Ubah nilai, ubah tipe target, atau lakukan konversi yang benar. |
| TS2339 | Property 'p' does not exist on type 'T' | Properti tidak ada / salah ketik / belum di-narrow | Cek ejaan, tambahkan properti ke tipe, atau narrowing. |
| TS2345 | Argument of type 'X' is not assignable to parameter of type 'Y' | Argumen salah tipe saat memanggil fungsi | Sesuaikan argumen atau signature fungsi. |
| TS7006 | Parameter 'x' implicitly has an 'any' type | Parameter belum diberi tipe | Tambahkan anotasi tipe. |
| TS18048 / TS2532 | 'x' is possibly 'undefined' | Nilai mungkin kosong | Cek dulu (`if`), pakai `?.` / `??`. |
| TS2304 | Cannot find name 'x' | Lupa import / salah nama / tipe pustaka belum terpasang | Tambahkan import atau paket `@types/...`. |

Kebiasaan yang baik: **perbaiki penyebabnya**, jangan membungkam error dengan `any`, `as`, atau `// @ts-ignore`.

---

## 16. Latihan TypeScript

1. **Tipe dasar.** Tulis fungsi `average(nums: number[]): number` yang menangani array kosong dengan benar (pikirkan tipe return yang tepat).
2. **Union + narrowing.** Buat fungsi `formatId(id: string | number): string` yang mengembalikan huruf besar untuk string dan angka berformat 6 digit (`000482`) untuk number.
3. **Discriminated union.** Modelkan `PaymentMethod` dengan tiga kemungkinan (`"card"`, `"transfer"`, `"cash"`), masing-masing dengan properti berbeda, lalu buat fungsi `describe(p: PaymentMethod): string` dengan pengecekan kelengkapan memakai `never`.
4. **Utility types.** Dari `interface Product { id: number; name: string; price: number; stock: number }`, turunkan: `ProductSummary` (hanya `id` dan `name`), `ProductUpdate` (semua opsional kecuali tidak boleh mengubah `id`), dan `ReadonlyProduct`.
5. **Generics.** Buat fungsi `groupBy<T, K extends string | number>(items: T[], key: (item: T) => K): Record<K, T[]>`.
6. **Async.** Tulis `fetchJson<T>(url: string): Promise<T>` yang melempar `Error` jika `res.ok` bernilai `false`. Diskusikan: apa yang *tidak* dijamin oleh `T` di sini?
7. **Refactor.** Ambil kode JavaScript dari bagian "Intermediate JavaScript" di modul (misalnya manipulasi array `fruits`), ubah ke TypeScript `strict`, dan catat error yang muncul.

---

# Bagian B – Vibe Coding dengan Harness

## 17. Apa itu Vibe Coding

**Vibe coding** dipopulerkan **Andrej Karpathy** (co-founder OpenAI, mantan Director of AI di Tesla) lewat postingan di X pada **2 Februari 2025**. Ia menggambarkan gaya kerja ketika kita "fully give in to the vibes": mendeskripsikan apa yang diinginkan ke AI, menjalankan hasilnya, menempelkan error kembali, dan sering menerima perubahan tanpa membaca detailnya. Karpathy sendiri menyebutnya cocok untuk proyek akhir pekan sekali pakai.

```text
Deskripsikan  →  AI menulis/mengubah kode  →  Jalankan  →  Lihat hasil/error  →  Arahkan lagi
     ↑                                                                              │
     └──────────────────────────────────────────────────────────────────────────────┘
```

| Cocok | Hati-hati / hindari |
| --- | --- |
| Prototipe, demo, hackathon | Kode keamanan (autentikasi, pembayaran, kriptografi) |
| Script sekali pakai, tool internal kecil | Sistem produksi dengan banyak pengguna |
| Mengeksplorasi library/ide baru | Kode yang dirawat banyak orang tanpa review |
| Boilerplate dengan pola umum | Domain yang kalian sendiri belum paham |

Praktik ini kemudian berkembang ke arah yang lebih terstruktur (sering disebut **agentic engineering**): tujuan jelas, konteks kaya (misalnya `AGENTS.md`), dan alur kerja terdefinisi. Dokumen ini mengajarkan versi kedua: **cepat seperti vibe coding, tapi ada guardrail.**

---

## 18. Apa itu Harness

Saat membuka Claude Code, Codex, OpenCode, atau Oh My Pi, kalian **tidak** bicara langsung dengan model mentah. Ada program di tengahnya yang disebut **harness** (arti harfiahnya "tali kekang").

```text
Kalian ──► HARNESS ──► Model (LLM)
            │
            ├─ system prompt & instruksi proyek (AGENTS.md, dll.)
            ├─ tools: baca file, edit, grep, jalankan command, cari web
            ├─ izin & sandbox (boleh edit? boleh jalankan apa?)
            ├─ manajemen sesi & konteks
            └─ integrasi (LSP, MCP, git, editor)
```

Pengalaman yang kalian rasakan = **model + harness**. Model yang sama bisa terasa jauh berbeda tergantung kualitas tools, prompt, dan alur kerja di harness-nya. Karena itu memilih harness sama pentingnya dengan memilih model.

---

## 19. Daftar Harness Populer

Daftar ini **tidak lengkap**; ekosistemnya berubah cepat. Perintah di bawah diambil dari dokumentasi/README masing-masing; selalu cek versi terbaru sebelum mengikuti.

### 19.1 Claude Code (Anthropic)

Agent coding dari Anthropic yang berjalan di terminal, juga tersedia sebagai aplikasi desktop dan ekstensi IDE (VS Code, JetBrains).

```bash
# macOS / Linux
curl -fsSL https://claude.ai/install.sh | bash
# macOS (Homebrew)
brew install --cask claude-code
# Windows
winget install Anthropic.ClaudeCode
```

```bash
cd proyek-ts
claude
```

- Login memakai akun Claude atau API key saat pertama dijalankan.
- File instruksi proyek: `CLAUDE.md`. Konfigurasi tambahan (agent, command) berada di folder `.claude/`.
- Model: keluarga Claude.

### 19.2 Codex CLI (OpenAI)

Agent coding dari OpenAI yang berjalan lokal di komputer kalian.

```bash
npm install -g @openai/codex
# atau
brew install --cask codex
```

```bash
cd proyek-ts
codex
```

- Login lewat "Sign in with ChatGPT" atau memakai API key.
- File instruksi: `AGENTS.md`. Codex membaca dan menggabungkan dari atas ke bawah: `~/.codex/AGENTS.md` (global), `AGENTS.md` di root repo, lalu `AGENTS.md` di folder kerja saat ini.
- Konfigurasi di `~/.codex/config.toml` (model, mode persetujuan, sandbox, MCP server).
- `/init` membuat kerangka `AGENTS.md`.
- Bisa memakai provider lain yang kompatibel dengan API OpenAI.

### 19.3 Gemini CLI (Google)

Agent terminal open source (Apache 2.0) dari Google.

```bash
npm install -g @google/gemini-cli
# atau
brew install gemini-cli
# atau tanpa install
npx @google/gemini-cli
```

```bash
cd proyek-ts
gemini
```

- Login: "Sign in with Google" atau API key.
- File konteks: `GEMINI.md` (global `~/.gemini/GEMINI.md`, atau per workspace). Mendukung impor file lain dengan `@./docs/architecture.md`. Nama file bisa diatur lewat `context.fileName` di `settings.json` (termasuk `AGENTS.md`).
- `/init` membuat `GEMINI.md`; `gemini -p "..."` untuk mode non-interaktif; konfigurasi di `~/.gemini/settings.json` (termasuk MCP server).

### 19.4 OpenCode

Agent coding **open source** untuk terminal, juga tersedia sebagai aplikasi desktop dan ekstensi IDE. Fleksibel soal provider.

```bash
curl -fsSL https://opencode.ai/install | bash
# atau
npm i -g opencode-ai@latest
brew install opencode
scoop install opencode      # Windows
```

```bash
cd proyek-ts
opencode
```

- `/init` menganalisis repo dan menulis `AGENTS.md`; **commit file ini ke git**.
- Dua agent bawaan, ganti dengan tombol `Tab`: **Build** (default, semua tool aktif) dan **Plan** (read-only untuk analisis dan perencanaan).
- Perintah bawaan: `/init`, `/undo`, `/redo`, `/share`, `/help`.
- Konfigurasi di `opencode.json` (global: `~/.config/opencode/opencode.json`; proyek: `opencode.json` atau `.opencode/opencode.json`). Format model: `provider/model-id`.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "provider/model-id"
}
```

Dokumentasi: <https://opencode.ai/docs>

### 19.5 Oh My Pi (`omp`)

Fork dari Pi dengan fitur "baterai termasuk", dikembangkan Stencil Labs, lisensi MIT, berbasis TypeScript/Bun dengan inti native Rust. Slogan: *a coding agent with the IDE wired in*.

```bash
# Bun (direkomendasikan, butuh bun >= 1.3.14)
bun install -g @oh-my-pi/pi-coding-agent
# macOS / Linux
curl -fsSL https://omp.sh/install | sh
# Homebrew
brew install can1357/tap/omp
# Windows (PowerShell)
irm https://omp.sh/install.ps1 | iex
```

```bash
cd proyek-ts
omp setup     # pilih model default
omp           # buka TUI interaktif
omp -p "jelaskan struktur proyek ini"   # sekali jalan
```

Fitur yang menonjol:

- **LSP terintegrasi**: diagnostik/rename lewat language server (cocok dengan TypeScript).
- **Hashline edits**: agent menunjuk *anchor* berbasis hash isi baris alih-alih menulis ulang baris; patch pada file yang sudah berubah ditolak sebelum merusak apa pun.
- **Subagent** (`task`), **advisor** (model kedua yang memantau tiap giliran), dan **role model** (`default`, `smol`, `slow`, `plan`, dll.).
- **60+ provider**, termasuk coding plan berlangganan dan model lokal (Ollama, LM Studio, llama.cpp, vLLM).
- Membaca konfigurasi tool lain (`.claude`, `.cursor`, `.codex`, dsb.) dan `AGENTS.md`.
- Perintah berguna: `/login`, `/model`, `/review`, `/collab`, `omp commit`.
- Kata kunci di prompt: `ultrathink`, `orchestrate`, `workflowz`.
- `/vibe` mengaktifkan *Vibe mode* (agent bertindak sebagai "director" atas worker `fast`/`good`). Nama ini kebetulan sama dengan istilah vibe coding, tapi merupakan fitur spesifik Oh My Pi.

Repositori: <https://github.com/can1357/oh-my-pi>

### 19.6 Pi (upstream)

**Pi** karya Mario Zechner adalah harness minimalis yang menjadi dasar Oh My Pi. Filosofinya: mulai dari inti kecil lalu rakit sendiri alat yang kalian butuhkan. Cocok bagi yang ingin kontrol penuh dan tidak suka fitur bawaan yang banyak. Paket npm-nya `@mariozechner/pi-coding-agent`; repositori: <https://github.com/badlogic/pi-mono>.

### 19.7 Aider

Alat **pair programming di terminal** yang *git-native*: setiap perubahan otomatis di-commit ke repo lokal dengan pesan commit deskriptif.

```bash
pipx install aider-chat
# atau
pip install aider-chat
# atau
brew install aider
```

```bash
cd proyek-ts      # harus berada di dalam repo git
aider src/index.ts
```

- Butuh API key dari minimal satu provider model.
- Memberi LLM "peta" seluruh repo untuk memahami codebase besar.
- Perintah dalam chat diawali `/`, misalnya `/drop <file>` dan `/undo` (membatalkan commit terakhir yang dibuat aider).
- Konfigurasi lewat `.aider.conf.yml`.

### 19.8 Lainnya

Ada banyak harness dan agent lain yang bisa kalian coba, misalnya agent berbasis editor (Cursor, Cline, Copilot agent mode) dan CLI lain (seperti Crush). Prinsipnya sama: **model + tools + konteks proyek + izin**.

---

## 20. Perbandingan Harness

| Harness | Pembuat | Lisensi | Provider model | File konteks proyek | Ciri khas |
| --- | --- | --- | --- | --- | --- |
| **Claude Code** | Anthropic | Produk Anthropic (bukan open source) | Claude | `CLAUDE.md` | Terminal, desktop, dan ekstensi IDE |
| **Codex CLI** | OpenAI | Open source | OpenAI (plus provider kompatibel) | `AGENTS.md` | Sandbox dan mode persetujuan, `config.toml` |
| **Gemini CLI** | Google | Apache 2.0 | Gemini | `GEMINI.md` | Impor file konteks, login Google |
| **OpenCode** | Anomaly | Open source | Banyak provider | `AGENTS.md` | Agent Plan vs Build, `/undo` `/redo` |
| **Oh My Pi** | Stencil Labs (fork Pi) | MIT | 60+ provider | `AGENTS.md` (+ format tool lain) | LSP, hashline edit, subagent, advisor |
| **Pi** | Mario Zechner | Open source | Banyak provider | (rakit sendiri) | Minimalis, bisa dirakit sesuai kebutuhan |
| **Aider** | Komunitas open source | Open source | Banyak provider | `.aider.conf.yml` | Auto-commit git, repo map |

### Cara memilih

- **Mau mulai paling cepat dengan langganan yang sudah dimiliki?** Pakai harness bawaan penyedianya (Claude Code, Codex CLI, Gemini CLI).
- **Mau bebas ganti-ganti model/provider?** OpenCode, Oh My Pi, Pi, atau Aider.
- **Mau alur plan → build yang jelas?** OpenCode (Plan/Build).
- **Mau banyak tool siap pakai (LSP, debugger, subagent)?** Oh My Pi.
- **Mau semua perubahan otomatis tercatat di git?** Aider.
- **Mau minimalis?** Pi.

Tidak ada yang paling benar. Coba dua atau tiga di proyek kecil yang sama lalu pilih yang cocok dengan gaya kerja kalian.

---

## 21. File Instruksi Proyek: `AGENTS.md` dan Teman-temannya

Hampir semua harness mendukung **file instruksi proyek**: teks Markdown yang dibaca agent di awal sesi (cara build, aturan kode, hal yang dilarang). Isinya yang menentukan seberapa "searah" agent dengan proyek kalian.

| Harness | File yang dibaca |
| --- | --- |
| Codex CLI | `AGENTS.md` (global di `~/.codex/`, root repo, folder saat ini; digabung) |
| OpenCode | `AGENTS.md` (dibuat lewat `/init`) |
| Oh My Pi | `AGENTS.md`, plus format tool lain |
| Gemini CLI | `GEMINI.md` (nama bisa diatur, termasuk `AGENTS.md`) |
| Claude Code | `CLAUDE.md` |

`AGENTS.md` makin umum dipakai sebagai konvensi lintas tool. Tips bila tim memakai beberapa harness: tulis aturan inti di **satu** `AGENTS.md`, lalu arahkan file lain ke sana (misalnya `CLAUDE.md` atau `GEMINI.md` berisi impor `@AGENTS.md` jika harness-nya mendukung impor, atau atur nama file di konfigurasi).

Template `AGENTS.md` untuk proyek TypeScript:

````markdown
# AGENTS.md

## Proyek
Task Tracker CLI berbasis Node.js + TypeScript. Data disimpan di `data/tasks.json`.

## Perintah
- Typecheck: `npm run typecheck`
- Build: `npm run build`
- Jalankan: `npm start -- <perintah>`

## Aturan kode
- TypeScript `strict`. **Dilarang** memakai `any`, `@ts-ignore`, atau `as` kecuali ada komentar alasannya.
- Semua tipe dan interface ada di `src/types/` dan diekspor lewat `index.ts`.
- Fungsi publik wajib punya type annotation untuk parameter dan return.
- Data dari luar (argumen CLI, isi JSON) bertipe `unknown` lalu divalidasi.
- Jangan menambah dependency baru tanpa meminta persetujuan.

## Alur kerja
1. Jelaskan rencana singkat sebelum mengedit.
2. Setelah mengedit, jalankan `npm run typecheck` dan perbaiki semua error.
3. Jangan mengubah file di luar yang dibutuhkan tugas.
````

---

## 22. MCP (Model Context Protocol)

Tool bawaan harness (lihat [bagian 18](#18-apa-itu-harness)) bekerja **di dalam folder proyek**: baca file, edit, grep, jalankan command. Begitu kalian butuh konteks dari luar seperti skema database, desain di Figma, issue di GitHub, hasil test di cloud agent jadi hilang arah. **MCP (Model Context Protocol)** mengisi celah itu: standar terbuka yang diperkenalkan Anthropic (akhir 2024) dan kini dikembangkan terbuka bersama komunitas, untuk menghubungkan aplikasi AI dengan sistem luar: data, tools, dan workflow.

### 22.1 Cara kerja singkat

```text
Kalian ──► HARNESS (MCP host) ──► MCP server ──► Layanan luar
              │
              ├─ 1 host, banyak client (satu client per server)
              └─ server lokal  atau remote (Streamable HTTP)
```

- **MCP host** adalah aplikasi AI-nya (harness kalian). Untuk setiap server yang dikonfigurasi, host membuka satu **MCP client** yang menjaga koneksinya.
- **MCP server** bisa berjalan **lokal** (proses di komputer masing masing) atau **remote** (diakses lewat HTTP dengan autentikasi).
- MCP bisa menawarkan tiga jenis aksi:

| Aksi | Arti | Contoh |
| --- | --- | --- |
| **Tools** | Aksi yang boleh dipanggil agent | jalankan query, klik halaman web, buat issue |
| **Resources** | Data konteks yang bisa dibaca | skema database, isi file, hasil analisis |
| **Prompts** | Template interaksi siap pakai | prompt khusus untuk debugging atau review |

> Agent tidak otomatis boleh semuanya: harness biasanya meminta izin sebelum tool MCP dijalankan, dan server berjalan dengan akses sebesar yang kalian berikan (token, kredensial). Detail protokol (JSON-RPC, versi spesifikasi, transport) tidak perlu dihafal — konsepnya saja yang penting.

### 22.2 Contoh penggunaan

Empat skenario yang paling sering muncul saat vibe coding:

1. **Database.** Sebelum menulis kode yang menyentuh data, agent membaca skema yang sebenarnya:

```text
Baca skema tabel tasks lewat MCP database, cocokkan dengan tipe Task
di src/types, lalu tulis migrasi untuk kolom priority.
```

2. **Testing (TestSprite).** TestSprite adalah MCP server testing otomatis dengan dua jenis tes: **UI/frontend** yaitu dengan alur bisnis end-to-end yang dijalankan dengan Playwright (navigasi, validasi form, auth flow, penanganan error UI) dan **API/backend** dengan workflow fungsional, validasi kontrak/skema, auth, integritas data, dan keamanan. Dari IDE, agent cukup diminta:

```text
Help me test this project with TestSprite.
```

Test plan dibuat otomatis, test dijalankan di cloud, laporan kegagalan beserta saran perbaikan kembali ke agent untuk ditindaklanjuti.

3. **Desain (Figma).** Lewat MCP server Figma, agent membaca frame yang dipilih dan menghasilkan kode dari situ. Hal ini termasuk memakai komponen design system asli lewat Code Connect, agar hasilnya konsisten dengan codebase.

4. **Repository (GitHub).** Agent membaca issue lewat GitHub MCP, mengimplementasikan perbaikannya, lalu membuka PR — tanpa pindah-pindah tab.

### 22.3 Contoh MCP yang berguna

| Kategori | MCP | Kegunaan |
| --- | --- | --- |
| Testing | **TestSprite** | Rencana + eksekusi test UI (E2E) dan API otomatis langsung dari IDE |
| Browser/E2E | **Playwright**, **Chrome DevTools** | Buka halaman, klik, isi form, baca console, membuat agent "melihat" web app |
| Database | **PostgreSQL**, **Supabase** | Baca skema dan jalankan query sebelum menulis kode |
| Repository | **GitHub** | Baca/tulis issue, PR, dan Actions; tersedia mode read-only dan `toolsets` untuk memangkas jumlah tool |
| Desain | **Figma** | Ambil frame, variabel, dan komponen desain sebagai konteks kode |
| Dokumentasi | **Context7** | Dokumentasi library selalu terbaru; mengurangi halusinasi API lama |
| Pencarian kode | **Grep by Vercel** | Cari contoh kode nyata dari repositori publik |
| Monitoring | **Sentry** | Baca error produksi dan stack trace langsung ke agent |
| Catatan/tiket | **Notion**, **Linear** | Baca PRD, spesifikasi, dan tiket sebagai konteks tugas |
| Umum (server referensi) | **Filesystem**, **Git**, **Fetch**, **Memory** | Operasi file, git, fetch web, memori ringan. Sebagai contoh resmi untuk mulai |

Katalog lengkapnya bisa dijelajahi di **MCP Registry** resmi: <https://registry.modelcontextprotocol.io/>.

### 22.4 Cara memasang

Format konfigurasi berbeda antar harness, tetapi isinya sama: nama server + cara menjalankannya (lokal) atau URL-nya (remote). Format klasik yang dipakai banyak client:

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    }
  }
}
```

Contoh di OpenCode (`opencode.json`), memakai server remote untuk dokumentasi:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

> Di Windows, sebagian client perlu `npx` dibungkus `cmd /c` — ikuti dokumentasi harness masing-masing.

| Harness | MCP dikonfigurasi di |
| --- | --- |
| Claude Code | `.mcp.json` di proyek, atau `claude mcp add` |
| Codex CLI | `~/.codex/config.toml` |
| Gemini CLI | `~/.gemini/settings.json` |
| OpenCode | `opencode.json`, di bawah kunci `mcp` |
| Oh My Pi | Mewarisi otomatis dari `.claude`, `.cursor`, `.gemini`, `.codex`, dan format lain |

Setelah terpasang, tool MCP muncul di samping tool bawaan. Trik kecil: sebut namanya di prompt ("...use context7"), atau tulis aturan pemakaiannya di `AGENTS.md` (lihat [bagian 21](#21-file-instruksi-proyek-agentsmd-dan-teman-temannya)).

### 22.5 Apakah MCP penting?

Bisa dibilang penting, bisa dibilang tidak — **tergantung kebutuhan**.

| Pakai MCP bila… | Lewati dulu bila… |
| --- | --- |
| Butuh konteks nyata di luar repo: skema DB, desain, issue, PRD | Semua konteks memang sudah ada di dalam folder proyek |
| Butuh aksi di luar repo: test E2E, buka PR, cek error produksi | Proyek kecil/latihan yang cukup dengan `AGENTS.md` + typecheck |
| Tim memakai tool tertentu dan ingin agent mengikuti alurnya | MCP-nya belum dipahami cara kerja dan risikonya |

**Efek kalau MCP-nya banyak.** MCP bukan gratis, namun tiap MCP membawa biayanya tersendiri

---

## 23. Plugin: Memperluas Harness

### 23.1 Plugin di coding agent: apa bedanya dengan MCP?


| | MCP | Plugin |
| --- | --- | --- |
| Apa itu | **Protokol** untuk menyambungkan agent ke layanan luar | **Paket distribusi** yang memperluas harness itu sendiri |
| Isi | Tools, resources, prompts | Skills, commands, agents, hooks, tema bisa juga **membungkus MCP server** |
| Satuan | Satu server = satu pintu ke satu layanan | Satu plugin = banyak komponen, dipasang dan diperbarui sebagai satu unit |
| Analogi | Kabel USB-C | Paket perangkat siap colok |

```text
PLUGIN (satu unit install)
 ├─ skills / commands   → instruksi dan alur kerja siap pakai
 ├─ agents              → subagent khusus
 ├─ hooks               → aksi otomatis (mis. typecheck setelah edit)
 └─ MCP server(s)       → pintu ke layanan luar
```

### 23.2 Contoh plugin populer

| Plugin | Harness | Kegunaan |
| --- | --- | --- |
| `commit-commands` | Claude Code | Perintah commit, push, dan buka PR (marketplace resmi) |
| `opencode-notify` | OpenCode | Notifikasi OS saat tugas atau sesi selesai |
| `opencode-type-inject` | OpenCode | Menyuntikkan tipe TypeScript/Svelte ke hasil pembacaan file |
| `opencode-supermemory` | OpenCode | Memori persisten lintas sesi |
| `oh-my-opencode` | OpenCode | Background agents + tool LSP/AST/MCP tambahan |
| Workspace extension | Gemini CLI | Integrasi Google Workspace (Docs, Sheets, Drive) |
| Codex Security | Codex | Scan keamanan kode beserta rekomendasi perbaikan |

Ekosistemnya berubah cepat; selalu cek marketplace resmi harness yang kalian pakai.

### 23.3 Cara memasang plugin

**Claude Code** marketplace resmi sudah terdaftar otomatis:

```text
/plugin install commit-commands@claude-plugins-official
# atau dari shell:
claude plugin install commit-commands@claude-plugins-official --scope project
```

**Gemini CLI** (di sana namanya *extensions*):

```bash
gemini extensions install https://github.com/gemini-cli-extensions/workspace
gemini extensions list      # cek yang sudah terpasang
```

**OpenCode** — lewat paket npm di `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-notify"]
}
```

atau dengan menaruh file JS/TS di `.opencode/plugins/`.

**Codex CLI** plugin browser:

```text
/plugins    # di dalam sesi codex: telusuri, install, mulai sesi baru
```

**Oh My Pi dan Pi** — extension TypeScript / paket yang dibagikan lewat npm atau git di Oh My Pi dimuat ulang dengan `/reload-plugins`.

### 23.4 Kompatibilitas plugin antar harness

| Harness | Dukungan plugin | Mekanisme | Catatan |
| --- | --- | --- | --- |
| Claude Code | Ya | Marketplace berisi skills, agents, hooks, MCP server (format `.claude-plugin/`) | Jalan di terminal, desktop, dan VS Code. Namun sesi cloud tidak memuat plugin lokal |
| Codex CLI | Ya | Plugin browser `/plugins`; berisi skills + MCP server | **Ekstensi IDE Codex tidak mendukung plugin** |
| Gemini CLI | Ya (namanya *extensions*) | `gemini extensions install` + galeri resmi | Mengemas prompts, commands, hooks, subagents, skills, MCP |
| OpenCode | Ya | Plugin JS/TS di `.opencode/plugins/` atau paket npm | Ditulis dengan TypeScript, tipe dari `@opencode-ai/plugin` |
| Oh My Pi | Ya | Extension TypeScript: lokal, marketplace, atau npm | Mewarisi aturan, skills, dan MCP dari `.claude`, `.cursor`, `.windsurf`, `.gemini`, `.codex`, `.cline`, dan lainnya |
| Pi | Ya (namanya *packages*) | Ekstensi/skills/template dibundel lalu dibagikan via npm atau git | Tetap minimalis: yang tidak ada, rakit sendiri |
| Aider | **Tidak** | — | Tidak ada sistem plugin/marketplace, namun dapa konfigurasi lewat `.aider.conf.yml` |
| Agent editor (Cursor, Cline, Copilot) | Sebagian | Ekstensi dan marketplace masing-masing + MCP | Bukan format plugin lintas-harness |

Format plugin **belum ada standar lintas harness** plugin Claude Code tidak jalan di OpenCode, begitu pula sebaliknya. Arahnya memang konvergen: Oh My Pi membaca konfigurasi delapan tool lain, dan direktori plugin OpenAI menerima plugin berformat Claude Code. Komponen yang paling portabel saat ini justru yang paling sederhana: `AGENTS.md`, MCP, dan skills.

Setelah agent kaya konteks (MCP) dan kaya alur kerja (plugin), lapisan terakhir yang membuat semuanya terkendali adalah bahasanya sendiri: TypeScript. Itulah bahasan bagian berikutnya.

---

## 24. Kenapa TypeScript Cocok untuk Vibe Coding

1. **Type checker = penguji otomatis yang objektif.** Agent bisa menjalankan `npx tsc --noEmit`, membaca error, lalu memperbaiki dirinya sendiri. Di JavaScript murni, banyak bug baru muncul saat runtime.
2. **Tipe adalah spesifikasi yang bisa dibaca mesin.** Kalian menulis `interface` dan signature fungsi; agent mengisi implementasinya. Tipe membatasi ruang kesalahan agent.
3. **LSP dan autocomplete.** Harness yang terhubung ke language server (seperti Oh My Pi) memberi agent informasi yang sama dengan editor kalian: definisi, referensi, rename aman.
4. **Refactor lebih aman.** Mengubah bentuk data memunculkan error di semua tempat yang terdampak.
5. **Review lebih cepat.** Membaca tipe dan signature jauh lebih cepat daripada membaca seluruh isi fungsi.

Syarat agar manfaat ini nyata: aktifkan `"strict": true` dan **larang `any`, `@ts-ignore`, serta `as` sembarangan** di `AGENTS.md`. Tanpa itu, agent bisa "lolos" dengan menyembunyikan masalah. Karena itulah pemahaman Bagian A penting: kalian harus bisa menilai apakah solusi agent memperbaiki masalah atau hanya membungkam compiler.

---

## 25. Hands-on: Task Tracker CLI

Pola kerja (berlaku untuk harness mana pun): **tipe dulu (oleh kalian) → rencana → implementasi bertahap → verifikasi → commit.**

### Langkah 1 – Siapkan proyek

```bash
mkdir task-tracker && cd task-tracker
git init
npm init -y
npm install typescript @types/node --save-dev
npx tsc --init      # lalu samakan dengan tsconfig di bagian 2
mkdir -p src/types
```

Tambahkan script `build`, `typecheck`, dan `start` seperti di [bagian 2](#2-setup-proyek-dan-tsconfigjson), lalu buat `.gitignore` berisi `node_modules`, `dist`, dan `.env`.

### Langkah 2 – Tulis kontrak tipe sendiri

`src/types/task.ts`:

```ts
export type TaskStatus = "todo" | "doing" | "done";

export interface Task {
  id: number;
  title: string;
  status: TaskStatus;
  dueDate?: string; // format ISO: 2026-10-31
}

export type NewTask = Omit<Task, "id" | "status">;
export type TaskPatch = Partial<Omit<Task, "id">>;
```

`src/types/index.ts`:

```ts
export * from "./task";
```

Bagian ini sengaja ditulis manual: kalian yang mendefinisikan *apa* yang dibangun, agent mengurus *bagaimana*.

### Langkah 3 – Buat file instruksi

Salin template `AGENTS.md` dari [bagian 21](#21-file-instruksi-proyek-agentsmd-dan-teman-temannya) (atau jalankan `/init` di harness yang mendukungnya lalu edit hasilnya).

### Langkah 4 – Minta rencana dulu

Gunakan mode read-only/rencana jika tersedia (OpenCode: agent **Plan** via `Tab`; Oh My Pi: role/mode `plan`; harness lain: minta secara eksplisit).

```text
Baca src/types dan AGENTS.md. Buat rencana implementasi Task Tracker CLI
dengan perintah: add, list, done, remove. Pecah jadi file-file kecil di src/.
Jangan edit apa pun dulu. Sebutkan juga risiko dan edge case.
```

Baca rencananya. Jika ada yang aneh (dependency tak perlu, struktur terlalu rumit), koreksi **sebelum** ada kode.

### Langkah 5 – Implementasi bertahap

Satu prompt, satu hasil yang bisa diverifikasi:

```text
Implementasikan src/storage.ts: fungsi loadTasks() dan saveTasks() untuk
data/tasks.json. Tangani file belum ada dan JSON rusak. Data hasil parse
bertipe unknown lalu divalidasi sebelum dikembalikan sebagai Task[].
Setelah selesai, jalankan npm run typecheck.
```

```text
Sekarang implementasikan src/commands.ts untuk add, list, done, remove,
memakai tipe NewTask, TaskPatch, dan TaskStatus dari src/types.
```

```text
Terakhir buat src/index.ts sebagai entry point yang membaca process.argv
dan memanggil command yang sesuai. Tampilkan usage jika perintah tidak dikenal.
```

### Langkah 6 – Verifikasi sendiri

Jangan hanya percaya laporan "sudah selesai":

```bash
npm run typecheck
npm run build
npm start -- add "Belajar TypeScript"
npm start -- list
npm start -- done 1
npm start -- list
git diff
```

Pertanyaan review cepat:

- Adakah `any`, `as`, atau `@ts-ignore` yang tidak perlu? (`grep -rnE "\bany\b|@ts-ignore| as " src/`)
- Apakah input pengguna dan isi JSON divalidasi, bukan dipercaya begitu saja?
- Adakah file atau dependency yang tidak kalian minta?
- Bisakah kalian menjelaskan tiap fungsi dengan kata-kata sendiri?

### Langkah 7 – Commit kecil dan sering

```bash
git add -A
git commit -m "feat: add storage and task commands"
```

Commit setelah tiap langkah yang berhasil agar mudah rollback (`git restore` / `git reset`). Banyak harness juga punya fitur undo sendiri (mis. `/undo` di OpenCode dan Aider).

### Langkah 8 – Iterasi dengan error nyata

```text
Saat menjalankan `npm start -- done 99` muncul error berikut:
<tempel error lengkap>
Harapannya: tampil pesan "Task tidak ditemukan" dan exit code 1.
Perbaiki tanpa mengubah perilaku command lain, lalu jalankan typecheck.
```

---

## 26. Best Practice dan Jebakan Umum

### Praktik yang disarankan

- **Beri konteks, bukan hanya perintah.** Tujuan, batasan, dan file terkait. Kegagalan paling umum adalah konteks yang kurang, bukan modelnya.
- **Pecah tugas kecil.** Satu prompt, satu hasil yang bisa diverifikasi.
- **Rencanakan sebelum eksekusi.**
- **Tulis aturan di `AGENTS.md`** supaya tidak mengulang instruksi tiap sesi.
- **Biarkan compiler jadi hakim.** Wajibkan `typecheck` (dan lint/test jika ada) setelah setiap perubahan.
- **Commit sering.**
- **Pahami sebelum merge.** Jika tidak bisa menjelaskan kodenya, jangan kirim ke produksi.

### Jebakan yang sering terjadi

| Jebakan | Pencegahan |
| --- | --- |
| Agent menghalusinasi fungsi/package yang tidak ada | Jalankan `typecheck` dan build; pastikan package benar-benar ada di npm sebelum diinstal. |
| `any`, `as`, `@ts-ignore` menyusup | Larang di `AGENTS.md`; cari dengan `grep`. |
| Tipe benar tapi data runtime salah | Validasi data dari luar (ingat: tipe hilang saat runtime). |
| Rahasia (API key, `.env`) terkirim ke provider atau ter-commit | Jangan tempel secret di prompt; `.env` masuk `.gitignore`. |
| Command berbahaya dijalankan otomatis | Atur izin/sandbox agent; jangan "accept all" untuk perintah yang menghapus atau mengubah sistem. |
| Prompt injection dari file/halaman web yang dibaca agent | Anggap isi eksternal sebagai data, bukan instruksi; periksa perubahan yang tidak kalian minta. |
| Kode jalan di demo tapi rapuh | Tambahkan validasi input dan test untuk jalur gagal. |
| Kalian tidak lagi paham proyeknya | Baca `git diff`; minta agent menjelaskan bagian yang tidak jelas. |
| Biaya token membengkak | Pakai model murah untuk tugas ringan, batasi konteks, tutup sesi yang selesai. |

> **Makin penting dan makin lama umur kodenya, makin sedikit "vibe" dan makin banyak review.**

---

## 27. Latihan Harness

1. **Instal dan kenali.** Pasang satu harness, jalankan di proyek `task-tracker`, dan minta ia menjelaskan struktur proyek. Catat file instruksi mana yang ia baca.
2. **Konteks.** Tulis `AGENTS.md` (atau jalankan `/init`), lalu bandingkan hasil permintaan yang sama **dengan** dan **tanpa** file itu.
3. **Fitur baru.** Tambahkan field `priority: "low" | "medium" | "high"` ke `Task`. Biarkan compiler menunjukkan semua tempat yang rusak, lalu minta agent memperbaikinya.
4. **Perbandingan.** Kerjakan fitur yang sama (`list --status done`) di dua harness berbeda. Bandingkan kualitas rencana, jumlah error `tsc`, banyaknya intervensi manual, dan kenyamanan alur kerja. Tulis kesimpulan satu paragraf.
5. **Audit.** Cari satu tempat di hasil agent yang memakai `as` atau `any`. Ganti dengan tipe yang benar (union, type guard, atau generics).

---

## 28. Referensi

**TypeScript**
- Dokumentasi resmi: <https://www.typescriptlang.org/docs/>
- Utility types: <https://www.typescriptlang.org/docs/handbook/utility-types.html>
- Modul 2 – JavaScript & TypeScript (materi praktikum)

**Vibe coding**
- Postingan asli Karpathy, 2 Feb 2025 (dikutip di): <https://superwhisper.com/vibe-coding>
- Glosarium vibe coding dan agentic engineering: <https://codegen.com/blog/glossary/vibe-coding>
- Asal-usul istilah: <https://knowyourmeme.com/memes/vibe-coding>

**Harness**
- Claude Code (instalasi): <https://code.claude.com/docs/en/setup>
- Codex CLI: <https://github.com/openai/codex> · panduan AGENTS.md: <https://developers.openai.com/codex/guides/agents-md>
- Gemini CLI: <https://github.com/google-gemini/gemini-cli>
- OpenCode: <https://opencode.ai/docs> · <https://github.com/anomalyco/opencode>
- Oh My Pi: <https://github.com/can1357/oh-my-pi> · <https://omp.sh> · artikel "The Harness Problem": <https://blog.can.ac/2026/02/12/the-harness-problem/>
- Pi: <https://github.com/badlogic/pi-mono>
- Aider: <https://aider.chat>
- Kesan pertama memakai Oh-My-Pi dan OpenCode (Bahasa Portugis): <https://akitaonrails.com/2026/05/25/primeiras-impressoes-usando-oh-my-pi-e-opencode/>

**MCP**
- Dokumentasi resmi: <https://modelcontextprotocol.io> · arsitektur: <https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture>
- MCP Registry (katalog server): <https://registry.modelcontextprotocol.io/>
- Server referensi resmi: <https://github.com/modelcontextprotocol/servers>
- TestSprite MCP: <https://docs.testsprite.com/mcp/getting-started/overview>
- Figma MCP Server: <https://developers.figma.com/docs/figma-mcp-server/>
- GitHub MCP Server: <https://github.com/github/github-mcp-server>

**Plugin**
- Claude Code (plugins): <https://code.claude.com/docs/en/plugins> · instalasi: <https://code.claude.com/docs/en/plugins/install>
- Codex CLI (plugins): <https://developers.openai.com/codex/plugins>
- Gemini CLI (extensions): <https://github.com/google-gemini/gemini-cli/blob/main/docs/extensions/index.md>
- OpenCode (plugins): <https://opencode.ai/docs/plugins/> · ekosistem: <https://opencode.ai/docs/ecosystem/>
- Asal usul istilah plug-in: <https://en.wikipedia.org/wiki/Plug-in_(computing)>

> Catatan: alat AI coding berubah sangat cepat — termasuk ekosistem MCP dan plugin. Perintah, nama fitur, dan ketersediaan di atas diambil dari sumber per Oktober 2026; verifikasi ke dokumentasi resmi sebelum mengikuti langkah-langkahnya.

Happy Coding! 🚀
