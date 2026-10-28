# Modul 13: Job terjadwal

Perkiraan: 4 jam. Prasyarat: Modul 12 (atau Modul 11 bila melewati Prisma).

## Tujuan

Membangun pengingat tindak lanjut lamaran yang berjalan terjadwal, aman bila dijalankan dua kali atau oleh dua proses sekaligus.

## Konsep inti

- Job terjadwal: kode yang berjalan menurut waktu (cron, scheduler dalam proses, atau worker terpisah).
- **Idempotency**: menjalankan job dua kali tidak menghasilkan efek ganda (misal dua pengingat untuk hal yang sama).
- Dua worker bersamaan (setelah scale, atau restart tumpang tindih) bisa memproses baris yang sama. Solusi: kunci baris (`SELECT ... FOR UPDATE SKIP LOCKED`), kolom status yang diubah atomik, atau advisory lock.
- Zona waktu: simpan UTC (`timestamptz`), tampilkan lokal. Bug "jam 7 pagi" biasanya bug zona waktu.
- Kegagalan: retry dengan batas, catat error, jangan kirim ulang yang sudah terkirim.
- Pengiriman nyata ke luar (email/pesan) hanya ke dirimu sendiri; di latihan cukup tulis ke tabel `notifications` atau log.

## Langkah

1. Migrasi: tabel `reminders` (application_id, due_at timestamptz, status, sent_at, attempts).
2. Endpoint untuk membuat pengingat pada sebuah lamaran (tetap dilindungi autentikasi dan kepemilikan).
3. Worker (`src/worker.ts`) yang tiap interval mengambil pengingat jatuh tempo, "mengirim" (catat ke tabel/log), dan menandainya terkirim.
4. Pastikan pengambilan baris atomik: pilih satu mekanisme di atas dan tulis alasannya.
5. Jalankan **dua worker bersamaan** terhadap data yang sama dan buktikan tidak ada pengingat ganda.
6. Simulasikan kegagalan di tengah pengiriman dan tunjukkan perilaku retry-mu.
7. Tes: pengingat jatuh tempo diproses sekali; yang belum jatuh tempo tidak disentuh; zona waktu benar.

## Petunjuk

Pertanyaan penuntun:
- Apa yang terjadi bila worker mati setelah "mengirim" tetapi sebelum menandai terkirim? Apa kompromi yang kamu pilih (at-least-once vs at-most-once)?
- Bagaimana dua worker bisa memilih baris berbeda tanpa saling menunggu?
- Di mana kamu menyimpan jam, dan bagaimana kamu menguji dengan waktu palsu?

Contoh pola klaim atomik pada domain lain:

```sql
WITH picked AS (
  SELECT id FROM jobs
  WHERE status = 'pending' AND run_at <= now()
  ORDER BY run_at
  LIMIT 10
  FOR UPDATE SKIP LOCKED
)
UPDATE jobs SET status = 'running'
FROM picked WHERE jobs.id = picked.id
RETURNING jobs.*;
```

## Tugas

Selesaikan langkah 1 sampai 7 dan tulis `docs/job-terjadwal.md`: diagram alur, jaminan pengiriman yang dipilih, dan skenario kegagalan.

## Lulus jika

- [ ] Worker memproses pengingat jatuh tempo dan mencatat `sent_at`.
- [ ] Dengan dua worker bersamaan, tidak ada pengingat yang diproses dua kali (buktikan dengan hitungan).
- [ ] Menjalankan worker berulang kali tidak menggandakan hasil (idempotent).
- [ ] Waktu disimpan sebagai `timestamptz` dan diuji lintas zona waktu.
- [ ] Ada penanganan gagal dengan batas retry.
- [ ] Kamu bisa menjelaskan at-least-once vs at-most-once untuk job-mu.

## Kuis mandiri

1. Apa itu idempotency dan kenapa penting untuk job?
2. Apa fungsi `SKIP LOCKED`?
3. Apa risiko menyimpan waktu tanpa zona?
4. Jika worker mati setelah mengirim sebelum menandai, apa yang terjadi saat restart?
5. Kapan scheduler dalam proses aplikasi tidak cukup dan kamu butuh worker terpisah?

## Referensi

- PostgreSQL (Explicit Locking, `SELECT` locking clause): https://www.postgresql.org/docs/
- Node.js: https://nodejs.org/en/docs
