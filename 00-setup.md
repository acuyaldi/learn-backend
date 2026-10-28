# Modul 0: Setup lingkungan

Perkiraan: 1 jam

## Tujuan

Punya folder proyek, Git, dan PostgreSQL yang berjalan di Docker, siap dipakai mulai Modul 1.

## Cek versi

Yang sudah terpasang di Mac kamu (dari pengecekan sebelumnya): Node v24, npm 11, Git 2.55, Docker 29, Docker Compose v5, Homebrew 6. Semuanya cukup.

```bash
node -v && npm -v && git --version
docker --version && docker compose version
```

Pasang alat bantu kecil:

```bash
brew install jq
```

**Jangan** `brew install postgresql`. Database berjalan di Docker, dan Postgres di host bisa bentrok di port 5432.

## Folder proyek

Kode proyek hidup di folder terpisah dari `learn/`:

```bash
mkdir -p ~/Developer/job-tracker && cd ~/Developer/job-tracker
git init -b main
```

Buat `.gitignore`:

```
node_modules/
dist/
coverage/
.env
.env.*
!.env.example
.DS_Store
```

## PostgreSQL di Docker

Buat `docker-compose.yml` di folder proyek:

```yaml
services:
  db:
    image: postgres:17
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: dev_password_only
      POSTGRES_DB: tracker
    ports:
      - "127.0.0.1:5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

Catatan:
- Port hanya dibuka ke `127.0.0.1`, jadi database tidak terlihat dari jaringan. Biasakan sejak sekarang.
- Password ini hanya untuk latihan lokal. Jangan dipakai di server.
- `postgres:17` boleh diganti versi stabil yang lebih baru. Cek tag di Docker Hub.

Jalankan dan masuk:

```bash
docker compose up -d
docker compose ps
docker compose exec db psql -U app -d tracker
```

Di dalam `psql`: `\l` (daftar database), `\dt` (daftar tabel), `\q` (keluar).

## Perintah yang perlu dikenal

```bash
docker compose logs db          # lihat log database
docker compose stop             # hentikan, data tetap
docker compose down             # hapus container, data tetap (volume)
docker compose down -v          # HAPUS container DAN data. Hanya untuk reset latihan.
```

## Masalah umum

- **Port 5432 dipakai:** `lsof -i :5432` untuk melihat prosesnya. Hentikan Postgres lain atau ganti port host (`"127.0.0.1:5433:5432"`).
- **Docker tidak merespons:** buka Docker Desktop dan tunggu sampai statusnya stabil.
- **Lupa password atau ingin mulai bersih:** `docker compose down -v` lalu `docker compose up -d`.

## Lulus jika

- [ ] `docker compose ps` menunjukkan service `db` berjalan.
- [ ] Kamu bisa masuk `psql` dan menjalankan `\l`.
- [ ] Folder `~/Developer/job-tracker` adalah repo Git dengan `.gitignore` di atas.
- [ ] Kamu bisa menjelaskan kenapa port dibuka hanya ke `127.0.0.1`.

## Kuis mandiri

1. Apa beda `docker compose down` dan `docker compose down -v`?
2. Kenapa Postgres dijalankan di Docker, bukan dipasang langsung di Mac?
3. Apa arti `127.0.0.1:5432:5432` pada baris `ports`?
