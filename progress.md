# Progress

Mulai: 2026-11-12
Modul aktif: 1
Lingkungan: Windows 11 + WSL2 Ubuntu, Docker Desktop, proyek di `~/Developer/job-tracker` (dalam WSL). Postgres Windows `postgresql-x64-18` dihentikan dan diset Manual; jangan dinyalakan bersamaan (bentrok port 5432).

## Daftar modul

- [x] 0 Setup lingkungan (2026-11-12)
- [ ] 1 PostgreSQL dan SQL dasar
- [ ] 2 JOIN dan agregasi
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

- Loopback dan port mapping: arti `127.0.0.1` vs `0.0.0.0`, format `IP:portHost:portContainer`. Paham setelah dijelaskan. Kuis ulang: 2026-11-15, 2026-11-19.
- Karakter tak terlihat di file: pagar markdown ikut tersalin, line ending CRLF. Cara cek: `file`, `cat -A`. Kuis ulang: 2026-11-15, 2026-11-19.

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
