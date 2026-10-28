# Modul 7: CRUD, validasi, penanganan error

Perkiraan: 5 jam. Prasyarat: Modul 6.

## Tujuan

Membangun endpoint CRUD lengkap untuk lamaran dengan validasi input (zod) dan format error yang konsisten.

## Konsep inti

- REST: kata benda di URL, metode HTTP sebagai aksi (`GET`, `POST`, `PATCH`, `DELETE`).
- Kode status yang tepat: 200, 201, 204, 400, 404, 409, 422, 500.
- Jangan percaya input. Validasi bentuk, tipe, rentang di batas sistem (request body, query, params).
- zod: skema sekali, dapat tipe TypeScript dari skema (`z.infer`).
- Error terpusat: satu middleware error yang memetakan kesalahan ke respons JSON seragam. Jangan bocorkan detail internal (stack trace, SQL) ke klien.
- Pelanggaran constraint database (misal `UNIQUE`) dipetakan ke 409, bukan 500.

## Langkah

1. Endpoint untuk `applications`: `POST /applications`, `GET /applications`, `GET /applications/:id`, `PATCH /applications/:id`, `DELETE /applications/:id`.
2. Skema zod untuk body create dan update; validasi `:id` sebagai angka.
3. Format error seragam, misal `{ "error": { "code": "VALIDATION_ERROR", "message": "...", "details": [...] } }`.
4. Middleware error terpusat dan middleware 404 untuk rute tak dikenal.
5. Petakan error Postgres: kode `23505` (unique) ke 409, `23503` (foreign key) ke 409 atau 422, `23514` (check) ke 422. Pilih dan tulis alasannya.
6. Uji manual dengan `curl` atau REST client: semua jalur sukses dan setiap jenis kegagalan.

## Petunjuk

Contoh pada domain lain:

```ts
import { z } from 'zod';

const CreateBook = z.object({
  title: z.string().min(1).max(200),
  pages: z.number().int().positive().optional(),
});
type CreateBook = z.infer<typeof CreateBook>;

const parsed = CreateBook.safeParse(req.body);
if (!parsed.success) {
  return res.status(400).json({ error: { code: 'VALIDATION_ERROR', details: parsed.error.issues } });
}
```

Pertanyaan penuntun:
- `PATCH` vs `PUT`: mana yang cocok untuk ubah sebagian field?
- Apa yang dikembalikan `DELETE` dan `POST` yang sukses?
- Apa yang terjadi kalau `GET /applications/abc` dan `GET /applications/999999`? Keduanya harus punya jawaban yang berbeda dan masuk akal.
- Cek dokumentasi zod versi yang terpasang untuk API yang tepat.

## Tugas

Selesaikan langkah 1 sampai 6 dan buat tabel di README proyek: endpoint, metode, kode status yang mungkin, contoh respons.

## Lulus jika

- [ ] Kelima endpoint CRUD bekerja terhadap database nyata.
- [ ] Input salah (tipe, field hilang, string terlalu panjang, id bukan angka) ditolak dengan 400/422 dan format error seragam.
- [ ] Id tidak ada menghasilkan 404; duplikat menghasilkan 409; tidak ada 500 untuk kesalahan klien.
- [ ] Tidak ada stack trace atau teks SQL di respons error.
- [ ] Tipe request di TypeScript berasal dari skema zod (tidak ditulis ganda).
- [ ] Kamu bisa menjelaskan perbedaan 400, 404, 409, 422.

## Kuis mandiri

1. Kenapa validasi di server tetap wajib walau form frontend sudah memvalidasi?
2. Kapan memakai 400 vs 422? (Jelaskan pilihan konsistenmu.)
3. Kenapa error 500 tidak boleh membawa detail internal?
4. Bagaimana pelanggaran `UNIQUE` di database sampai ke klien sebagai 409?
5. Apa keuntungan satu middleware error terpusat dibanding `try/catch` di setiap handler?

## Referensi

- Express: https://expressjs.com/
- zod: https://zod.dev/
- Kode error PostgreSQL: https://www.postgresql.org/docs/ (Appendix, "PostgreSQL Error Codes")
