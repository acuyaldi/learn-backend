# Modul 1: PostgreSQL dan SQL dasar

Perkiraan: 3 jam. Prasyarat: Modul 0.

## Tujuan

Memahami database relasional sebagai tabel berisi baris bertipe, dan menulis `CREATE TABLE`, `INSERT`, `SELECT`, `UPDATE`, `DELETE` sendiri.

## Konsep inti

- Tabel, kolom bertipe, baris. Beda dengan array of object di JS: skema dipaksa oleh database.
- Primary key: identitas unik tiap baris.
- Tipe dasar: `integer`, `text`, `boolean`, `timestamptz`, `date`, `numeric`.
- `NULL` bukan nol dan bukan string kosong. Perbandingannya memakai `IS NULL`, bukan `= NULL`.
- `WHERE` memfilter, `ORDER BY` mengurutkan, `LIMIT` membatasi.
- `UPDATE` dan `DELETE` tanpa `WHERE` mengubah **semua baris**.

## Langkah

1. Masuk `psql` (lihat Modul 0).
2. Buat tabel `companies` (id, name, website, created_at) dan `applications` (id, company_id, role_title, status, applied_at, notes).
3. Isi 5 baris dummy ke `applications` dan 3 ke `companies`.
4. Coba `SELECT` dengan `WHERE`, `ORDER BY`, `LIMIT`.
5. Coba `UPDATE` satu baris, lalu `DELETE` satu baris, selalu dengan `WHERE`.
6. Latihan aman: sebelum `UPDATE`/`DELETE`, jalankan dulu `SELECT` dengan `WHERE` yang sama untuk melihat baris yang akan terkena.

## Petunjuk

Contoh sintaks pada domain lain (perpustakaan), bukan jawaban tugas:

```sql
CREATE TABLE books (
  id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  title text NOT NULL,
  pages integer,
  published_on date
);

INSERT INTO books (title, pages) VALUES ('Contoh', 120);

SELECT title FROM books WHERE pages > 100 ORDER BY title LIMIT 5;
```

Pertanyaan penuntun:
- Kolom mana yang boleh kosong, mana yang wajib?
- Apa yang terjadi kalau `company_id` mengarah ke perusahaan yang tidak ada? (Dibahas serius di Modul 3.)

## Tugas

Selesaikan langkah 2 sampai 5 dan simpan perintah SQL-nya di `~/Developer/job-tracker/sql/01-dasar.sql`.

## Lulus jika

- [ ] Postgres berjalan di Docker dan kamu bisa masuk `psql`.
- [ ] Tabel `companies` dan `applications` ada, dibuat dengan SQL yang kamu tulis sendiri.
- [ ] Ada 5 baris di `applications`, dan satu `SELECT` dengan `WHERE` plus `ORDER BY` menghasilkan yang kamu harapkan.
- [ ] Kamu bisa menjelaskan fungsi primary key dengan kata-katamu sendiri.
- [ ] Kamu bisa menjelaskan kenapa `UPDATE` tanpa `WHERE` berbahaya dan kebiasaan apa yang mencegahnya.

## Kuis mandiri

1. Apa beda `NULL` dan string kosong? Bagaimana cara memfilter baris yang kolomnya `NULL`?
2. Apa yang terjadi bila kamu `INSERT` dua baris dengan primary key sama?
3. Kenapa `timestamptz` lebih disarankan daripada `timestamp` untuk waktu kejadian?
4. Apa yang dilakukan `LIMIT` tanpa `ORDER BY`, dan kenapa itu tidak bisa diandalkan?
5. Tulis dengan kata sendiri: mengapa tabel dengan skema lebih kuat daripada array objek bebas?

## Referensi

- Dokumentasi PostgreSQL: https://www.postgresql.org/docs/
