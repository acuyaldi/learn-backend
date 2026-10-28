# Modul 10: Autentikasi

Perkiraan: 6 jam. Prasyarat: Modul 9.

## Tujuan

Menambah registrasi dan login dengan password yang di-hash dan token (JWT), serta melindungi endpoint.

## Konsep inti

- Autentikasi = siapa kamu. Otorisasi = apa yang boleh kamu lakukan (Modul 11).
- Password **tidak pernah** disimpan mentah atau di-hash dengan hash cepat biasa (SHA-256). Pakai fungsi hash password yang lambat dan ber-salt: argon2 atau bcrypt.
- JWT: token bertanda tangan berisi klaim (misal user id, waktu kedaluwarsa). Siapa pun bisa **membaca** isinya; tanda tangan hanya mencegah **pengubahan**. Jangan taruh rahasia di dalamnya.
- Access token berumur pendek; refresh token (opsional di modul ini) berumur lebih panjang dan bisa dicabut.
- Pesan error login yang seragam ("email atau password salah") agar tidak membocorkan email yang terdaftar.
- Rahasia penandatangan JWT ada di environment, bukan di kode.

## Langkah

1. Migrasi baru: tabel `users` (email unik, password_hash, created_at) dan hubungkan `applications` ke `user_id`.
2. `POST /auth/register`: validasi (zod), hash password, simpan.
3. `POST /auth/login`: verifikasi, kembalikan access token berumur pendek.
4. Middleware autentikasi: baca header `Authorization: Bearer ...`, verifikasi, taruh user di `req`.
5. Lindungi semua endpoint `applications`: tanpa token valid, kembalikan 401.
6. Tes (Modul 9): register, login sukses, login gagal, akses tanpa token, token kedaluwarsa, token yang diubah.
7. Pastikan respons dan log tidak pernah memuat password atau hash.

## Petunjuk

Pertanyaan penuntun:
- Apa yang bocor bila tabel `users` dicuri, dan bagaimana hash yang lambat membantu?
- Apa yang terjadi bila kamu mengubah satu karakter di payload JWT?
- Algoritma penandatanganan apa yang kamu pilih dan kenapa? Pastikan verifikasi **menetapkan** algoritma yang diterima, bukan mempercayai header token.
- Mengapa pesan error login tidak boleh membedakan "email tidak ada" dan "password salah"?
- Pilih library dan cek dokumentasi versi yang terpasang untuk API tepatnya.

Contoh pola pada domain lain:

```ts
// pseudocode alur, bukan implementasi lengkap
const ok = await verifyHash(storedHash, plainPassword);
if (!ok) return res.status(401).json({ error: { code: 'INVALID_CREDENTIALS' } });
const token = sign({ sub: String(user.id) }, SECRET, { expiresIn: '15m' });
```

## Tugas

Selesaikan langkah 1 sampai 7 dan tulis `docs/auth.md`: alur register/login (diagram teks), pilihan algoritma, umur token, dan ancaman yang belum ditangani.

## Lulus jika

- [ ] Password disimpan hanya sebagai hash argon2/bcrypt; kamu bisa menunjukkan barisnya di database.
- [ ] Login sukses mengembalikan token; endpoint `applications` mengembalikan 401 tanpa token valid atau dengan token yang diubah/kedaluwarsa.
- [ ] Pesan login gagal seragam untuk email tak ada dan password salah.
- [ ] Rahasia JWT ada di environment, bukan di Git.
- [ ] Ada tes otomatis untuk skenario langkah 6.
- [ ] Kamu bisa menjelaskan kenapa JWT bisa dibaca siapa saja dan apa implikasinya.

## Kuis mandiri

1. Kenapa password di-hash, bukan dienkripsi?
2. Apa guna salt dan kenapa hash password sengaja lambat?
3. Apa yang dijamin tanda tangan JWT, dan apa yang tidak?
4. Apa trade-off token berumur pendek vs panjang?
5. Apa itu 401 dan 403, dan kapan masing-masing dipakai?

## Referensi

- OWASP Cheat Sheet Series (Password Storage, Authentication): https://cheatsheetseries.owasp.org/
- Node.js: https://nodejs.org/en/docs
