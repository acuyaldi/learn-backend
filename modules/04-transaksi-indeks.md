# Modul 4: Transaksi dan indeks

Perkiraan: 4 jam. Prasyarat: Modul 3.

## Tujuan

Memahami transaksi (semua atau tidak sama sekali) dan indeks (kecepatan baca, harga tulis), serta membaca `EXPLAIN`.

## Konsep inti

- Transaksi: `BEGIN`, `COMMIT`, `ROLLBACK`. Sifat ACID, terutama atomicity.
- Kasus klasik: dua langkah yang harus berhasil bersama (misal pindah status dan catat riwayat).
- Dua sesi bersamaan bisa saling mengganggu. Level isolasi default PostgreSQL adalah `READ COMMITTED`.
- Indeks (B-tree) mempercepat `WHERE`, `JOIN`, `ORDER BY` pada kolom yang diindeks, tapi memperlambat tulis dan memakai ruang.
- `EXPLAIN` menampilkan rencana query; `EXPLAIN ANALYZE` menjalankannya dan mengukur. Perhatikan `Seq Scan` vs `Index Scan`.
- Planner tidak selalu memakai indeks, terutama pada tabel kecil.

## Langkah

1. Di `psql`, jalankan `BEGIN`, ubah beberapa baris, `ROLLBACK`, dan buktikan perubahan hilang.
2. Buka dua sesi `psql` sekaligus. Di sesi A mulai transaksi dan ubah satu baris tanpa `COMMIT`. Dari sesi B baca baris yang sama. Catat apa yang terlihat, lalu `COMMIT` dan baca lagi.
3. Buat data dummy besar (misal 200 ribu baris) dengan `generate_series`.
4. Jalankan `EXPLAIN ANALYZE` pada query filter kolom tertentu **sebelum** indeks. Catat jenis scan dan waktu.
5. Buat indeks, jalankan lagi, bandingkan.
6. Buat indeks gabungan (dua kolom) dan coba query yang memakai kolom pertama saja, kolom kedua saja, dan keduanya. Catat mana yang memakai indeks.

## Petunjuk

Contoh pada domain lain:

```sql
BEGIN;
UPDATE accounts SET balance = balance - 10 WHERE id = 1;
UPDATE accounts SET balance = balance + 10 WHERE id = 2;
COMMIT;

INSERT INTO logs (level, message)
SELECT 'info', 'row ' || g FROM generate_series(1, 200000) AS g;

CREATE INDEX idx_logs_level ON logs (level);
EXPLAIN ANALYZE SELECT * FROM logs WHERE level = 'info' LIMIT 10;
```

Pertanyaan penuntun:
- Kalau langkah kedua gagal setelah langkah pertama berhasil, tanpa transaksi apa yang terjadi?
- Query apa yang tidak terbantu indeks yang kamu buat?

## Tugas

Tulis temuanmu di `docs/transaksi-indeks.md`: hasil percobaan dua sesi, dan tabel sebelum/sesudah indeks (jenis scan, waktu). Tuliskan juga satu kasus di mana indeks tidak membantu.

## Lulus jika

- [ ] Kamu mendemonstrasikan `ROLLBACK` membatalkan perubahan.
- [ ] Kamu bisa menjelaskan apa yang dilihat sesi B sebelum dan sesudah `COMMIT` sesi A.
- [ ] Ada perbandingan `EXPLAIN ANALYZE` sebelum dan sesudah indeks pada data besar, dengan angka.
- [ ] Kamu bisa menjelaskan harga yang dibayar untuk setiap indeks.
- [ ] Kamu bisa menjelaskan kenapa indeks gabungan `(a, b)` tidak membantu query yang hanya memfilter `b`.

## Kuis mandiri

1. Apa arti atomicity? Beri contoh bila tidak ada.
2. Apa yang terjadi bila koneksi putus di tengah transaksi sebelum `COMMIT`?
3. Kenapa menambahkan indeks di setiap kolom bukan ide bagus?
4. Apa beda `Seq Scan` dan `Index Scan`, dan kenapa planner kadang memilih `Seq Scan` walau indeks ada?
5. Apa beda `EXPLAIN` dan `EXPLAIN ANALYZE`, dan bahaya menjalankan `EXPLAIN ANALYZE` pada `UPDATE`/`DELETE`?

## Referensi

- Dokumentasi PostgreSQL (Concurrency Control, Indexes, Performance Tips): https://www.postgresql.org/docs/
