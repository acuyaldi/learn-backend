# Modul 9: Pengujian

Perkiraan: 6 jam. Prasyarat: Modul 8.

## Tujuan

Menulis tes otomatis untuk API (unit dan integrasi) dengan Vitest + Supertest, memakai database tes yang terisolasi.

## Konsep inti

- Tes unit: logika murni (validasi, fungsi helper). Tes integrasi: request HTTP nyata ke aplikasi dan database nyata.
- Database tes **terpisah** dari database dev. Tes tidak boleh pernah menyentuh data dev atau produksi.
- Isolasi: tiap tes mulai dari keadaan bersih (truncate tabel atau transaksi yang di-rollback).
- Supertest memanggil `app` tanpa membuka port; karena itu `app.ts` dipisah dari `server.ts` (Modul 6).
- Tes yang bagus gagal karena alasan yang benar. Buktikan dengan sengaja merusak kode.
- Cakupan (coverage) adalah alat bantu, bukan tujuan.

## Langkah

1. Pasang `vitest` dan `supertest` (beserta tipenya).
2. Buat database kedua (misal `tracker_test`) dan konfigurasi `DATABASE_URL` khusus tes. Tambahkan **penjaga**: tes menolak berjalan bila nama database tidak berakhiran `_test`.
3. Setup: jalankan migrasi pada database tes sebelum tes; bersihkan tabel antar tes.
4. Tulis minimal 12 tes, termasuk:
   - jalur sukses tiap endpoint CRUD,
   - validasi gagal (400/422),
   - id tidak ada (404),
   - pelanggaran unique (409),
   - paginasi dan filter,
   - satu tes unit untuk fungsi murni.
5. **Mutation check manual:** sengaja masukkan bug (misal hapus `WHERE` pada `DELETE`, atau kembalikan 200 untuk id tidak ada), jalankan tes, dan pastikan ada tes yang gagal. Catat bug apa dan tes mana yang menangkapnya. Kembalikan kodenya.
6. Skrip `npm test` dan `npm run test:coverage`.

## Petunjuk

Contoh pada domain lain:

```ts
import request from 'supertest';
import { describe, it, expect, beforeEach } from 'vitest';
import { app } from '../src/app';

describe('GET /books/:id', () => {
  beforeEach(async () => { /* bersihkan tabel */ });

  it('mengembalikan 404 bila tidak ada', async () => {
    const res = await request(app).get('/books/999999');
    expect(res.status).toBe(404);
  });
});
```

Pertanyaan penuntun:
- Bagaimana menyiapkan data awal untuk satu tes tanpa tergantung tes lain?
- Apa yang terjadi jika urutan tes diacak? Tes harus tetap lulus.
- Bagaimana kamu memastikan tes tidak pernah berjalan terhadap database dev?
- Cek dokumentasi Vitest versi yang terpasang untuk konfigurasi.

## Tugas

Selesaikan langkah 1 sampai 6 dan tulis `docs/pengujian.md`: strategi isolasi, daftar bug yang sengaja disisipkan, dan tes yang menangkapnya.

## Lulus jika

- [ ] `npm test` lulus dengan minimal 12 tes.
- [ ] Tes memakai database terpisah dan ada penjaga yang menolak jalan di database selain `_test`.
- [ ] Tes bisa dijalankan berulang dan dalam urutan acak tanpa gagal.
- [ ] Minimal 3 bug yang sengaja disisipkan tertangkap oleh tes (dengan catatan).
- [ ] Ada tes untuk jalur gagal, bukan hanya jalur sukses.
- [ ] Kamu bisa menjelaskan beda tes unit dan integrasi, dan kapan memilih masing-masing.

## Kuis mandiri

1. Kenapa tes tidak boleh memakai database dev?
2. Apa risikonya jika tes saling bergantung pada urutan?
3. Apa yang kamu buktikan dengan menyisipkan bug sengaja?
4. Kenapa coverage 100% tidak menjamin tidak ada bug?
5. Apa kelemahan me-mock database di semua tes dibanding memakai database tes nyata?

## Referensi

- Vitest: https://vitest.dev/
- Supertest: https://github.com/ladjs/supertest
