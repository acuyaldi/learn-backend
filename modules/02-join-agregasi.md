# Modul 2: JOIN dan agregasi

Perkiraan: 3 jam. Prasyarat: Modul 1.

## Tujuan

Menggabungkan tabel dengan `JOIN` dan meringkas data dengan `GROUP BY`.

## Konsep inti

- `INNER JOIN`: hanya baris yang cocok di kedua tabel.
- `LEFT JOIN`: semua baris tabel kiri, kolom kanan `NULL` bila tidak cocok.
- Alias tabel (`FROM applications a`) membuat query terbaca.
- Fungsi agregat: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`.
- `GROUP BY` mengelompokkan. `WHERE` memfilter baris sebelum dikelompokkan, `HAVING` memfilter kelompok.
- `COUNT(*)` menghitung baris, `COUNT(kolom)` melewati `NULL`.

## Langkah

1. Tambah data sampai ada minimal 3 perusahaan, 10 lamaran, dan satu perusahaan **tanpa** lamaran.
2. Tampilkan daftar lamaran beserta nama perusahaannya (`INNER JOIN`).
3. Tampilkan semua perusahaan beserta jumlah lamarannya, termasuk yang 0 (`LEFT JOIN` + `GROUP BY`).
4. Tampilkan jumlah lamaran per `status`.
5. Tampilkan hanya perusahaan yang punya lebih dari 1 lamaran (`HAVING`).

## Petunjuk

Contoh pada domain lain:

```sql
SELECT a.name, COUNT(b.id) AS total_books
FROM authors a
LEFT JOIN books b ON b.author_id = a.id
GROUP BY a.id, a.name
ORDER BY total_books DESC;
```

Pertanyaan penuntun:
- Kenapa `COUNT(b.id)` dan bukan `COUNT(*)` di contoh itu?
- Apa bedanya hasil `INNER JOIN` dan `LEFT JOIN` untuk perusahaan tanpa lamaran?
- Kolom apa yang harus ada di `GROUP BY`?

## Tugas

Tulis query langkah 2 sampai 5 di `sql/02-join.sql`. Untuk tiap query, tulis satu komentar SQL yang menjelaskan pertanyaan bisnis yang dijawabnya.

## Lulus jika

- [ ] Kelima query berjalan dan hasilnya sesuai data dummy.
- [ ] Perusahaan tanpa lamaran muncul dengan hitungan 0 di query langkah 3.
- [ ] Kamu bisa menjelaskan beda `INNER JOIN` dan `LEFT JOIN` memakai contoh dari datamu.
- [ ] Kamu bisa menjelaskan beda `WHERE` dan `HAVING`.

## Kuis mandiri

1. Kapan `LEFT JOIN` memberi hasil berbeda dari `INNER JOIN`?
2. Apa beda `COUNT(*)` dan `COUNT(kolom)`?
3. Kenapa `HAVING COUNT(*) > 1` tidak bisa ditulis di `WHERE`?
4. Apa yang error bila kolom non-agregat tidak ada di `GROUP BY`, dan kenapa?
5. Jika satu lamaran punya banyak catatan wawancara (tabel lain) dan kamu `JOIN` keduanya lalu `COUNT`, apa risikonya terhadap hasil hitungan?

## Referensi

- Dokumentasi PostgreSQL: https://www.postgresql.org/docs/
