# Modul 5: Migrasi dan seed

Perkiraan: 3 jam. Prasyarat: Modul 4.

## Tujuan

Mengelola perubahan skema sebagai file berurutan yang bisa dijalankan ulang dengan satu perintah, dan memisahkan data seed dari skema.

## Konsep inti

- Migrasi: file berurutan (`001_...`, `002_...`) yang membawa skema dari versi N ke N+1. Disimpan di Git.
- Tabel pencatat migrasi (misal `schema_migrations`) menyimpan migrasi mana yang sudah dijalankan.
- **Jangan edit migrasi yang sudah dijalankan** di lingkungan bersama; tambahkan migrasi baru.
- Idempotent: menjalankan runner dua kali tidak merusak apa pun dan tidak menjalankan migrasi dua kali.
- Setiap migrasi dijalankan dalam transaksi, supaya gagal di tengah tidak meninggalkan setengah skema.
- Seed untuk data dev; jangan sama dengan data produksi.

## Langkah

1. Inisialisasi proyek Node + TypeScript di `~/Developer/job-tracker` (`npm init`, TypeScript, tsx atau sejenis).
2. Pasang driver `pg` dan buat folder `migrations/`.
3. Pecah skema Modul 3 menjadi beberapa file migrasi berurutan.
4. **Tulis sendiri** runner kecil (`scripts/migrate.ts`): baca file, cek tabel pencatat, jalankan yang belum, masing-masing dalam transaksi.
5. Tambahkan skrip npm: `db:migrate`, `db:seed`, `db:reset`.
6. `db:reset` harus **menolak** berjalan jika `NODE_ENV=production`.
7. Jalankan `db:migrate` dua kali. Kedua kali tidak boleh error dan yang kedua tidak melakukan apa-apa.

## Petunjuk

Pertanyaan penuntun:
- Bagaimana runner tahu migrasi mana yang sudah jalan?
- Jika migrasi 003 gagal di tengah, apa kondisi database dan tabel pencatat?
- Bagaimana mengurutkan file dengan benar (hati-hati urutan leksikografis)?

Contoh sintaks transaksi di Node pada domain lain:

```ts
const client = await pool.connect();
try {
  await client.query('BEGIN');
  await client.query('INSERT INTO notes (body) VALUES ($1)', ['halo']);
  await client.query('COMMIT');
} catch (e) {
  await client.query('ROLLBACK');
  throw e;
} finally {
  client.release();
}
```

Catatan: alat siap pakai (node-pg-migrate, dll.) ada, tapi menulis runner sendiri sekali membuatmu paham yang dilakukan alat itu. Setelah paham, boleh pakai alat.

## Tugas

Selesaikan langkah 1 sampai 7 dan tulis `README` singkat di repo proyek berisi cara menjalankan tiga skrip tadi dari database kosong.

## Lulus jika

- [ ] Dari database kosong, satu perintah (`npm run db:migrate`) membuat seluruh skema.
- [ ] Menjalankan `db:migrate` dua kali tidak error dan tidak menduplikasi.
- [ ] Setiap migrasi berjalan dalam transaksi; kamu mendemonstrasikan migrasi yang sengaja salah tidak meninggalkan setengah perubahan.
- [ ] `db:reset` menolak jalan bila `NODE_ENV=production`.
- [ ] Kamu bisa menjelaskan kenapa migrasi yang sudah dijalankan tidak boleh diedit.

## Kuis mandiri

1. Apa fungsi tabel `schema_migrations`?
2. Kenapa migrasi sebaiknya di dalam transaksi? Ada jenis perintah yang tidak bisa? (Cari tahu di dokumentasi.)
3. Apa risiko mengedit file migrasi lama setelah tim lain menjalankannya?
4. Kenapa `db:reset` butuh penjaga produksi?
5. Apa beda data seed dan data migrasi (misalnya data referensi yang wajib ada)?

## Referensi

- node-postgres: https://node-postgres.com/
- Node.js: https://nodejs.org/en/docs
