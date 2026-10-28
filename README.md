# Belajar Backend untuk Full Stack

Proyek latihan: **API pelacak lamaran kerja** (Node.js, TypeScript, Express, PostgreSQL di Docker).

Tujuan akhir: kamu bisa membangun dan menjelaskan backend sendiri (database, autentikasi, tes, deploy) dan menunjukkannya sebagai bukti kemampuan full stack.

## Aturan main

1. **Kamu yang menulis kodenya.** AI hanya memberi petunjuk dan mereview. Kalau kamu menyalin solusi tanpa paham, modul itu belum selesai.
2. **Pindah modul hanya kalau semua poin "Lulus jika" terpenuhi.** Bergerak per modul, bukan per hari.
3. **Tiap modul diakhiri satu paragraf catatan** dengan kata-katamu sendiri (simpan di `progress.md`). Ini bahan wawancara.
4. **Pakai data dummy.** Jangan masukkan data lamaran asli, kunci, token, atau informasi pribadi ke chat AI mana pun.
5. **Macet lebih dari 30 menit?** Tempel pesan error persis dan apa yang sudah kamu coba. Jangan tulis "tidak jalan" saja.
6. **Setiap klaim teknis dari AI harus bisa dicek** di dokumentasi resmi atau dibuktikan lewat kode yang dijalankan.
7. **Versi paket berubah.** Kalau perintah atau API di materi tidak cocok dengan yang terpasang, baca dokumentasi versi yang terpasang, jangan menebak.

## Peta modul

Perkiraan jam itu kasar dan belum memperhitungkan kecepatan belajarmu. Total sekitar 64 jam.

| # | Modul | Jam |
|---|---|---|
| 0 | [Setup lingkungan](00-setup.md) | 1 |
| 1 | [PostgreSQL dan SQL dasar](modules/01-postgres-sql-dasar.md) | 3 |
| 2 | [JOIN dan agregasi](modules/02-join-agregasi.md) | 3 |
| 3 | [Desain skema dan constraint](modules/03-skema-constraint.md) | 4 |
| 4 | [Transaksi dan indeks](modules/04-transaksi-indeks.md) | 4 |
| 5 | [Migrasi dan seed](modules/05-migrasi-seed.md) | 3 |
| 6 | [Express, TypeScript, koneksi database](modules/06-express-ts-koneksi-db.md) | 4 |
| 7 | [CRUD, validasi, penanganan error](modules/07-crud-validasi-error.md) | 5 |
| 8 | [Filter, paginasi, query aman](modules/08-filter-paginasi-query-aman.md) | 4 |
| 9 | [Pengujian](modules/09-pengujian.md) | 6 |
| 10 | [Autentikasi](modules/10-autentikasi.md) | 6 |
| 11 | [Otorisasi dan keamanan](modules/11-otorisasi-keamanan.md) | 5 |
| 12 | [Prisma](modules/12-prisma.md) | 5 |
| 13 | [Job terjadwal](modules/13-job-terjadwal.md) | 4 |
| 14 | [Docker, deploy, cadangan](modules/14-docker-deploy-cadangan.md) | 8 |

## Struktur folder

```
learn/                      <- materi ini (jangan taruh kode proyek di sini)
  README.md  CLAUDE.md  progress.md  00-setup.md
  modules/01..14

~/Developer/job-tracker/    <- kode proyekmu (repo git terpisah)
```

## Cara belajar per modul

1. Baca bagian **Tujuan** dan **Konsep inti**.
2. Kerjakan **Langkah** dan **Tugas**. Coba sendiri minimal 20 menit sebelum membuka **Petunjuk**.
3. Periksa semua poin **Lulus jika**.
4. Jawab **Kuis mandiri** dengan kata-katamu sendiri (satu sampai tiga kalimat per jawaban), tanpa melihat materi.
5. Tulis satu paragraf catatan di `progress.md`, lalu centang modul.

## Memakai Claude Code sebagai pembimbing

Jalankan dari folder `learn/` (Claude Code membaca `CLAUDE.md` otomatis):

```bash
cd learn
claude
```

Prompt awal sesi:

> Ikuti CLAUDE.md. Saya mengerjakan Modul N. Baca progress.md dan file modulnya, mulai dengan 3 pertanyaan kuis, lalu pandu saya sebagai pembimbing. Jangan beri solusi lengkap.

Prompt untuk setor tugas:

> Saya setor Modul N. Periksa terhadap setiap poin "Lulus jika". Ini kode, hasil tes, dan jawaban kuis saya: (tempel). Beri umpan balik: yang benar, yang kurang, langkah berikutnya.

## Nanti: integrasi Hermes

Setelah pengingat dan digest terbukti stabil, Hermes bisa membaca `progress.md` dan `CLAUDE.md` yang sama untuk mengirim tugas dan kuis harian ke Discord. Sebelum itu, belajar manual saja.
