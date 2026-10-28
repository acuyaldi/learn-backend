# Modul 6: Express, TypeScript, koneksi database

Perkiraan: 4 jam. Prasyarat: Modul 5.

## Tujuan

Menjalankan server HTTP dengan Express 5 + TypeScript yang terhubung ke Postgres lewat connection pool, dengan konfigurasi dari environment.

## Konsep inti

- Request/response, route, middleware (fungsi berantai), `req`/`res`.
- Konfigurasi lewat environment variable, bukan hardcode. `.env` tidak masuk Git; `.env.example` berisi nama variabel saja.
- `Pool` dari `pg`: sekumpulan koneksi dipakai bergantian. Satu koneksi per request tanpa pool akan menghabiskan batas koneksi database.
- Query berparameter (`$1`, `$2`): nilai dikirim terpisah dari teks SQL.
- Health check: endpoint yang memastikan server dan database hidup.
- Graceful shutdown: tutup server dan pool saat proses dihentikan.

## Langkah

1. Pasang `express` dan tipenya; atur `tsconfig` ketat (`strict: true`).
2. Struktur sederhana: `src/server.ts` (start), `src/app.ts` (konfigurasi Express, mudah dites nanti), `src/db.ts` (pool), `src/config.ts` (baca env, gagal cepat jika kurang).
3. Buat `GET /health` yang mengembalikan status server dan hasil `SELECT 1` ke database.
4. Buat `GET /companies` yang membaca dari tabel (query berparameter bila perlu).
5. Tangani `SIGINT`/`SIGTERM`: tutup server lalu `pool.end()`.
6. Buat `.env.example` dan pastikan `.env` ada di `.gitignore`.

## Petunjuk

Contoh pada domain lain:

```ts
import express from 'express';
import { Pool } from 'pg';

const pool = new Pool({ connectionString: process.env.DATABASE_URL });
const app = express();

app.get('/books', async (_req, res) => {
  const { rows } = await pool.query('SELECT id, title FROM books WHERE pages > $1', [100]);
  res.json(rows);
});
```

Pertanyaan penuntun:
- Apa yang terjadi bila `DATABASE_URL` tidak diset? Di mana sebaiknya itu ketahuan?
- Kenapa `app.ts` dipisah dari `server.ts`?
- Mengapa tidak boleh menyusun SQL dengan template string yang berisi input pengguna?
- Express 5 menangani error di handler async dengan cara berbeda dari Express 4; cek dokumentasi versi yang terpasang.

## Tugas

Selesaikan langkah 1 sampai 6 dan buktikan dengan `curl`: `/health` ok saat database hidup, dan melaporkan masalah saat database dihentikan (`docker compose stop`).

## Lulus jika

- [ ] Server jalan dengan satu perintah (`npm run dev`) dan `strict: true` aktif tanpa error tipe.
- [ ] `/health` mengecek database dan responsnya berubah saat database mati.
- [ ] Koneksi database memakai `Pool`, dan konfigurasi hanya dari environment.
- [ ] `.env` tidak ada di Git; `.env.example` ada.
- [ ] Ctrl+C menutup server dan pool dengan bersih.
- [ ] Kamu bisa menjelaskan fungsi middleware dan urutan eksekusinya.

## Kuis mandiri

1. Apa itu connection pool dan masalah apa yang dicegahnya?
2. Kenapa konfigurasi dibaca dari environment dan gagal cepat saat kurang?
3. Apa itu middleware, dan apa yang terjadi bila lupa memanggil `next()` (atau mengirim respons)?
4. Apa bedanya `$1` dengan menyisipkan string ke SQL?
5. Apa itu graceful shutdown dan apa yang terjadi tanpa itu pada request yang sedang berjalan?

## Referensi

- Express: https://expressjs.com/
- node-postgres: https://node-postgres.com/
- TypeScript: https://www.typescriptlang.org/docs/
