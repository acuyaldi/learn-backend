# Modul 3: Desain skema dan constraint

Perkiraan: 4 jam. Prasyarat: Modul 2.

## Tujuan

Merancang skema yang membuat data salah **tidak bisa disimpan**, lewat foreign key, `NOT NULL`, `UNIQUE`, dan `CHECK`.

## Konsep inti

- Validasi di aplikasi saja tidak cukup. Database adalah garis pertahanan terakhir.
- Foreign key menjaga relasi. `ON DELETE` menentukan nasib baris anak (`RESTRICT`, `CASCADE`, `SET NULL`).
- Relasi: satu-ke-banyak (perusahaan ke lamaran), banyak-ke-banyak (lamaran ke tag, lewat tabel penghubung).
- Normalisasi: jangan menyalin nama perusahaan di setiap lamaran; simpan sekali, rujuk dengan id.
- Tipe enum atau `CHECK` untuk status terbatas.

## Langkah

1. Rancang ulang skema di atas kertas: `users`, `companies`, `applications`, `tags`, `application_tags`, `interview_notes`.
2. Tulis `CREATE TABLE` lengkap dengan:
   - foreign key,
   - `NOT NULL` pada kolom wajib,
   - `UNIQUE` (misal nama perusahaan per user),
   - `CHECK` untuk `status` (misal `applied`, `interview`, `offer`, `rejected`).
3. Tulis tabel penghubung `application_tags` dengan primary key gabungan.
4. Sengaja langgar tiap constraint dengan `INSERT` yang salah, dan catat pesan errornya.
5. Putuskan `ON DELETE` untuk tiap foreign key dan tulis alasannya.

## Petunjuk

Contoh pada domain lain:

```sql
CREATE TABLE orders (
  id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  customer_id integer NOT NULL REFERENCES customers(id) ON DELETE RESTRICT,
  state text NOT NULL CHECK (state IN ('new', 'paid', 'shipped'))
);

CREATE TABLE order_items (
  order_id integer REFERENCES orders(id) ON DELETE CASCADE,
  product_id integer REFERENCES products(id),
  PRIMARY KEY (order_id, product_id)
);
```

Pertanyaan penuntun:
- Kalau perusahaan dihapus, apakah lamarannya harus ikut hilang? Apa konsekuensi tiap pilihan?
- Kolom mana yang secara bisnis tidak boleh kosong?

## Tugas

Tulis skema final di `sql/03-skema.sql` dan satu file `docs/skema.md` berisi: diagram sederhana (teks atau Mermaid) dan alasan tiap keputusan `ON DELETE`.

## Lulus jika

- [ ] Skema punya minimal 5 tabel dengan foreign key yang benar.
- [ ] Ada minimal 3 jenis constraint berbeda (`NOT NULL`, `UNIQUE`, `CHECK`, FK).
- [ ] Kamu mendemonstrasikan tiap constraint menolak data salah dan mencatat pesan errornya.
- [ ] Setiap `ON DELETE` punya alasan tertulis.
- [ ] Kamu bisa menjelaskan kenapa tabel penghubung dibutuhkan untuk relasi banyak-ke-banyak.

## Kuis mandiri

1. Kenapa constraint di database tetap perlu walau API sudah memvalidasi input?
2. Apa beda `ON DELETE CASCADE` dan `RESTRICT`? Beri satu kasus yang cocok untuk masing-masing.
3. Apa itu primary key gabungan dan kapan dipakai?
4. Apa yang terjadi bila dua request mencoba menyisipkan nama perusahaan yang sama pada waktu bersamaan, dan constraint apa yang menyelamatkannya?
5. Kapan kamu sengaja tidak menormalisasi data, dan apa harganya?

## Referensi

- Dokumentasi PostgreSQL (bagian Data Definition): https://www.postgresql.org/docs/
