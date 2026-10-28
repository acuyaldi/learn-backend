# Modul 14: Docker, deploy, cadangan

Perkiraan: 8 jam. Prasyarat: Modul 13.

## Tujuan

Mengemas aplikasi dalam Docker, men-deploy ke VPS dengan HTTPS, mengamankan server, dan membuktikan cadangan database bisa dipulihkan.

## Konsep inti

- Dockerfile multi-stage: build di satu tahap, jalankan hasil minimal di tahap akhir; jalan sebagai user non-root.
- `docker compose` untuk app + database + reverse proxy; volume untuk data.
- Reverse proxy (Caddy atau Nginx) menangani HTTPS (sertifikat otomatis di Caddy) dan meneruskan ke app.
- **Port yang dipublikasikan Docker melewati aturan `ufw`** karena Docker mengatur iptables sendiri. Port database tidak boleh dipublikasikan ke host publik; pakai jaringan internal compose atau bind ke `127.0.0.1`.
- Hardening dasar: login SSH dengan kunci, nonaktifkan login password dan root, firewall hanya 22/80/443, pembaruan keamanan otomatis, user non-root.
- Rahasia: lewat environment/secret di server, bukan di image atau Git.
- Cadangan: `pg_dump` terjadwal, disimpan di luar server yang sama. **Cadangan yang belum pernah dipulihkan belum bisa disebut cadangan.**
- Pemantauan minimal: log, health check, restart policy.

## Langkah

1. Tulis `Dockerfile` multi-stage dan `.dockerignore`; image berjalan sebagai non-root.
2. Tulis `docker-compose.prod.yml`: app, db (tanpa publish port), reverse proxy. Rahasia dari file env di server.
3. Siapkan VPS (kamu yang menyewa dan mengatur akses): buat user non-root, SSH hanya dengan kunci, nonaktifkan login password/root, `ufw` untuk 22/80/443, pembaruan otomatis.
4. Arahkan domain/subdomain ke VPS dan aktifkan HTTPS lewat reverse proxy.
5. Deploy: tarik kode, jalankan migrasi, naikkan compose. Tulis langkah deploy sebagai skrip/README yang bisa diulang.
6. Verifikasi dari **luar** server: aplikasi bisa diakses lewat HTTPS; port database (5432) **tidak** terjangkau (misal lewat pemindaian dari mesin lain atau layanan cek port).
7. Cadangan: skrip `pg_dump` terjadwal dengan retensi (misal 7 harian), simpan juga salinan di lokasi lain.
8. **Uji pemulihan:** pulihkan dump ke database kosong terpisah (scratch), bandingkan jumlah baris tabel utama dengan aslinya.
9. Uji reboot: restart VPS dan pastikan semua layanan naik sendiri dan data utuh.

## Petunjuk

Pertanyaan penuntun:
- Apa yang masuk ke image dan apa yang tidak boleh (rahasia, `.env`, `node_modules` lokal)?
- Bagaimana kamu menjamin `ufw` tidak "tertipu" oleh publish port Docker?
- Berapa lama data maksimum yang boleh hilang (RPO) dan berapa lama pemulihan yang bisa diterima (RTO)? Apakah jadwal cadangan memenuhi itu?
- Bagaimana kamu rollback bila deploy baru rusak?

Contoh pola Dockerfile pada domain lain (cek praktik terbaru di dokumentasi):

```dockerfile
FROM node:24-slim AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:24-slim
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app/package*.json ./
RUN npm ci --omit=dev
COPY --from=build /app/dist ./dist
USER node
CMD ["node", "dist/server.js"]
```

## Tugas

Selesaikan langkah 1 sampai 9 dan tulis `docs/deploy.md` berisi: arsitektur (diagram teks), langkah deploy, prosedur pemulihan cadangan, dan hasil uji yang kamu jalankan (dengan bukti: output perintah).

Catatan keamanan: jangan menempelkan kunci SSH privat, isi `.env`, atau token ke chat AI mana pun. Tempelkan output yang sudah disamarkan saja.

## Lulus jika

- [ ] Aplikasi berjalan di VPS lewat HTTPS dengan sertifikat valid.
- [ ] Port database tidak dapat dijangkau dari luar (bukti: hasil cek dari mesin lain).
- [ ] SSH hanya dengan kunci; login password dan root dinonaktifkan; firewall aktif.
- [ ] Container aplikasi berjalan sebagai non-root dan tidak ada rahasia di dalam image atau Git.
- [ ] Cadangan terjadwal ada dan satu cadangan sudah **dipulihkan** ke database scratch dengan jumlah baris cocok.
- [ ] Setelah reboot VPS, semua layanan naik sendiri.
- [ ] Langkah deploy bisa diulang dari dokumen oleh orang lain (atau kamu bulan depan).

## Kuis mandiri

1. Kenapa port yang dipublikasikan Docker bisa menembus `ufw`, dan bagaimana mencegahnya?
2. Kenapa image sebaiknya multi-stage dan berjalan non-root?
3. Apa beda cadangan yang "ada" dan cadangan yang "terbukti bisa dipulihkan"?
4. Apa itu RPO dan RTO, dan bagaimana kamu menentukan jadwal cadangan?
5. Bagaimana kamu me-rollback deploy yang gagal, termasuk bila ada migrasi skema?

## Referensi

- Docker: https://docs.docker.com/
- Caddy: https://caddyserver.com/docs/
- PostgreSQL (Backup and Restore): https://www.postgresql.org/docs/
- OWASP Cheat Sheet Series (Docker Security): https://cheatsheetseries.owasp.org/
