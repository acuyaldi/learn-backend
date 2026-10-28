# Modul 8: Filter, paginasi, query aman

Perkiraan: 4 jam. Prasyarat: Modul 7.

## Tujuan

Menambah filter, pengurutan, dan paginasi pada `GET /applications`, dan memahami serta menutup SQL injection lewat percobaan nyata.

## Konsep inti

- Query string: `?status=interview&limit=20&cursor=...`. Semua nilainya string dan harus divalidasi.
- Paginasi offset (`LIMIT`/`OFFSET`): sederhana, makin lambat di offset besar, hasil bisa bergeser saat data berubah.
- Paginasi keyset/cursor: stabil dan cepat, butuh urutan yang deterministik dan indeks yang cocok.
- Urutan harus deterministik: tambahkan kolom pembeda (misal `id`) sebagai tie-breaker.
- Nama kolom untuk `ORDER BY` **tidak bisa** diparameterkan dengan `$1`. Pakai allow-list.
- SQL injection: input pengguna yang menjadi bagian struktur SQL.

## Langkah

1. Filter `status`, rentang tanggal, dan pencarian teks sederhana (`ILIKE`) pada `GET /applications`.
2. Pengurutan dengan parameter `sort` yang dibatasi allow-list (misal `applied_at`, `company`).
3. Paginasi: implementasikan offset dahulu, lalu keyset. Ukur dengan `EXPLAIN ANALYZE` di data besar (Modul 4).
4. Batasi `limit` (nilai maksimum) dan beri default.
5. **Lab SQL injection** (di endpoint khusus, hanya lokal, dihapus setelahnya): tulis versi yang menyusun SQL dengan string dan tunjukkan input yang membocorkan atau merusak data dummy. Lalu perbaiki dan tunjukkan input yang sama kini tidak berbahaya.
6. Respons paginasi menyertakan cursor atau info halaman berikutnya.

## Petunjuk

Pertanyaan penuntun:
- Bagaimana membangun `WHERE` dinamis tanpa menggabungkan nilai ke string? (Susun daftar kondisi dan array nilai secara terpisah.)
- Mengapa `ORDER BY $1` tidak bekerja seperti yang kamu kira?
- Bagaimana keyset menangani dua baris dengan nilai urut sama?

Contoh pada domain lain (urutan allow-list):

```ts
const SORTABLE = { title: 'title', pages: 'pages' } as const;
const col = SORTABLE[req.query.sort as keyof typeof SORTABLE] ?? 'title';
const sql = `SELECT id, title FROM books ORDER BY ${col}, id LIMIT $1`;
```

Perhatikan: yang disisipkan ke string adalah nilai dari allow-list milikmu, bukan input mentah.

## Tugas

Selesaikan langkah 1 sampai 6 dan tulis `docs/sql-injection.md`: input penyerang, apa yang terjadi, perbaikannya, dan kenapa perbaikan itu bekerja.

## Lulus jika

- [ ] Filter, urutan, dan paginasi bekerja dan semua parameter divalidasi (nilai aneh menghasilkan 400, bukan 500).
- [ ] `limit` punya batas atas dan default.
- [ ] Tidak ada nilai input yang digabung ke SQL; nama kolom urutan memakai allow-list.
- [ ] Lab injection menunjukkan serangan yang berhasil pada versi rentan dan gagal pada versi aman.
- [ ] Paginasi keyset tidak melewatkan atau menggandakan baris saat ada dua baris dengan nilai urut sama.
- [ ] Kamu bisa menjelaskan kapan memilih offset vs keyset.

## Kuis mandiri

1. Mengapa nilai query string harus divalidasi walau sudah bertipe string?
2. Kenapa query berparameter mencegah SQL injection, dan apa yang tetap tidak terlindungi olehnya?
3. Kelemahan paginasi offset pada tabel besar apa?
4. Kenapa urutan harus punya tie-breaker?
5. Apa bahayanya `limit` yang tidak dibatasi?

## Referensi

- OWASP (SQL Injection): https://owasp.org/
- PostgreSQL: https://www.postgresql.org/docs/
