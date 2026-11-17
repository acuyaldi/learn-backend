# Progress

Mulai: 2026-11-12
Modul aktif: 3
Lingkungan: Windows 11 + WSL2 Ubuntu, Docker Desktop, proyek di `~/Developer/job-tracker` (dalam WSL). Postgres Windows `postgresql-x64-18` dihentikan dan diset Manual; jangan dinyalakan bersamaan (bentrok port 5432).

## Daftar modul

- [x] 0 Setup lingkungan (2026-11-12)
- [x] 1 PostgreSQL dan SQL dasar (2026-11-12)
- [x] 2 JOIN dan agregasi (2026-11-17)
- [ ] 3 Desain skema dan constraint
- [ ] 4 Transaksi dan indeks
- [ ] 5 Migrasi dan seed
- [ ] 6 Express, TypeScript, koneksi database
- [ ] 7 CRUD, validasi, penanganan error
- [ ] 8 Filter, paginasi, query aman
- [ ] 9 Pengujian
- [ ] 10 Autentikasi
- [ ] 11 Otorisasi dan keamanan
- [ ] 12 Prisma
- [ ] 13 Job terjadwal
- [ ] 14 Docker, deploy, cadangan

## Topik lemah

- Loopback dan port mapping: arti `127.0.0.1` vs `0.0.0.0`, format `IP:portHost:portContainer`. Paham setelah dijelaskan. Kuis 2026-11-17: masih menyebut `127.0.0.1` sebagai "port", belum bisa menjelaskan port host vs port container. Kuis ulang: 2026-11-20, 2026-11-24.
- Karakter tak terlihat di file: pagar markdown ikut tersalin, line ending CRLF. Cara cek: `file`, `cat -A`. Terulang di Modul 1: newline ikut tersimpan di string SQL saat copas (`+` di output psql). Terulang di Modul 2 (notes lamaran 4). Kuis 2026-11-17: penyebab benar, cara mencegah belum. Kuis ulang: 2026-11-19.
- Data yatim: `company_id` menunjuk perusahaan yang tidak ada, dan DB menerimanya karena belum ada foreign key. Lanjut di Modul 3. Terulang di Modul 2 (lamaran 9, `company_id = 4`), ditemukan lewat INNER vs LEFT JOIN. Kuis ulang: 2026-11-19.
- Kebiasaan aman `UPDATE`/`DELETE`: sempat menjalankan `DELETE` tanpa `WHERE` (terhapus semua) dan `DELETE` tanpa melihat pratinjau `SELECT`. Sekarang sudah konsisten pakai `WHERE id = ...`. Modul 2: pratinjau `WHERE company_id = 4` tapi UPDATE `WHERE id = 9` (WHERE tidak sama). Kuis 2026-11-17: alasan WHERE harus sama baru dijelaskan secara umum. Kuis ulang: 2026-11-20, 2026-11-24.
- psql: beda prompt `=#` (siap) vs `-#` (menunggu `;`), beda meta-command (`\dt`) vs SQL, petik tunggal = teks vs tanpa petik = nama kolom. Kuis ulang: 2026-11-15, 2026-11-19.
- Rumusan INNER vs LEFT JOIN: sempat menyebut NULL sebagai penyebab baris hilang di INNER JOIN, dan arah tabel kiri tertukar. Lulus setelah beberapa putaran. Inti: INNER membuang baris tanpa pasangan `ON`; LEFT menyimpan semua baris tabel kiri dan mengisi kolom kanan dengan NULL. Kuis ulang: 2026-11-20, 2026-11-24.
- Mencocokkan output dengan pertanyaan: dua kali menyetor query yang jalan tapi menjawab pertanyaan lain (langkah 2 dan 5 Modul 2). Kebiasaan: baca ulang kalimat langkah sebelum menyetor. Kuis ulang: 2026-11-20, 2026-11-24.

## Catatan per modul

Satu paragraf per modul, dengan kata-katamu sendiri: apa yang baru kamu pahami, apa yang masih membingungkan.

### Modul 0

(isi)

### Modul 1

(isi)

## Log sesi

| Tanggal | Modul | Kuis (0-5) | Catatan |
|---|---|---|---|
| 2026-11-12 | 0 | 3 | Setup WSL2 + Docker. Q1 benar, Q2 benar setelah petunjuk, Q3 paham setelah dijelaskan. Kendala: Postgres Windows di 5432, izin grup docker, CRLF. |
| 2026-11-12 | 1 | - | Lulus. File `sql/01-dasar.sql` bisa dijalankan dari nol. PK dan bahaya UPDATE tanpa WHERE dijelaskan benar; kebiasaan pratinjau SELECT dijelaskan setelah petunjuk. Kendala: data yatim, DELETE tanpa WHERE, newline di string. |
| 2026-11-17 | 2 | 1,5/3 | Kuis ulang topik lemah (port mapping, karakter tak terlihat, WHERE pratinjau). Langkah 2 (INNER JOIN) benar; beda INNER/LEFT dijelaskan setelah koreksi. Data yatim lamaran 9 ditemukan lewat INNER vs LEFT JOIN lalu diperbaiki. Pasang DBeaver (sempat pilih driver MySQL). Lulus: `sql/02-join.sql` berjalan, Xo = 0 di langkah 3, beda INNER/LEFT dan WHERE/HAVING dijelaskan. `COUNT(*)` vs `COUNT(kolom)` dipahami lewat perbandingan langsung. |
