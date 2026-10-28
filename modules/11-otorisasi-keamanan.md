# Modul 11: Otorisasi dan keamanan

Perkiraan: 5 jam. Prasyarat: Modul 10.

## Tujuan

Memastikan setiap user hanya bisa mengakses datanya sendiri, dan menambah lapisan keamanan dasar: rate limit, header keamanan, CORS, audit dependensi.

## Konsep inti

- **IDOR** (Insecure Direct Object Reference): user A mengakses `/applications/42` milik user B hanya dengan menebak id. Salah satu kelemahan API yang paling umum.
- Kepemilikan dicek di **query** (misal `WHERE id = $1 AND user_id = $2`), bukan hanya di middleware.
- Respons untuk sumber milik orang lain: 404 (agar tidak membocorkan keberadaan) atau 403; pilih satu dan konsisten.
- Rate limiting menahan brute force login dan penyalahgunaan.
- Header keamanan (helmet) dan CORS yang ketat (allow-list origin, bukan `*` untuk API ber-kredensial).
- Batas ukuran body, dan jangan mengembalikan field internal.
- `npm audit` dan menjaga dependensi tetap diperbarui.

## Langkah

1. Pastikan semua query `applications` memfilter `user_id` dari token, tidak pernah dari body.
2. Tulis tes IDOR: dua user, user A tidak bisa membaca, mengubah, atau menghapus data user B (ketiga metode).
3. Pasang rate limit pada `/auth/login` (dan register) dan buktikan 429 setelah sejumlah percobaan.
4. Pasang helmet dan atur CORS dengan allow-list dari environment.
5. Batasi ukuran body JSON.
6. Jalankan `npm audit`, baca hasilnya, perbaiki yang relevan, dan catat yang ditunda beserta alasannya.
7. Tinjau checklist OWASP API Security Top 10 terhadap proyekmu; tulis mana yang sudah ditangani dan mana belum.

## Petunjuk

Pertanyaan penuntun:
- Di mana tepatnya `user_id` harus masuk agar mustahil terlewat? Bagaimana struktur kode yang membuatnya sulit lupa?
- Rate limit berdasarkan apa (IP, email, keduanya) dan apa kelemahan masing-masing?
- Jika di belakang reverse proxy, bagaimana aplikasi tahu IP asli klien? (Cek dokumentasi `trust proxy`.)
- Kenapa `Access-Control-Allow-Origin: *` bermasalah untuk API yang memakai kredensial?

Contoh pola kepemilikan pada domain lain:

```ts
const { rows } = await pool.query(
  'SELECT * FROM notes WHERE id = $1 AND owner_id = $2',
  [req.params.id, req.user.id],
);
if (rows.length === 0) return res.status(404).json({ error: { code: 'NOT_FOUND' } });
```

## Tugas

Selesaikan langkah 1 sampai 7 dan tulis `docs/keamanan.md` berisi daftar risiko, status, dan bukti (nama tes atau hasil perintah).

## Lulus jika

- [ ] Ada tes yang membuktikan user A tidak bisa baca, ubah, atau hapus data user B.
- [ ] Brute force login menghasilkan 429 (ditunjukkan lewat tes atau percobaan).
- [ ] Respons membawa header keamanan dan CORS memakai allow-list.
- [ ] `npm audit` sudah dijalankan dan hasilnya ditindaklanjuti atau dicatat.
- [ ] Tidak ada field sensitif (hash password) di respons mana pun.
- [ ] Kamu bisa menjelaskan IDOR dan cara mencegahnya dengan kata-katamu sendiri.

## Kuis mandiri

1. Beda autentikasi dan otorisasi dengan contoh dari proyekmu.
2. Apa itu IDOR dan kenapa id berurutan membuatnya mudah dieksploitasi?
3. Kenapa rate limit di aplikasi saja kadang tidak cukup?
4. Apa yang dilindungi CORS, dan apa yang tidak (misal dari `curl`)?
5. Apa risiko mengabaikan `npm audit`, dan kapan boleh menundanya?

## Referensi

- OWASP API Security Project: https://owasp.org/
- OWASP Cheat Sheet Series: https://cheatsheetseries.owasp.org/
- Express (security best practices): https://expressjs.com/
