# Modul 0: Setup lingkungan

Perkiraan: 1 jam

## Tujuan

Punya folder proyek, Git, dan PostgreSQL yang berjalan di Docker, siap dipakai mulai Modul 1.

## Cek versi

Butuh: Node (LTS terbaru), npm, Git, Docker dengan Compose v2, dan `jq`. Semua perintah di modul ini memakai shell bash (macOS, Linux, atau WSL2 di Windows).

```bash
node -v && npm -v && git --version
docker --version && docker compose version
```

Pasang alat bantu kecil:

```bash
brew install jq          # macOS
sudo apt install jq      # Linux / WSL2 (Ubuntu)
```

**Jangan** pasang PostgreSQL langsung di host (`brew install postgresql`, `apt install postgresql`, atau installer Windows). Database berjalan di Docker, dan Postgres di host bisa bentrok di port 5432.

## Khusus Windows

Cara yang disarankan: **kerjakan semuanya di dalam WSL2**, supaya semua perintah di materi berlaku apa adanya. Modul 14 (VPS) juga Linux.

1. Pasang WSL2 + Ubuntu: `wsl --install` di PowerShell (admin), lalu restart. Lihat https://learn.microsoft.com/windows/wsl/install
2. Pasang Docker Desktop dan pastikan backend WSL2 aktif, termasuk integrasi untuk distro Ubuntu kamu (Settings → Resources → WSL Integration). Lihat https://docs.docker.com/desktop/features/wsl/
3. Di terminal Ubuntu, pasang Node (misalnya lewat nvm), Git, dan `jq` dengan `apt`.
4. Simpan proyek di filesystem Linux (`~/Developer/...` di dalam WSL), **bukan** di `/mnt/c/...`. Di `/mnt/c` operasi file lambat dan file watcher sering tidak mendeteksi perubahan.
5. Buka proyek dari VS Code dengan `code .` dari terminal WSL (extension WSL).

Kalau tetap memakai Windows native (PowerShell), perintahnya berbeda:

| Materi (bash) | Windows native |
|---|---|
| `brew install jq` | `winget install jqlang.jq` |
| `lsof -i :5432` | `Get-NetTCPConnection -LocalPort 5432` |
| `mkdir -p dir && cd dir` | `New-Item -ItemType Directory -Force dir; cd dir` |
| `curl ...` | `curl.exe ...` (di PowerShell 5.1, `curl` adalah alias `Invoke-WebRequest`) |
| `~/Developer` | `$HOME\Developer` |

Atau pakai Git Bash, yang menerima sebagian besar perintah bash di materi ini.

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

Buat `.gitattributes` supaya line ending tetap LF di semua OS. Script yang ber-line ending CRLF (Windows) gagal dijalankan di dalam container Linux.

```
* text=auto eol=lf
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

- **Port 5432 dipakai:** `lsof -i :5432` (Windows native: `Get-NetTCPConnection -LocalPort 5432`) untuk melihat prosesnya. Hentikan Postgres lain atau ganti port host (`"127.0.0.1:5433:5432"`).
- **Docker tidak merespons:** buka Docker Desktop dan tunggu sampai statusnya stabil.
- **Lupa password atau ingin mulai bersih:** `docker compose down -v` lalu `docker compose up -d`.

## Lulus jika

- [ ] `docker compose ps` menunjukkan service `db` berjalan.
- [ ] Kamu bisa masuk `psql` dan menjalankan `\l`.
- [ ] Folder `~/Developer/job-tracker` adalah repo Git dengan `.gitignore` dan `.gitattributes` di atas.
- [ ] Kamu bisa menjelaskan kenapa port dibuka hanya ke `127.0.0.1`.

## Kuis mandiri

1. Apa beda `docker compose down` dan `docker compose down -v`?
2. Kenapa Postgres dijalankan di Docker, bukan dipasang langsung di laptop?
3. Apa arti `127.0.0.1:5432:5432` pada baris `ports`?
