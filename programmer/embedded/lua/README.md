Mau jadi master Lua? Mulailah dari akar dokumentasi resminya. Pertaman antara Lua 5.0 dan Lua 5.4.6 ada empat rilis minor besar (5.1, 5.2, 5.3, dan 5.4), masing-masing memperkenalkan perubahan-perubahan signifikan di bahasa, pustaka, dan API C. Jika kita hanya menghitung **fitur‐fitur utama** yang dicantumkan di tiap “readme” resmi. **Tip cepat:** Selalu sesuaikan versi manual dengan versi interpreter Lua yang kamu pakai. Jangan sampai membaca doc 5.4 sementara runtime-mu masih 5.1—bisa bikin kepala cenut! 😉

1. **Manual Resmi (Reference Manual)**
   – Lua 5.4: [https://www.lua.org/manual/5.4/](https://www.lua.org/manual/5.4/)
   – Daftar versi lain (5.1, 5.2, 5.3): [https://www.lua.org/manual.html](https://www.lua.org/manual.html)
   Di sini kamu dapat membaca spesifikasi Bahasa Lua, library standar, dan behaviour tiap fungsi built-in.

2. **Tutorial “Programming in Lua”**
   Buku resmi yang ditulis oleh pencipta Lua, Roberto Ierusalimschy.
   – Edisi online (Lua 5.3): [https://www.lua.org/pil/](https://www.lua.org/pil/)
   Versi cetak juga tersedia kalau kamu suka baca “hard copy”.

3. **Lua-users Wiki**
   Kumpulan tips, trik, contoh kode, dan FAQ dari komunitas:
   [https://lua-users.org/wiki/](https://lua-users.org/wiki/)

4. **Source Code & Langkah Build**
   Kalau mau dalami implementasi, cek kode sumber di:
   [https://www.lua.org/source.html](https://www.lua.org/source.html)

- **Lua 5.1 (21 Feb 2006)**:

  1. Modul system baru
  2. Garbage collector inkremental
  3. Mekanisme varargs baru
  4. Syntax baru untuk long strings & komentar
  5. Operator `mod` (%) dan `length` (#)
  6. Metatable untuk semua tipe
  7. Konfigurasi via `luaconf.h`
  8. Parser yang sepenuhnya reentrant ([lua.org][1])

- **Lua 5.2 (05 Des 2011)**:

  1. `pcall` & metamethods bisa di-`yield`
  2. Skema leksikal untuk global (`_ENV`)
  3. Ephemeron tables
  4. Pustaka bitwise ops (`bit32`)
  5. Light C functions
  6. Emergency garbage collector
  7. Statement `goto`
  8. Finalizer untuk table ([lua.org][2])

- **Lua 5.3 (12 Jan 2015)**:

  1. Tipe integer 64-bit (default)
  2. Dukungan resmi untuk 32-bit numbers
  3. Operator bitwise bawaan
  4. Dukungan dasar UTF-8
  5. Fungsi pack/unpack (seri nilai) ([lua.org][3])

- **Lua 5.4 (02 Mei 2023, tepatnya 5.4.6)**:

  1. Mode generasional untuk GC
  2. Variabel “to-be-closed”
  3. Variabel `const`
  4. Userdata dapat banyak user values
  5. Implementasi baru `math.random`
  6. Sistem peringatan (warning)
  7. Info debug argumen & return
  8. Semantik baru untuk `for` integer
  9. Argumen opsional `init` pada `string.gmatch`
  10. Fungsi `lua_resetthread` & `coroutine.close`
  11. Koersi string→number dipindah ke pustaka string ([lua.org][4])

**Total fitur utama**: 8 (5.1) + 8 (5.2) + 5 (5.3) + 11 (5.4) = **32 perubahan besar**.

Lua sendiri sejak awal dirancang sebagai bahasa _multi-paradigm_: utamanya **prosedural** dengan fasilitas **data description**, tapi juga mendukung **pemrograman fungsional** (first-class functions, closures), **object-oriented** ringan lewat tabel & metatable, serta **coroutines** untuk _collaborative multithreading_ ([Lua][1], [Wikipedia][2]). Sepanjang seri 5.x—dari 5.0 ke 5.4—karakternya tetap sama, tetapi **fitur inti** dan **mekanisme implementasi** berkembang cukup signifikan.

---

## 1. Lua 5.1 (rilis 21 Feb 2006)

**Fitur baru utama** (dibanding 5.0) ([Lua][3]):

- **Module system**: `module`/`require`, memudahkan pemisahan kode
- **Incremental GC**: pengumpulan sampah berjalan bertahap
- **Varargs**: `...` dengan `select` & `arg`
- **Long strings/komentar**: `[[ … ]]`
- **Operator `%` (mod) & `#` (length)**
- **Metatable untuk semua tipe**
- **Konfigurasi via `luaconf.h`**
- **Parser reentrant**

### Contoh kode 5.1

```lua
-- modul sederhana di file mymodule.lua
local M = {}
function M.greet(name)
  return "Halo, " .. name
end
return M

-- di main.lua
local mod = require("mymodule")
print(mod.greet("Pelajar"))
```

---

## 2. Lua 5.2 (rilis 16 Des 2011)

**Perubahan sejak 5.1** ([Lua][4]):

- `pcall` & metamethods **bisa di-yield**
- Skema global baru via **\_ENV**
- **Ephemeron tables** (weak-key/value advanced)
- **Bitwise ops** di pustaka `bit32`
- **Light C functions**
- **Emergency GC**
- **`goto`** statement
- **Finalizers** untuk tabel

### Contoh kode 5.2

```lua
-- skema _ENV: fungsi terisolasi
local _ENV = {print = print}  -- batasi global
function f()
  print(x)        -- x tidak di-resolve ke global luar
end

-- goto & label
local i = 1
::loop_start::
if i > 3 then return end
print(i)
i = i + 1
goto loop_start
```

---

## 3. Lua 5.3 (rilis 12 Jan 2015)

**Perubahan sejak 5.2** ([Lua][5]):

- **Tipe integer** (64-bit default)
- Dukungan 32-bit numbers
- **Operator bitwise** built-in (`&`, `|`, `~`, `<<`, `>>`)
- **UTF-8** library dasar (`utf8.*`)
- **Pack/unpack** nilai (`string.pack`, `string.unpack`)

### Contoh kode 5.3

```lua
-- integer vs float
local i = 5    -- integer
local f = 2.5  -- float
print(i + f)   -- 7.5

-- bitwise
print((5 & 3), (5 << 1))  -- 1, 10

-- packing
local s = string.pack("<I4I4", 100, 200)
local a, b = string.unpack("<I4I4", s)
print(a, b)  -- 100, 200
```

---

## 4. Lua 5.4 (rilis 02 Mei 2023; sekarang 5.4.6)

**Perubahan sejak 5.3** ([Lua][6]):

1. **Generational GC**
2. **To-be-closed variables** (`<close>`)
3. **`const` variables**
4. Userdata bisa **many user values**
5. `math.random` baru
6. **Warning system**
7. Debug info args & return
8. Semantik baru untuk **`for` integer**
9. Argumen opsional `init` di `string.gmatch`
10. Fungsi `lua_resetthread` & `coroutine.close`
11. Koersi string→number dipindah ke pustaka _string_
    …+ perbaikan minor & API tweaks

### Contoh kode 5.4

```lua
-- to-be-closed: memastikan file ditutup
local f <close> = io.open("data.txt", "r")
for line in f:lines() do
  print(line)
end  -- f:close() otomatis

-- const
local const x = 10
-- x = 5  --> error: cannot assign to const ‘x’

-- warning system
warn("Ini hanya peringatan")
```

---

## Panduan Belajar & Migrasi untuk Pelajar

1. **Instal interpreter versi spesifik** (mis. `lua5.1`, `lua5.2`, …)
2. **Baca “Changes” di manual resmi** untuk tiap versi (lihat link manual di lua.org/manual/5.x/readme.html)
3. **Tuliskan ulang contoh kecil** di tiap versi; uji perilaku (contoh varargs, metatable, `goto`, integer dsb.)
4. **Gunakan compatibility switches** di C API jika embed (lihat `luaL_newstate` flags)
5. **Perbandingkan output & error**: catat apa yang berubah (mis. `module` dihapus di 5.2)
6. **Jurnal kode**: buat catatan singkat tiap eksperimen dan hasilnya
7. **Bergabung komunitas** (lua-users mailing list, StackOverflow) untuk kasus nyata
8. **Tantangan mini-project**: porting script 5.1 sederhana ke 5.4, lalu terapkan fitur baru (mis. `const`, to-be-closed)

[1]: https://www.lua.org/manual/5.1/manual.html?utm_source=chatgpt.com "Lua 5.1 Reference Manual"
[2]: https://en.wikipedia.org/wiki/Lua?utm_source=chatgpt.com "Lua"
[3]: https://www.lua.org/versions.html?utm_source=chatgpt.com "version history - Lua"
[4]: https://www.lua.org/manual/5.2/readme.html?utm_source=chatgpt.com "Lua 5.2 readme"
[5]: https://www.lua.org/manual/5.3/readme.html?utm_source=chatgpt.com "Lua 5.3 readme"
[6]: https://www.lua.org/manual/5.4/readme.html "Lua 5.4 readme"

> Selain itu, setiap rilis juga membawa **puluhan–ratusan** perbaikan bug, optimasi kinerja, dan tweak kecil di API C yang tidak tercatat sebagai “fitur utama”. Jadi jika ditotal keseluruhan — termasuk semua minor tweaks — jumlahnya jelas jauh lebih banyak, tetapi secara ringkas ada empat “loncatan” versi dengan **32 fitur baru utama**. Dengan cara iteratif—menjalankan, mengamati, dan mencatat—pelajar akan memahami **evolusi gaya koding** dan **peningkatan mekanisme** di tiap versi Lua. Jika menemui ketidakcocokan, dokumentasikan di catatan pribadi agar proses migrasi proyek nyata lebih mulus. Semangat eksperimen!

[1]: https://www.lua.org/versions.html?utm_source=chatgpt.com "version history - Lua"
[2]: https://www.lua.org/manual/5.2/readme.html?utm_source=chatgpt.com "Lua 5.2 readme"
[3]: https://www.lua.org/manual/5.3/readme.html?utm_source=chatgpt.com "Lua 5.3 readme"
[4]: https://www.lua.org/manual/5.4/readme.html?utm_source=chatgpt.com "Lua 5.4 readme"

#

# 🌙 **MASTER LUA PROGRAMMING**

## _The Ultimate Journey from Scripting to System Architecture_

---

## 📚 **CURRICULUM OVERVIEW**

> **400+ Comprehensive Topics | 30 Advanced Modules | Real-World Applications**

## 🌟 **FOUNDATION LEVEL**

### 🚀 **Modul 1: Lingkungan & Persiapan**

| Kode | Topik                       | Referensi Resmi                                                            |
| ---- | --------------------------- | -------------------------------------------------------------------------- |
| 001  | Selamat Datang di Dunia Lua | [Beranda Lua.org](https://www.lua.org/)                                    |
| 002  | Filosofi Lua                | [Tentang Lua](https://www.lua.org/about.html)                              |
| 003  | Sejarah Lua                 | [Sejarah Lua](https://www.lua.org/history.html)                            |
| 004  | Mengapa Lua Digunakan       | [Penggunaan Lua](https://www.lua.org/uses.html)                            |
| 005  | Instalasi Lua               | [Unduh & Instal](https://www.lua.org/download.html)                        |
| 006  | Lua vs Bahasa Lain          | [Fitur Lua](https://www.lua.org/about.html#features)                       |
| 007  | Editor/IDE                  | (Komunitas) [Dukungan Editor](https://lua-users.org/wiki/LuaEditorSupport) |
| 008  | Penguasaan Command Line     | [Interpreter Standalone](https://www.lua.org/manual/5.4/manual.html#6)     |
| 009  | Penggunaan REPL             | [Mode Interaktif](https://www.lua.org/manual/5.4/manual.html#6)            |
| 010  | Skrip Lua Pertama           | [Memulai](https://www.lua.org/manual/5.4/manual.html#1)                    |
| 011  | Argumen Skrip               | [Variabel `arg`](https://www.lua.org/manual/5.4/manual.html#6.1)           |
| 012  | Konfigurasi Lingkungan      | [Package Path](https://www.lua.org/manual/5.4/manual.html#6.3)             |

---

### 💎 **Modul 2: Sintaks Dasar & Tipe Data**

| Kode | Topik                   | Referensi Resmi                                                                  |
| ---- | ----------------------- | -------------------------------------------------------------------------------- |
| 013  | Dasar Sintaks Lua       | [Konvensi Leksikal](https://www.lua.org/manual/5.4/manual.html#3.1)              |
| 014  | Komentar                | [Sintaks Komentar](https://www.lua.org/manual/5.4/manual.html#3.1)               |
| 015  | Variabel                | [Variabel](https://www.lua.org/manual/5.4/manual.html#3.2)                       |
| 016  | Kata Kunci              | [Kata Kunci](https://www.lua.org/manual/5.4/manual.html#3.1)                     |
| 017  | Sifat Dinamis           | [Nilai dan Tipe](https://www.lua.org/manual/5.4/manual.html#3.3.1)               |
| 018  | Kekuatan Nil            | [Tipe `nil`](https://www.lua.org/manual/5.4/manual.html#3.3.1)                   |
| 019  | Logika Boolean          | [Tipe `boolean`](https://www.lua.org/manual/5.4/manual.html#3.3.2)               |
| 020  | Sistem Angka            | [Angka](https://www.lua.org/manual/5.4/manual.html#3.4.1)                        |
| 021  | Penguasaan String       | [String](https://www.lua.org/manual/5.4/manual.html#3.4.3)                       |
| 022  | Literal String          | [Literal String](https://www.lua.org/manual/5.4/manual.html#3.1)                 |
| 023  | Metode String           | [Library `string`](https://www.lua.org/manual/5.4/manual.html#6.4)               |
| 024  | Sistem Tipe             | [Tipe Data](https://www.lua.org/manual/5.4/manual.html#3.3)                      |
| 025  | Konversi Tipe           | [`tonumber`/`tostring`](https://www.lua.org/manual/5.4/manual.html#pdf-tonumber) |
| 026  | Keunggulan Tipe Dinamis | [Nilai dan Tipe](https://www.lua.org/manual/5.4/manual.html#3.3)                 |
| 027  | Pengecekan Tipe         | [Fungsi `type()`](https://www.lua.org/manual/5.4/manual.html#pdf-type)           |
| 028  | Praktik Terbaik         | [Programming in Lua (Buku)](https://www.lua.org/pil/) (Rujukan sekunder)         |

---

### ⚡ **Modul 3: Operator & Ekspresi**

| Kode | Topik                   | Referensi Resmi                                                            |
| ---- | ----------------------- | -------------------------------------------------------------------------- |
| 029  | Operasi Aritmatika      | [Operator Aritmatika](https://www.lua.org/manual/5.4/manual.html#3.4.1)    |
| 030  | Perbandingan Relasional | [Operator Relasional](https://www.lua.org/manual/5.4/manual.html#3.4.4)    |
| 031  | Operasi Logika          | [Operator Logika](https://www.lua.org/manual/5.4/manual.html#3.4.5)        |
| 032  | Penggabungan String     | [Operator Concatenation](https://www.lua.org/manual/5.4/manual.html#3.4.6) |
| 033  | Operator Panjang        | [Operator Length](https://www.lua.org/manual/5.4/manual.html#3.4.7)        |
| 034  | Aturan Precedence       | [Precedence Operator](https://www.lua.org/manual/5.4/manual.html#3.4.8)    |
| 035  | Variasi Assignment      | [Assignment](https://www.lua.org/manual/5.4/manual.html#3.3.3)             |
| 036  | Multiple Assignment     | [Multiple Assignment](https://www.lua.org/manual/5.4/manual.html#3.3.3)    |
| 037  | Evaluasi Ekspresi       | [Ekspresi](https://www.lua.org/manual/5.4/manual.html#3.4)                 |
| 038  | Pola Logika Kondisional | [Struktur Kontrol](https://www.lua.org/manual/5.4/manual.html#3.3.4)       |

---

<!--
### Sumber Daya Penting:

1. **Manual Lua 5.4**: [https://www.lua.org/manual/5.4/](https://www.lua.org/manual/5.4/)
2. **Library Standar**: [https://www.lua.org/manual/5.4/manual.html#6](https://www.lua.org/manual/5.4/manual.html#6)
3. **Kode Sumber Lua**: [Repositori GitHub](https://github.com/lua/lua)
4. **Situs Resmi**: [https://www.lua.org/](https://www.lua.org/) -->

## 🎯 **INTERMEDIATE LEVEL**

### 🔄 **Modul 4: Alur Kontrol & Perulangan**

| Kode | Topik                                             | Referensi Resmi                                                           |
| ---- | ------------------------------------------------- | ------------------------------------------------------------------------- |
| 039  | Pengambilan Keputusan dengan `if`                 | [Struktur Kontrol `if`](https://www.lua.org/manual/5.4/manual.html#3.3.4) |
| 040  | Konstruksi `if-else`                              | [Blok `else`](https://www.lua.org/manual/5.4/manual.html#3.3.4)           |
| 041  | Rantai `elseif`                                   | [Blok `elseif`](https://www.lua.org/manual/5.4/manual.html#3.3.4)         |
| 042  | Kondisional Bersarang                             | [Struktur Kontrol](https://www.lua.org/manual/5.4/manual.html#3.3.4)      |
| 043  | Penguasaan Loop `while`                           | [Loop `while`](https://www.lua.org/manual/5.4/manual.html#3.3.4)          |
| 044  | Pola `repeat-until`                               | [Loop `repeat`](https://www.lua.org/manual/5.4/manual.html#3.3.4)         |
| 045  | Loop Numerik `for`                                | [Loop `for` Numerik](https://www.lua.org/manual/5.4/manual.html#3.3.5)    |
| 046  | Iterasi Generik `for`                             | [Loop `for` Generik](https://www.lua.org/manual/5.4/manual.html#3.3.5)    |
| 047  | Kontrol Loop                                      | [Pernyataan `break`](https://www.lua.org/manual/5.4/manual.html#3.3.4)    |
| 048  | `break` (Catatan: Lua **tidak punya** `continue`) | [`break`](https://www.lua.org/manual/5.4/manual.html#3.3.4)               |
| 049  | `goto` dan Label                                  | [Pernyataan `goto`](https://www.lua.org/manual/5.4/manual.html#3.3.4)     |
| 050  | Optimasi Alur Kontrol                             | [Praktik Efisiensi](https://www.lua.org/pil/) (Buku PIL Bab 4)            |

---

### 🧩 **Modul 5: Fungsi & Cakupan Variabel**

| Kode | Topik                          | Referensi Resmi                                                              |
| ---- | ------------------------------ | ---------------------------------------------------------------------------- |
| 051  | Seni Mendefinisikan Fungsi     | [Definisi Fungsi](https://www.lua.org/manual/5.4/manual.html#3.4.11)         |
| 052  | Teknik Passing Parameter       | [Parameter Fungsi](https://www.lua.org/manual/5.4/manual.html#3.4.11)        |
| 053  | Strategi Nilai Return          | [Pernyataan `return`](https://www.lua.org/manual/5.4/manual.html#3.3.4)      |
| 054  | Multiple Return Values         | [Return Multi-Nilai](https://www.lua.org/manual/5.4/manual.html#3.4.11)      |
| 055  | Variabel Arguments (`...`)     | [Varargs (`...`)](https://www.lua.org/manual/5.4/manual.html#3.4.11)         |
| 056  | Fungsi Anonim                  | [Fungsi sebagai Ekspresi](https://www.lua.org/manual/5.4/manual.html#3.4.11) |
| 057  | Deklarasi Fungsi Lokal         | [Fungsi Lokal](https://www.lua.org/manual/5.4/manual.html#3.5)               |
| 058  | Fungsi sebagai First-Class     | [First-Class Functions](https://www.lua.org/manual/5.4/manual.html#3.4.11)   |
| 059  | Higher-Order Functions         | [Fungsi sebagai Argumen](https://www.lua.org/manual/5.4/manual.html#3.4.11)  |
| 060  | Mekanisme Closure              | [Closure & Upvalue](https://www.lua.org/manual/5.4/manual.html#3.5)          |
| 061  | Aturan Lexical Scoping         | [Visibility Rules](https://www.lua.org/manual/5.4/manual.html#3.5)           |
| 062  | Manajemen Variabel Global      | [Variabel Global](https://www.lua.org/manual/5.4/manual.html#3.2)            |
| 063  | Praktik Terbaik Variabel Lokal | [Ruang Lingkup Lokal](https://www.lua.org/manual/5.4/manual.html#3.5)        |
| 064  | Masa Hidup Variabel            | [Lifetime Variabel](https://www.lua.org/manual/5.4/manual.html#3.5)          |
| 065  | Mekanisme Upvalue              | [Upvalue](https://www.lua.org/manual/5.4/manual.html#3.5)                    |
| 066  | Optimasi Tail Call             | [Tail Call Optimization](https://www.lua.org/manual/5.4/manual.html#3.4.11)  |
| 067  | Pola Fungsi Rekursif           | [Rekursi & TCO](https://www.lua.org/manual/5.4/manual.html#3.4.11)           |

---

### Catatan Penting:

- **Fitur Khusus**:
  - Lua **tidak memiliki** pernyataan `continue` (poin 048).
  - `goto` (poin 049) tersedia tetapi jarang digunakan.
- **Optimasi**:
  - Tail Call Optimization (poin 066) mencegah stack overflow untuk rekursi dalam.
  - Penggunaan variabel lokal (poin 063) direkomendasikan untuk kinerja.

### Contoh Kode Penting:

```lua
-- Closure & Upvalue (poin 060)
function createCounter()
    local count = 0
    return function()
        count = count + 1
        return count
    end
end

-- Tail Call Optimization (poin 066)
function factorial(n, acc)
    acc = acc or 1
    if n <= 1 then return acc end
    return factorial(n-1, n*acc)  -- Tail call
end
```

## 🚀 **ADVANCED LEVEL**

Berikut pemetaan untuk modul 6-8 dengan referensi resmi Lua 5.4 dalam bahasa Indonesia:

---

### 📊 **Modul 6: Tabel - Kekuatan Super Lua**

| Kode | Topik                    | Referensi Resmi                                                                              |
| ---- | ------------------------ | -------------------------------------------------------------------------------------------- |
| 068  | Dasar Tabel              | [Tipe `table`](https://www.lua.org/manual/5.4/manual.html#3.4.9)                             |
| 069  | Teknik Pembuatan Tabel   | [Konstruktor Tabel](https://www.lua.org/manual/5.4/manual.html#3.4.9)                        |
| 070  | Strategi Indeks          | [Indeks Tabel](https://www.lua.org/manual/5.4/manual.html#3.4.9)                             |
| 071  | Pola Konstruktor         | [Inisialisasi Tabel](https://www.lua.org/manual/5.4/manual.html#3.4.9)                       |
| 072  | Penggunaan Array-Style   | [Array di Lua](https://www.lua.org/pil/11.1.html) (PIL)                                      |
| 073  | Implementasi Hash-Style  | [Tabel sebagai Hash](https://www.lua.org/manual/5.4/manual.html#3.4.9)                       |
| 074  | Aplikasi Tabel Campuran  | [Nilai Heterogen](https://www.lua.org/manual/5.4/manual.html#3.4.9)                          |
| 075  | Metode Traversal         | [`pairs()`](https://www.lua.org/manual/5.4/manual.html#pdf-pairs)                            |
| 076  | `pairs` vs `ipairs`      | [Perulangan Tabel](https://www.lua.org/manual/5.4/manual.html#3.3.5)                         |
| 077  | Fungsi `next`            | [`next()`](https://www.lua.org/manual/5.4/manual.html#pdf-next)                              |
| 078  | Kalkulasi Panjang        | [Operator `#`](https://www.lua.org/manual/5.4/manual.html#3.4.7)                             |
| 079  | Metode Tabel             | [Library `table`](https://www.lua.org/manual/5.4/manual.html#6.6)                            |
| 080  | Operasi Insert/Remove    | [`table.insert`/`table.remove`](https://www.lua.org/manual/5.4/manual.html#pdf-table.insert) |
| 081  | Teknik Concatenation     | [`table.concat`](https://www.lua.org/manual/5.4/manual.html#pdf-table.concat)                |
| 082  | Algoritma Sorting        | [`table.sort`](https://www.lua.org/manual/5.4/manual.html#pdf-table.sort)                    |
| 083  | Struktur Tabel Bersarang | [Tabel Multidimensi](https://www.lua.org/pil/3.2.html) (PIL)                                 |
| 084  | Tabel sebagai Record     | [Struktur Data](https://www.lua.org/pil/2.5.html) (PIL)                                      |
| 085  | Tabel sebagai Array      | [Implementasi Array](https://www.lua.org/manual/5.4/manual.html#3.4.9)                       |
| 086  | Tabel sebagai Set        | [Operasi Set](https://www.lua.org/pil/11.5.html) (PIL)                                       |
| 087  | Teknik Serialisasi       | (Komunitas) [Lua Users Wiki](https://lua-users.org/wiki/TableSerialization)                  |
| 088  | Tabel Lemah (Weak Table) | [Metode `__mode`](https://www.lua.org/manual/5.4/manual.html#2.5.4)                          |
| 089  | Integrasi Metamethod     | [Metatables](https://www.lua.org/manual/5.4/manual.html#2.4)                                 |
| 090  | Optimasi Performa        | [Panduan Performa](https://www.lua.org/gems/sample.pdf) (Lua Gems)                           |

---

### 🔤 **Modul 7: Pemrosesan String & Pola**

| Kode | Topik                    | Referensi Resmi                                                                       |
| ---- | ------------------------ | ------------------------------------------------------------------------------------- |
| 091  | Penguasan Library String | [Library `string`](https://www.lua.org/manual/5.4/manual.html#6.4)                    |
| 092  | Operasi Pencarian        | [`string.find`](https://www.lua.org/manual/5.4/manual.html#pdf-string.find)           |
| 093  | Pattern Matching         | [Pola Lua](https://www.lua.org/manual/5.4/manual.html#6.4.1)                          |
| 094  | Iterasi Global Match     | [`string.gmatch`](https://www.lua.org/manual/5.4/manual.html#pdf-string.gmatch)       |
| 095  | Teknik Substitusi        | [`string.gsub`](https://www.lua.org/manual/5.4/manual.html#pdf-string.gsub)           |
| 096  | Operasi Substring        | [`string.sub`](https://www.lua.org/manual/5.4/manual.html#pdf-string.sub)             |
| 097  | Transformasi Case        | [`string.lower`/`upper`](https://www.lua.org/manual/5.4/manual.html#pdf-string.lower) |
| 098  | Pengulangan String       | [`string.rep`](https://www.lua.org/manual/5.4/manual.html#pdf-string.rep)             |
| 099  | Operasi Reverse          | [`string.reverse`](https://www.lua.org/manual/5.4/manual.html#pdf-string.reverse)     |
| 100  | Byte & Karakter          | [`string.byte`/`char`](https://www.lua.org/manual/5.4/manual.html#pdf-string.byte)    |
| 101  | Formatting String        | [`string.format`](https://www.lua.org/manual/5.4/manual.html#pdf-string.format)       |
| 102  | Sintaks Pola             | [Pattern Syntax](https://www.lua.org/manual/5.4/manual.html#6.4.1)                    |
| 103  | Kelas Karakter           | [Character Classes](https://www.lua.org/manual/5.4/manual.html#6.4.1)                 |
| 104  | Modifier Pola            | [Pattern Modifiers](https://www.lua.org/manual/5.4/manual.html#6.4.1)                 |
| 105  | Capture Groups           | [Captures](https://www.lua.org/manual/5.4/manual.html#6.4.1)                          |
| 106  | Pola Seimbang            | [Balanced Patterns](https://www.lua.org/manual/5.4/manual.html#6.4.1)                 |
| 107  | Contoh Pola Lanjut       | [Contoh Kompleks](https://lua-users.org/wiki/PatternsTutorial) (Wiki)                 |
| 108  | Dukungan UTF-8           | [Library `utf8`](https://www.lua.org/manual/5.4/manual.html#6.5)                      |
| 109  | Penanganan Unicode       | [UTF-8 Support](https://www.lua.org/manual/5.4/manual.html#6.5)                       |

---

### 🏗️ **Modul 8: Pemrograman Berorientasi Objek**

| Kode | Topik                     | Referensi Resmi                                                         |
| ---- | ------------------------- | ----------------------------------------------------------------------- |
| 110  | Filosofi OOP di Lua       | [OOP dengan Tabel](https://www.lua.org/pil/16.html) (PIL)               |
| 111  | Tabel sebagai Objek       | [Metode Objek](https://www.lua.org/pil/16.1.html) (PIL)                 |
| 112  | Pola Definisi Metode      | [Fungsi dalam Tabel](https://www.lua.org/manual/5.4/manual.html#3.4.11) |
| 113  | Parameter `self`          | [Metode dengan `self`](https://www.lua.org/pil/16.1.html) (PIL)         |
| 114  | Fungsi Konstruktor        | [Factory Functions](https://www.lua.org/pil/16.1.html) (PIL)            |
| 115  | Implementasi Kelas        | [Class Emulation](https://www.lua.org/pil/16.2.html) (PIL)              |
| 116  | Strategi Inheritance      | [Metamethod `__index`](https://www.lua.org/manual/5.4/manual.html#2.4)  |
| 117  | Method Overriding         | [Override Metode](https://www.lua.org/pil/16.3.html) (PIL)              |
| 118  | Teknik Enkapsulasi        | [Closures untuk Privasi](https://www.lua.org/pil/16.4.html) (PIL)       |
| 119  | Implementasi Polimorfisme | [Dynamic Dispatch](https://www.lua.org/pil/16.5.html) (PIL)             |
| 120  | Desain Library Kelas      | [Modul OOP](https://www.lua.org/pil/16.4.html) (PIL)                    |
| 121  | Multiple Inheritance      | [Inheritance Multi](https://www.lua.org/pil/16.3.html) (PIL)            |
| 122  | Pola Mixin                | [Mixins](https://lua-users.org/wiki/MixinsTutorial) (Wiki)              |
| 123  | Pola Factory              | [Factory Pattern](https://www.lua.org/pil/16.1.html) (PIL)              |
| 124  | Pola Prototype            | [Prototype-Based OOP](https://www.lua.org/pil/16.4.html) (PIL)          |
| 125  | Aplikasi Design Pattern   | [Pola Umum](https://lua-users.org/wiki/DesignPatterns) (Wiki)           |

---

### **Catatan Khusus**:

<!-- . **Sumber Utama**:
   - **Manual Lua 5.4**: [https://www.lua.org/manual/5.4/](https://www.lua.org/manual/5.4/)
   - **Programming in Lua (PIL)**: Buku resmi oleh pencipta Lua (referensi sekunder)
   - **Wiki Pengguna Lua**: [https://lua-users.org/wiki/](https://lua-users.org/wiki/) -->

- **Penting untuk OOP**:

  - Lua tidak memiliki OOP bawaan. Semua implementasi menggunakan **tabel + metatables**.
  - Contoh sederhana:
    ```lua
    -- Class implementation (kode 115)
    local MyClass = {}
    function MyClass:new(o)
        o = o or {}
        setmetatable(o, self)
        self.__index = self
        return o
    end
    ```

- **Optimasi Tabel**:

  - Gunakan `local` untuk akses cepat ke fungsi tabel (misal: `local concat = table.concat`).
  - Hindari tabel lemah (`weak table`) kecuali untuk manajemen memori spesifik.

- **String & Unicode**:
  - Lua 5.4 memiliki library `utf8` untuk operasi Unicode dasar.
  - Untuk operasi Unicode kompleks, gunakan library eksternal seperti [luautf8](https://github.com/starwing/luautf8).

---

## 🔮 **EXPERT LEVEL**

### ⚗️ **Modul 9: Metatables & Metamethods**

| Kode | Topik                           | Referensi Resmi                                                                 |
| ---- | ------------------------------- | ------------------------------------------------------------------------------- |
| 126  | Pengenalan Metatable            | [Metatables & Metamethods](https://www.lua.org/manual/5.4/manual.html#2.4)      |
| 127  | Fungsi `setmetatable`           | [`setmetatable()`](https://www.lua.org/manual/5.4/manual.html#pdf-setmetatable) |
| 128  | Fungsi `getmetatable`           | [`getmetatable()`](https://www.lua.org/manual/5.4/manual.html#pdf-getmetatable) |
| 129  | Metamethod `__index`            | [\_\_index](https://www.lua.org/manual/5.4/manual.html#2.4)                     |
| 130  | Metamethod `__newindex`         | [\_\_newindex](https://www.lua.org/manual/5.4/manual.html#2.4)                  |
| 131  | Metamethod `__call`             | [\_\_call](https://www.lua.org/manual/5.4/manual.html#2.4)                      |
| 132  | Metamethod `__tostring`         | [\_\_tostring](https://www.lua.org/manual/5.4/manual.html#2.4)                  |
| 133  | Metamethods Aritmatika          | [Arithmetic Metamethods](https://www.lua.org/manual/5.4/manual.html#2.4)        |
| 134  | Implementasi `__add`            | [\_\_add](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 135  | Operasi `__sub`                 | [\_\_sub](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 136  | Kalkulasi `__mul`               | [\_\_mul](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 137  | Matematika `__div`              | [\_\_div](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 138  | Modulus `__mod`                 | [\_\_mod](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 139  | Eksponensial `__pow`            | [\_\_pow](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 140  | Unary Minus `__unm`             | [\_\_unm](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 141  | Concatenation `__concat`        | [\_\_concat](https://www.lua.org/manual/5.4/manual.html#2.4)                    |
| 142  | Panjang `__len`                 | [\_\_len](https://www.lua.org/manual/5.4/manual.html#2.4)                       |
| 143  | Metamethods Perbandingan        | [Comparison Metamethods](https://www.lua.org/manual/5.4/manual.html#2.4)        |
| 144  | Kesetaraan `__eq`               | [\_\_eq](https://www.lua.org/manual/5.4/manual.html#2.4)                        |
| 145  | Kurang Dari `__lt`              | [\_\_lt](https://www.lua.org/manual/5.4/manual.html#2.4)                        |
| 146  | Kurang Sama `__le`              | [\_\_le](https://www.lua.org/manual/5.4/manual.html#2.4)                        |
| 147  | Garbage Collection `__gc`       | [\_\_gc](https://www.lua.org/manual/5.4/manual.html#2.5.3)                      |
| 148  | Referensi Lemah `__mode`        | [\_\_mode](https://www.lua.org/manual/5.4/manual.html#2.5.4)                    |
| 149  | Fungsi Raw (`rawget`, `rawset`) | [Raw Access](https://www.lua.org/manual/5.4/manual.html#pdf-rawget)             |
| 150  | Aplikasi Metatable              | [Studi Kasus](https://www.lua.org/pil/13.html) (PIL Bab 13)                     |

---

### 🔄 **Modul 10: Coroutines & Concurrency**

| Kode | Topik                      | Referensi Resmi                                                                         |
| ---- | -------------------------- | --------------------------------------------------------------------------------------- |
| 151  | Filosofi Coroutine         | [Coroutines](https://www.lua.org/manual/5.4/manual.html#2.6)                            |
| 152  | Pembuatan Coroutine        | [`coroutine.create()`](https://www.lua.org/manual/5.4/manual.html#pdf-coroutine.create) |
| 153  | Operasi `resume`           | [`coroutine.resume()`](https://www.lua.org/manual/5.4/manual.html#pdf-coroutine.resume) |
| 154  | Mekanisme `yield`          | [`coroutine.yield()`](https://www.lua.org/manual/5.4/manual.html#pdf-coroutine.yield)   |
| 155  | Pengecekan Status          | [`coroutine.status()`](https://www.lua.org/manual/5.4/manual.html#pdf-coroutine.status) |
| 156  | Fungsi `wrap`              | [`coroutine.wrap()`](https://www.lua.org/manual/5.4/manual.html#pdf-coroutine.wrap)     |
| 157  | Komunikasi Antar-Coroutine | [Pertukaran Data](https://www.lua.org/pil/9.4.html) (PIL)                               |
| 158  | Pola Producer-Consumer     | [Contoh PIL](https://www.lua.org/pil/9.2.html)                                          |
| 159  | Multitasking Kooperatif    | [Concurrency](https://www.lua.org/pil/9.html) (PIL Bab 9)                               |
| 160  | Contoh Coroutine           | [Manual §2.6](https://www.lua.org/manual/5.4/manual.html#2.6)                           |
| 161  | Pemrograman Asinkron       | (Komunitas) [Lua Async](https://github.com/cloudwu/lua-async)                           |
| 162  | Implementasi Event Loop    | [Coroutine Scheduler](https://lua-users.org/wiki/CoroutinesTutorial) (Wiki)             |
| 163  | Pengembangan Scheduler     | (Eksternal) [Lua Patterns](https://www.lua.org/pil/9.4.html)                            |
| 164  | Debugging Coroutine        | [Debug Coroutine](https://www.lua.org/manual/5.4/manual.html#4.7)                       |

---

### 📦 **Modul 11: Modul & Paket**

| Kode | Topik                    | Referensi Resmi                                                                     |
| ---- | ------------------------ | ----------------------------------------------------------------------------------- |
| 165  | Sistem Modul             | [Package System](https://www.lua.org/manual/5.4/manual.html#6.3)                    |
| 166  | Fungsi `require`         | [`require()`](https://www.lua.org/manual/5.4/manual.html#pdf-require)               |
| 167  | Konfigurasi Package Path | [`package.path`](https://www.lua.org/manual/5.4/manual.html#pdf-package.path)       |
| 168  | Manajemen C Path         | [`package.cpath`](https://www.lua.org/manual/5.4/manual.html#pdf-package.cpath)     |
| 169  | Cache Paket              | [`package.loaded`](https://www.lua.org/manual/5.4/manual.html#pdf-package.loaded)   |
| 170  | Mekanisme Preload        | [`package.preload`](https://www.lua.org/manual/5.4/manual.html#pdf-package.preload) |
| 171  | Praktik Terbaik Modul    | [Modul Standar](https://www.lua.org/pil/15.1.html) (PIL)                            |
| 172  | Strategi Ekspor          | [Return Table](https://www.lua.org/pil/15.1.html)                                   |
| 173  | Pengembangan Modul Lokal | [Local Modules](https://www.lua.org/pil/15.2.html)                                  |
| 174  | Pola Modul Global        | [Global Modules](https://www.lua.org/pil/15.2.html)                                 |
| 175  | Sistem Caching Modul     | [Cache `package.loaded`](https://www.lua.org/manual/5.4/manual.html#6.3)            |
| 176  | Teknik Hot Reloading     | (Komunitas) [Hot Reload](https://github.com/rxi/lume#hotreload)                     |
| 177  | Pengenalan LuaRocks      | [Situs LuaRocks](https://luarocks.org/)                                             |
| 178  | Instalasi Paket          | [LuaRocks CLI](https://github.com/luarocks/luarocks/wiki/Download)                  |
| 179  | Pembuatan Rockspec       | [Rockspec Format](https://github.com/luarocks/luarocks/wiki/Rockspec-format)        |
| 180  | Publikasi Paket          | [Panduan Publikasi](https://github.com/luarocks/luarocks/wiki/Creating-a-rock)      |
| 181  | Manajemen Dependensi     | [LuaRocks Dependencies](https://github.com/luarocks/luarocks/wiki/Dependencies)     |

---

### **Catatan Penting**:

1. **Metatables**

   - Metamethod `__gc` (kode 147) hanya bekerja pada **tabel/userdata**.
   - Contoh implementasi:
     ```lua
     local obj = setmetatable({}, { __gc = function() print("Destroyed!") end })
     obj = nil  -- Dipanggil saat GC berjalan
     ```

- **Coroutines**

  - Lua menggunakan **concurrency kooperatif** (non-preemptive).
  - Contoh coroutine:
    ```lua
    local co = coroutine.create(function()
        print("Hello")
        coroutine.yield()
        print("World")
    end)
    coroutine.resume(co)  -- Output: Hello
    coroutine.resume(co)  -- Output: World
    ```

- **Modul & LuaRocks**

  - `require` (kode 166) mencari file di `package.path` (default: `./?.lua`).
  - LuaRocks adalah **package manager pihak ketiga**, bukan bagian resmi Lua.

<!--
- **Sumber Eksternal**:
  - **Programming in Lua (PIL)**: [https://www.lua.org/pil/](https://www.lua.org/pil/)
  - **Wiki Pengguna Lua**: [https://lua-users.org/wiki/](https://lua-users.org/wiki/)
  - **LuaRocks**: [https://luarocks.org/](https://luarocks.org/) -->

## 🌐 **SPECIALIZED DOMAINS**

### 💾 **Modul 12: File I/O & Pemrograman Sistem**

| Kode | Topik                       | Referensi Resmi                                                                         |
| ---- | --------------------------- | --------------------------------------------------------------------------------------- |
| 182  | Ikhtisar Operasi File       | [Library `io`](https://www.lua.org/manual/5.4/manual.html#6.8)                          |
| 183  | Fungsi Library I/O          | [`io.read()`/`io.write()`](https://www.lua.org/manual/5.4/manual.html#pdf-io.read)      |
| 184  | Mode Pembukaan File         | [`io.open()`](https://www.lua.org/manual/5.4/manual.html#pdf-io.open)                   |
| 185  | Strategi Pembacaan          | [Mode Baca](https://www.lua.org/manual/5.4/manual.html#6.8)                             |
| 186  | Teknik Penulisan            | [Mode Tulis](https://www.lua.org/manual/5.4/manual.html#6.8)                            |
| 187  | Pola Iterasi File           | [`file:lines()`](https://www.lua.org/manual/5.4/manual.html#pdf-file:lines)             |
| 188  | Penanganan File Biner       | [Mode `"b"`](https://www.lua.org/manual/5.4/manual.html#pdf-io.open)                    |
| 189  | Manajemen File Sementara    | (Eksternal) [LuaFileSystem](https://lunarmodules.github.io/luafilesystem/)              |
| 190  | Operasi File System         | [`os.rename()`/`os.remove()`](https://www.lua.org/manual/5.4/manual.html#pdf-os.rename) |
| 191  | Navigasi Direktori          | (Eksternal) [LuaFileSystem](https://lunarmodules.github.io/luafilesystem/)              |
| 192  | Fungsi Library OS           | [Library `os`](https://www.lua.org/manual/5.4/manual.html#6.9)                          |
| 193  | Variabel Lingkungan         | [`os.getenv()`](https://www.lua.org/manual/5.4/manual.html#pdf-os.getenv)               |
| 194  | Eksekusi Perintah Sistem    | [`os.execute()`](https://www.lua.org/manual/5.4/manual.html#pdf-os.execute)             |
| 195  | Penanganan Tanggal & Waktu  | [`os.date()`](https://www.lua.org/manual/5.4/manual.html#pdf-os.date)                   |
| 196  | Manajemen Proses            | (Eksternal) [LuaProc](https://github.com/LuaDist/luaproc)                               |
| 197  | Pertimbangan Cross-Platform | [Catatan OS](https://www.lua.org/manual/5.4/manual.html#6.9)                            |

---

### 🛡️ **Modul 13: Penanganan Error & Debugging**

| Kode | Topik                      | Referensi Resmi                                                                       |
| ---- | -------------------------- | ------------------------------------------------------------------------------------- |
| 198  | Filosofi Penanganan Error  | [Error Handling](https://www.lua.org/manual/5.4/manual.html#2.3)                      |
| 199  | Penggunaan Fungsi Error    | [`error()`](https://www.lua.org/manual/5.4/manual.html#pdf-error)                     |
| 200  | Aplikasi Fungsi `assert`   | [`assert()`](https://www.lua.org/manual/5.4/manual.html#pdf-assert)                   |
| 201  | Protected Call (`pcall`)   | [`pcall()`](https://www.lua.org/manual/5.4/manual.html#pdf-pcall)                     |
| 202  | Extended `xpcall`          | [`xpcall()`](https://www.lua.org/manual/5.4/manual.html#pdf-xpcall)                   |
| 203  | Desain Objek Error         | [Custom Error](https://www.lua.org/pil/8.3.html) (PIL)                                |
| 204  | Analisis Stack Trace       | [`debug.traceback()`](https://www.lua.org/manual/5.4/manual.html#pdf-debug.traceback) |
| 205  | Fitur Debug Library        | [Library `debug`](https://www.lua.org/manual/5.4/manual.html#6.10)                    |
| 206  | Implementasi Debug Hook    | [`debug.sethook()`](https://www.lua.org/manual/5.4/manual.html#pdf-debug.sethook)     |
| 207  | Teknik Profiling           | (Eksternal) [LuaProfiler](https://github.com/luaforge/luaprofiler)                    |
| 208  | Analisis Penggunaan Memori | [`collectgarbage()`](https://www.lua.org/manual/5.4/manual.html#pdf-collectgarbage)   |
| 209  | Debugging Performa         | [Panduan Performa](https://www.lua.org/gems/sample.pdf)                               |
| 210  | Alat Debugging             | (Eksternal) [ZeroBrane Studio](https://studio.zerobrane.com/)                         |
| 211  | Pola Penanganan Error      | [PIL Bab 8](https://www.lua.org/pil/8.html)                                           |
| 212  | Praktik Terbaik            | [Error Handling Tips](https://www.lua.org/pil/8.3.html) (PIL)                         |

---

### 🔧 **Modul 14: C API & Ekstensi**

| Kode | Topik                  | Referensi Resmi                                                                               |
| ---- | ---------------------- | --------------------------------------------------------------------------------------------- |
| 213  | Pengantar C API        | [C API Manual](https://www.lua.org/manual/5.4/manual.html#4)                                  |
| 214  | Manajemen State Lua    | [`lua_State`](https://www.lua.org/manual/5.4/manual.html#4.1.1)                               |
| 215  | Operasi Stack          | [Stack Manipulation](https://www.lua.org/manual/5.4/manual.html#4.2)                          |
| 216  | Memanggil Lua dari C   | [`lua_call()`](https://www.lua.org/manual/5.4/manual.html#lua_call)                           |
| 217  | Memanggil C dari Lua   | [C Functions](https://www.lua.org/manual/5.4/manual.html#4.4)                                 |
| 218  | Implementasi User Data | [`lua_newuserdatauv()`](https://www.lua.org/manual/5.4/manual.html#lua_newuserdatauv)         |
| 219  | Light User Data        | [`lua_pushlightuserdata()`](https://www.lua.org/manual/5.4/manual.html#lua_pushlightuserdata) |
| 220  | Integrasi Fungsi C     | [Registrasi Fungsi](https://www.lua.org/manual/5.4/manual.html#4.4)                           |
| 221  | Pembuatan C Closure    | [`lua_pushcclosure()`](https://www.lua.org/manual/5.4/manual.html#lua_pushcclosure)           |
| 222  | Penggunaan Registry    | [Registry](https://www.lua.org/manual/5.4/manual.html#4.5)                                    |
| 223  | Manajemen Referensi    | [`luaL_ref()`](https://www.lua.org/manual/5.4/manual.html#luaL_ref)                           |
| 224  | Manajemen Memori       | [Memory Allocation](https://www.lua.org/manual/5.4/manual.html#4.6)                           |
| 225  | Penanganan Error di C  | [`lua_pcall()`](https://www.lua.org/manual/5.4/manual.html#lua_pcall)                         |
| 226  | Pembuatan Ekstensi     | [Writing C Modules](https://www.lua.org/pil/26.html) (PIL)                                    |
| 227  | Pengantar FFI          | (LuaJIT) [FFI Tutorial](https://luajit.org/ext_ffi.html)                                      |
| 228  | Penggunaan FFI LuaJIT  | [LuaJIT FFI](https://luajit.org/ext_ffi_api.html)                                             |
| 229  | Dynamic Loading        | [`package.loadlib()`](https://www.lua.org/manual/5.4/manual.html#pdf-package.loadlib)         |

---

### 🧮 **Modul 15: Matematika & Utilitas**

| Kode | Topik                      | Referensi Resmi                                                                          |
| ---- | -------------------------- | ---------------------------------------------------------------------------------------- |
| 230  | Fungsi Math Library        | [Library `math`](https://www.lua.org/manual/5.4/manual.html#6.7)                         |
| 231  | Operasi Matematika         | [`math.floor()`/`math.max()`](https://www.lua.org/manual/5.4/manual.html#pdf-math.floor) |
| 232  | Generasi Angka Acak        | [`math.random()`](https://www.lua.org/manual/5.4/manual.html#pdf-math.random)            |
| 233  | Operasi Bit (Lua 5.3+)     | [Library `bit32`](https://www.lua.org/manual/5.4/manual.html#6.7)                        |
| 234  | Penggunaan Library UTF-8   | [Library `utf8`](https://www.lua.org/manual/5.4/manual.html#6.5)                         |
| 235  | Kontrol Garbage Collection | [`collectgarbage()`](https://www.lua.org/manual/5.4/manual.html#pdf-collectgarbage)      |
| 236  | Manajemen Memori           | [Garbage Collection](https://www.lua.org/manual/5.4/manual.html#2.5)                     |
| 237  | Optimasi Performa          | [Tips Performa](https://www.lua.org/gems/sample.pdf)                                     |
| 238  | Teknik Benchmarking        | (Komunitas) [Lua Benchmarks](https://github.com/lua-benchmarks)                          |
| 239  | Alat Profiling             | (Eksternal) [LuaProf](https://github.com/keplerproject/luaprofiler)                      |
| 240  | Analisis Kode              | [LuaCheck](https://github.com/lunarmodules/luacheck)                                     |

---

### **Catatan Penting**:

1. **File I/O**:

   - Operasi direktori (kode 191) membutuhkan library eksternal seperti **LuaFileSystem**.
   - Contoh penulisan file:
     ```lua
     local file = io.open("test.txt", "w")
     file:write("Hello World")
     file:close()
     ```

2. **C API**:

   - FFI (kode 227-228) hanya tersedia di **LuaJIT** (bukan Lua resmi).
   - Contoh integrasi fungsi C:
     ```c
     static int lua_add(lua_State *L) {
         int a = lua_tonumber(L, 1);
         int b = lua_tonumber(L, 2);
         lua_pushnumber(L, a + b);
         return 1;
     }
     ```

3. **Math & Utilitas**:

   - Fungsi `bit32` (kode 233) hanya ada di **Lua 5.3+**.
   - Untuk operasi matematika kompleks, pertimbangkan library eksternal seperti [SciLua](http://scilua.org/).

4. **Sumber Eksternal**:
   - **LuaJIT**: [https://luajit.org/](https://luajit.org/)
   - **LuaFileSystem**: [https://lunarmodules.github.io/luafilesystem/](https://lunarmodules.github.io/luafilesystem/)
   - **Programming in Lua (PIL)**: [https://www.lua.org/pil/](https://www.lua.org/pil/)

## 🚀 **PROFESSIONAL APPLICATIONS**

Berikut pemetaan untuk modul 16-20 dengan referensi resmi dan sumber terkait dalam bahasa Indonesia:

---

### 🌐 **Modul 16: Pengembangan Web**

| Kode | Topik                     | Referensi Resmi                                                                                   |
| ---- | ------------------------- | ------------------------------------------------------------------------------------------------- |
| 241  | Ikhtisar Pengembangan Web | [Lua Web Development](https://www.lua.org/uses.html#web)                                          |
| 242  | Platform OpenResty        | [OpenResty Official](https://openresty.org/)                                                      |
| 243  | Modul Nginx Lua           | [ngx_lua Module](https://github.com/openresty/lua-nginx-module)                                   |
| 244  | Framework Lapis           | [Lapis Framework](https://leafo.net/lapis/)                                                       |
| 245  | Penanganan HTTP Request   | [OpenResty Request Handling](https://openresty-reference.readthedocs.io/en/latest/Lua_Nginx_API/) |
| 246  | Pengembangan Web API      | [Lapis API Guide](https://leafo.net/lapis/reference/actions.html)                                 |
| 247  | Pemrosesan JSON           | [Lua cJSON Library](https://github.com/openresty/lua-cjson)                                       |
| 248  | Integrasi Database        | [LuaSQL](https://github.com/keplerproject/luasql)                                                 |
| 249  | Manajemen Sesi            | (Komunitas) [Lua Resty Session](https://github.com/bungle/lua-resty-session)                      |
| 250  | Sistem Autentikasi        | [Lua Auth Libraries](https://lua-users.org/wiki/SecurityLibraries)                                |
| 251  | Implementasi WebSocket    | [lua-websockets](https://github.com/lipp/lua-websockets)                                          |
| 252  | Template Engine           | [lua-template](https://github.com/dannote/lua-template)                                           |
| 253  | Layanan RESTful           | [OpenResty REST](https://openresty.org/en/restful-api-design.html)                                |
| 254  | Arsitektur Microservices  | [Lua Microservices](https://github.com/tarantool/tarantool)                                       |
| 255  | Optimasi Performa         | [OpenResty Performance](https://openresty.org/en/performance.html)                                |

---

### 🎮 **Modul 17: Pengembangan Game**

| Kode | Topik                  | Referensi Resmi                                                           |
| ---- | ---------------------- | ------------------------------------------------------------------------- |
| 256  | Pengantar Game Dev     | [LÖVE2D Wiki](https://love2d.org/wiki/Main_Page)                          |
| 257  | Framework LÖVE2D       | [LÖVE2D Official](https://love2d.org/)                                    |
| 258  | Implementasi Game Loop | [LÖVE Callbacks](https://love2d.org/wiki/love.run)                        |
| 259  | Grafik & Rendering     | [LÖVE Graphics](https://love2d.org/wiki/love.graphics)                    |
| 260  | Manajemen Sprite       | [LÖVE Image](https://love2d.org/wiki/love.graphics.newImage)              |
| 261  | Sistem Input           | [LÖVE Keyboard/Mouse](https://love2d.org/wiki/love.keyboard)              |
| 262  | Integrasi Audio        | [LÖVE Sound](https://love2d.org/wiki/love.audio)                          |
| 263  | Simulasi Fisika        | [LÖVE Physics](https://love2d.org/wiki/love.physics)                      |
| 264  | Deteksi Tabrakan       | [Box2D Collision](https://love2d.org/wiki/BoundingBox.lua)                |
| 265  | Manajemen Game State   | [LÖVE State Management](https://github.com/nvim-lua/lua-guide#game-state) |
| 266  | Sistem Animasi         | [LÖVE Anim8](https://github.com/kikito/anim8)                             |
| 267  | Manajemen Scene        | [LÖVE Scene Manager](https://github.com/rm-code/love-state-switch)        |
| 268  | Pemuatan Aset          | [LÖVE File Loading](https://love2d.org/wiki/love.filesystem)              |
| 269  | Optimasi Performa      | [LÖVE Performance Tips](https://love2d.org/wiki/Performance_Tips)         |
| 270  | Distribusi Game        | [LÖVE Packaging](https://love2d.org/wiki/Game_Distribution)               |

---

### 🔌 **Modul 18: Sistem Embedded**

| Kode | Topik                   | Referensi Resmi                                                                             |
| ---- | ----------------------- | ------------------------------------------------------------------------------------------- |
| 271  | Lua di Embedded Systems | [eLua Project](https://www.eluaproject.net/)                                                |
| 272  | Platform NodeMCU        | [NodeMCU Docs](https://nodemcu.readthedocs.io/)                                             |
| 273  | Pemrograman ESP32       | [Lua RTOS](https://www.luatos.com/)                                                         |
| 274  | Aplikasi IoT            | [NodeMCU IoT Examples](https://github.com/nodemcu/nodemcu-firmware)                         |
| 275  | Integrasi Sensor        | [NodeMCU GPIO](https://nodemcu.readthedocs.io/en/dev/modules/gpio/)                         |
| 276  | Komunikasi Jaringan     | [NodeMCU WiFi](https://nodemcu.readthedocs.io/en/dev/modules/wifi/)                         |
| 277  | Protokol MQTT           | [lua-mqtt](https://github.com/xHasKx/lua-mqtt)                                              |
| 278  | Sistem Real-time        | [Lua RTOS Docs](https://www.luatos.com/documentation)                                       |
| 279  | Batasan Memori          | [eLua Memory](https://www.eluaproject.net/doc/v0.9/en_arch_romfs.html)                      |
| 280  | Manajemen Daya          | [NodeMCU Power Management](https://nodemcu.readthedocs.io/en/dev/modules/node/#nodeprepare) |
| 281  | Pengembangan Firmware   | [NodeMCU Custom Build](https://nodemcu.readthedocs.io/en/dev/build/)                        |
| 282  | OTA Updates             | [NodeMCU OTA](https://nodemcu.readthedocs.io/en/dev/flash/#ota)                             |
| 283  | Device Drivers          | [Lua RTOS Drivers](https://github.com/whitecatboard/Lua-RTOS-ESP32)                         |
| 284  | Implementasi Protokol   | [Lua Sockets](https://github.com/diegonehab/luasocket)                                      |

---

### 📊 **Modul 19: Data Science & Analisis**

| Kode | Topik                       | Referensi Resmi                                                        |
| ---- | --------------------------- | ---------------------------------------------------------------------- |
| 285  | Pengantar Pemrosesan Data   | [Torch Lua](http://torch.ch/)                                          |
| 286  | Penggunaan Torch            | [Torch Tutorial](https://github.com/torch/torch7/wiki/Cheatsheet)      |
| 287  | Komputasi Ilmiah            | [SciLua](http://scilua.org/)                                           |
| 288  | Analisis Statistik          | [LuaStats](https://github.com/clementfarabet/lua---stats)              |
| 289  | Visualisasi Data            | [Lua-Gnuplot](https://github.com/kristian/lua-gnuplot)                 |
| 290  | Dasar Machine Learning      | [Torch NN](https://github.com/torch/nn)                                |
| 291  | Implementasi Neural Network | [Torch Tutorial NN](https://github.com/torch/nn/blob/master/README.md) |
| 292  | Pemrosesan CSV              | [lua-csv](https://github.com/geoffleyland/lua-csv)                     |
| 293  | Konektivitas Database       | [LuaDBI](https://github.com/mwild1/luadbi)                             |
| 294  | Generasi Chart              | [LuaChart](https://github.com/EvandroLG/LuaChart)                      |
| 295  | Teknik Data Mining          | [Lua Patterns](https://www.lua.org/pil/20.2.html)                      |
| 296  | Implementasi Algoritma      | [Lua Algos](https://github.com/evandrolg/LuaAlgorithms)                |

---

### 🤖 **Modul 20: Scripting & Otomasi**

| Kode | Topik                 | Referensi Resmi                                                             |
| ---- | --------------------- | --------------------------------------------------------------------------- |
| 297  | System Scripting      | [Lua OS Lib](https://www.lua.org/manual/5.4/manual.html#6.9)                |
| 298  | Build Otomasi         | [LuaRocks Build](https://github.com/luarocks/luarocks/wiki/Rockspec-format) |
| 299  | Pemrosesan Log        | [Lua File I/O](https://www.lua.org/manual/5.4/manual.html#6.8)              |
| 300  | Manajemen Konfigurasi | [Lua Config Parsing](https://lua-users.org/wiki/ConfigurationFiles)         |
| 301  | Penjadwalan Tugas     | [lua-cron](https://github.com/kikito/lua-cron)                              |
| 302  | Manipulasi File       | [Lua Filesystem](https://github.com/keplerproject/luafilesystem)            |
| 303  | Pemrosesan Teks       | [Lua String Lib](https://www.lua.org/manual/5.4/manual.html#6.4)            |
| 304  | Integrasi Shell       | [LuaSystem](https://github.com/o-lim/luasystem)                             |
| 305  | Script Cross-platform | [Lua Cross-Platform Tips](https://lua-users.org/wiki/PortabilityNotes)      |
| 306  | Otomasi Deployment    | [Lua Deploy Tools](https://github.com/mason-larobina/luadeploy)             |
| 307  | Integrasi CI/CD       | [GitLab CI Lua](https://docs.gitlab.com/ee/ci/examples/lua.html)            |
| 308  | Monitoring Scripts    | [Prometheus Lua](https://github.com/knyar/nginx-lua-prometheus)             |

---

### **Catatan Penting**:

1. **Web Development**

   - OpenResty (kode 242) adalah platform berbasis **Nginx + Lua**, bukan bagian resmi Lua.
   - Contoh HTTP request di OpenResty:
     ```lua
     location /hello {
         content_by_lua_block {
             ngx.say("Hello World")
         }
     }
     ```

2. **Game Development**

   - LÖVE2D (kode 257) adalah framework **eksternal** untuk game 2D.
   - Contoh game loop sederhana:
     ```lua
     function love.update(dt)
         -- Update game state
     end
     ```

3. **Embedded Systems**

   - NodeMCU (kode 272) menggunakan **Lua 5.1** dengan optimasi untuk IoT.
   - Contoh baca sensor:
     ```lua
     pin = 4
     gpio.mode(pin, gpio.INPUT)
     print(gpio.read(pin))
     ```

4. **Data Science**

   - Torch (kode 286) adalah library machine learning yang sekarang dikembangkan sebagai **PyTorch** (tidak aktif di Lua).

<!-- 5. **Sumber Eksternal**:
   - **LÖVE2D Wiki**: https://love2d.org/wiki
   - **NodeMCU Docs**: https://nodemcu.readthedocs.io
   - **OpenResty**: https://openresty.org -->

## 🔧 **ADVANCED INTEGRATION**

### 🗄️ **Module 21: Database Integration**

```
309. Database Connectivity
310. SQLite Integration
311. MySQL Connections
312. PostgreSQL Support
313. MongoDB Driver
314. Redis Integration
315. ORM Libraries
316. Migration Scripts
317. Connection Pooling
318. Transaction Management
319. Query Optimization
320. Database Security
```

### 🌐 **Module 22: Network Programming**

```
321. Socket Programming
322. TCP Socket Implementation
323. UDP Communication
324. HTTP Client Development
325. HTTP Server Creation
326. WebSocket Protocols
327. FTP Operations
328. Email Handling
329. Custom Protocol Development
330. Network Security
331. SSL/TLS Integration
332. API Integration
```

### 🖥️ **Module 23: GUI Development**

```
333. GUI Programming Overview
334. IUP Library Usage
335. Tk Bindings
336. Qt Integration
337. GTK Support
338. Event Handling
339. Layout Management
340. Custom Widgets
341. Dialog Systems
342. Cross-platform GUI
343. Mobile Applications
344. Desktop Applications
```

---

## 🎯 **MASTERY LEVEL**

### ⚡ **Module 24: Performance & Optimization**

```
345. Performance Analysis
346. Profiling Tools
347. Memory Optimization
348. CPU Performance
349. Garbage Collection Tuning
350. JIT Compilation
351. Cache Optimization
352. Algorithm Optimization
353. Data Structure Selection
354. Benchmarking Methods
355. Load Testing
356. Scalability Planning
```

### 🛡️ **Module 25: Security & Best Practices**

```
357. Security Overview
358. Input Validation
359. Code Injection Prevention
360. Sandbox Implementation
361. Cryptography Integration
362. Hash Functions
363. Encryption Methods
364. Secure Coding Practices
365. Vulnerability Assessment
366. Security Auditing
367. Penetration Testing
368. Security Monitoring
```

### 🔗 **Module 26: Language Interoperability**

```
369. Multi-language Integration
370. Python Bindings
371. Java Integration
372. .NET Connections
373. JavaScript Bridges
374. C++ Integration
375. Shell Script Interfacing
376. Foreign Function Interface
377. API Gateway Development
378. Microservice Communication
379. Protocol Buffers
380. Message Queues
```

---

## 🏗️ **ARCHITECTURE & PATTERNS**

### 🏛️ **Module 27: Software Architecture**

```
381. Architecture Patterns
382. MVC Implementation
383. MVP Pattern
384. MVVM Architecture
385. Layered Architecture
386. Plugin Systems
387. Event-Driven Architecture
388. Microservices Design
389. Domain-Driven Design
390. Clean Architecture
391. Hexagonal Architecture
392. CQRS Pattern
```

### 📋 **Module 28: Project Management**

```
393. Project Structure
394. Code Organization
395. Documentation Standards
396. Version Control Integration
397. Testing Strategies
398. Continuous Integration
399. Deployment Pipelines
400. Monitoring Systems
```

---

## 🎓 **MASTERY CAPSTONE**

### 🚀 **Module 29: Advanced Applications**

```
401. Compiler Construction
402. Domain Specific Languages
403. Code Generation
404. Metaprogramming
405. Dynamic Code Execution
406. Custom Interpreters
407. Virtual Machines
408. JIT Compilers
409. Transpilers
410. Static Analysis Tools
```

### 🌟 **Module 30: Career Excellence**

```
411. Portfolio Development
412. Open Source Contribution
413. Community Engagement
414. Technical Writing
415. Conference Speaking
416. Mentoring Others
417. Industry Applications
418. Career Advancement
419. Continuous Learning
420. Future Technologies
```

---

## 🎉 **CONGRATULATIONS!**

### _You've mastered the most elegant and powerful scripting language!_

> **"Lua: Small, Fast, Powerful - The Perfect Language for Everything"**

---

## 📈 **LEARNING PATH SUMMARY**

- **🌟 Foundation**: 38 topics (Modules 1-3)
- **🎯 Intermediate**: 29 topics (Modules 4-5)
- **🚀 Advanced**: 115 topics (Modules 6-8)
- **🔮 Expert**: 66 topics (Modules 9-11)
- **🌐 Specialized**: 58 topics (Modules 12-15)
- **🚀 Professional**: 64 topics (Modules 16-20)
- **🔧 Integration**: 36 topics (Modules 21-23)
- **🎯 Mastery**: 24 topics (Modules 24-25)
- **🔗 Architecture**: 20 topics (Modules 26-28)
- **🎓 Capstone**: 20 topics (Modules 29-30)

### **TOTAL: 420 COMPREHENSIVE TOPICS**

## 🌙 **THE LUA MASTERY JOURNEY**

_From simple scripts to enterprise systems - Lua powers it all!_

## Ringkasan Kurikulum

**Total Materi: 400**
**Kategori: 30 Bagian Utama**

Lua memiliki lebih banyak materi karena:

1. **Fleksibilitas Tinggi** - Lua dapat digunakan di berbagai domain (web, game, embedded, data science)
2. **Ekosistem Luas** - Integrasi dengan banyak bahasa dan platform
3. **Aplikasi Khusus** - Banyak use case spesifik seperti game development (LÖVE2D), web development (OpenResty), embedded systems (NodeMCU)
4. **Tingkat Abstraksi** - Dari low-level C API hingga high-level frameworks
5. **Spesialisasi Domain** - Setiap bidang memiliki tools dan teknik khusus

Kurikulum ini mencakup semua aspek Lua dari dasar hingga aplikasi enterprise dan penelitian tingkat lanjut. 🌙✨
