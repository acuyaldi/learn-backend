# Modul 12: Prisma

Perkiraan: 5 jam. Prasyarat: Modul 11.

## Tujuan

Memahami ORM (Prisma) setelah paham SQL mentah: kapan membantu, kapan menyulitkan, dan memindahkan sebagian lapisan data ke Prisma.

## Konsep inti

- ORM memetakan tabel ke objek/tipe dan menyusun SQL untuk kamu. Keuntungan: tipe aman, boilerplate berkurang. Harga: SQL yang dihasilkan tidak selalu efisien, dan abstraksi bocor.
- Skema Prisma (`schema.prisma`) mendeskripsikan model dan relasi; Prisma Migrate menghasilkan migrasi.
- **N+1 query**: mengambil daftar lalu satu query per item. Muncul di ORM dan SQL mentah.
- Relasi dimuat eksplisit (`include`/`select`).
- Selalu ada jalan keluar ke SQL mentah untuk query kompleks (tetap berparameter).
- API dan versi Prisma berubah antar rilis. **Ikuti dokumentasi versi yang terpasang**, jangan hafalan.

## Langkah

1. Pasang Prisma di proyek; gunakan introspeksi dari database yang sudah ada (`db pull`) dan bandingkan model hasilnya dengan skema yang kamu tulis sendiri di Modul 3.
2. Putuskan strategi migrasi ke depan: tetap dengan runner sendiri atau beralih ke Prisma Migrate. Tulis alasan dan konsekuensinya. **Jangan campur keduanya tanpa rencana**.
3. Pindahkan satu resource (misal `companies`) dari `pg` mentah ke Prisma Client, termasuk tes yang sudah ada (tes harus tetap lulus tanpa diubah banyak).
4. Buat query daftar lamaran beserta perusahaannya: sengaja tulis versi N+1, lihat log query, lalu perbaiki dengan satu query.
5. Tulis satu query kompleks dengan `$queryRaw` yang berparameter dan jelaskan kenapa ORM kurang cocok di situ.
6. Bandingkan: SQL yang dihasilkan Prisma (aktifkan log query) vs SQL tulisanmu untuk operasi yang sama.

## Petunjuk

Pertanyaan penuntun:
- Apa yang tidak bisa diekspresikan oleh `schema.prisma` yang ada di SQL-mu (misal `CHECK` tertentu, indeks parsial)?
- Bagaimana kamu mendeteksi N+1 tanpa menebak?
- Kapan kamu memilih SQL mentah meski ada ORM?

Contoh bentuk (domain lain, cek dokumentasi untuk sintaks versi terpasang):

```prisma
model Author {
  id    Int    @id @default(autoincrement())
  name  String
  books Book[]
}
model Book {
  id       Int    @id @default(autoincrement())
  title    String
  author   Author @relation(fields: [authorId], references: [id])
  authorId Int
}
```

## Tugas

Selesaikan langkah 1 sampai 6 dan tulis `docs/prisma-vs-sql.md`: tabel perbandingan (kemudahan, kontrol, performa, risiko), keputusan untuk proyekmu, dan alasannya.

## Lulus jika

- [ ] Prisma terhubung ke database yang sama dan satu resource sudah dipindahkan dengan tes tetap lulus.
- [ ] Kamu menunjukkan N+1 lewat log query dan memperbaikinya, dengan jumlah query sebelum dan sesudah.
- [ ] Ada satu query `$queryRaw` yang berparameter, dengan penjelasan alasannya.
- [ ] Ada keputusan tertulis soal strategi migrasi (runner sendiri vs Prisma Migrate) beserta alasannya.
- [ ] Kamu bisa menjelaskan kapan ORM menolong dan kapan menyulitkan.

## Kuis mandiri

1. Apa itu N+1 query dan bagaimana cara mendeteksinya?
2. Apa yang hilang jika kamu hanya mengandalkan ORM tanpa paham SQL?
3. Kenapa mencampur dua sistem migrasi pada satu database berisiko?
4. Apakah `$queryRaw` dengan template tag aman dari injection? Jelaskan, dan periksa di dokumentasi apa bedanya dengan varian "unsafe".
5. Kapan kamu memilih tidak memakai ORM sama sekali?

## Referensi

- Prisma: https://www.prisma.io/docs
