# KiloCode — Task 001: Backend Foundation & First-Run Authentication

**Role:** Executor / Backend Engineer  
**Milestone:** M0 — Foundation  
**Branch:** `feat/001-backend-foundation`  
**Owner area:** `apps/api/` saja, kecuali file root yang memang diperlukan untuk menjalankan backend. Jangan mengubah `apps/web/`.

## Konteks wajib

Baca `docs/PRD.md` dan `docs/API_CONTRACT.md` sampai selesai sebelum coding. Dokumen tersebut adalah sumber kebenaran. Bila ada konflik atau requirement yang tidak jelas, jangan membuat keputusan produk sendiri; catat pertanyaan di ringkasan hasil.

Produk adalah buku kas web untuk satu pemilik bisnis, satu instalasi, IDR saja. Backend baseline: Node.js, TypeScript, Express, Prisma, PostgreSQL, Zod. Testing: Vitest dan Supertest.

## Tujuan task

Bangun fondasi backend yang dapat dijalankan, memiliki koneksi database dan schema awal, health check, serta alur setup owner pertama kali dan login/logout/me berbasis sesi yang aman.

## Scope implementasi

1. Buat struktur backend terpisah di `apps/api/`.
2. Siapkan TypeScript, Express, scripts dev/build/start/test/lint/typecheck, dan konfigurasi yang jelas.
3. Siapkan Prisma dengan PostgreSQL dan migration awal.
4. Buat model database awal: `User`, `Session`, `Account`, `Category`, `Transaction`, beserta enum dan relasi yang sesuai PRD.
5. Pastikan satu instalasi hanya dapat memiliki satu owner. Gunakan constraint database yang benar, bukan hanya pengecekan di UI. Setup harus aman terhadap dua request bersamaan.
6. Implementasikan endpoint:
   - `GET /api/health`
   - `GET /api/auth/setup-status`
   - `POST /api/auth/setup`
   - `POST /api/auth/login`
   - `POST /api/auth/logout`
   - `GET /api/auth/me`
7. Gunakan Zod untuk validasi request.
8. Hash password dengan Argon2id atau solusi setara yang kuat dan terpelihara.
9. Buat token sesi acak menggunakan cryptographic random generator. Simpan hash token pada database, jangan simpan token mentah.
10. Gunakan cookie sesi `HttpOnly`, `SameSite=Lax`, `Path=/`, dan `Secure` pada production. Masa berlaku default 7 hari dan dapat dikonfigurasi.
11. Tambahkan rate limiting login/setup, pemeriksaan Origin untuk request yang mengubah state, dan CORS allowlist dari environment variable. Jangan gunakan wildcard jika credentials aktif.
12. Tambahkan centralized error handler; jangan membocorkan stack trace, password, token, atau detail database kepada client.
13. Sediakan `.env.example` tanpa secret dan petunjuk menjalankan PostgreSQL, migration, test, serta server.
14. Tambahkan unit/integration tests untuk aturan setup satu kali, login valid/tidak valid, me tanpa sesi/dengan sesi, logout, rate limit atau middleware relevan, dan health/database readiness.

## Model data yang diharapkan

- `User`: ID, email unik (dinormalisasi lowercase), password hash, waktu dibuat/diubah. Constraint memastikan maksimum satu owner per instalasi.
- `Session`: ID, user ID, token hash unik, expiry, waktu dibuat.
- `Account`: ID, name, type `CASH|BANK|E_WALLET`, opening balance `BIGINT`, active, timestamps.
- `Category`: ID, name, type `INCOME|EXPENSE`, active, timestamps.
- `Transaction`: ID, type `INCOME|EXPENSE|TRANSFER`, amount `BIGINT`, transaction date `DATE`, account ID, destination account ID nullable, category ID nullable, description nullable, status `POSTED|VOIDED`, void reason nullable, timestamps.

Tambahkan constraint/index yang masuk akal. Amount harus positif. Untuk relasi yang tidak dapat diekspresikan penuh oleh Prisma schema, gunakan SQL migration dan/atau validasi service. Dokumentasikan batasan tersebut. Jangan mengimplementasikan CRUD transaksi/akun/kategori pada task ini kecuali dibutuhkan sekadar migration schema.

## Batasan penting

- Jangan membuat endpoint keuangan sebelum task berikutnya.
- Jangan membuat frontend atau mengubah `apps/web/`.
- Jangan menyimpan nominal Rupiah sebagai Float/Double/JavaScript floating point.
- API masa depan harus mengirim nominal `BIGINT` sebagai string integer JSON.
- Jangan hard-delete transaksi POSTED pada desain domain.
- Jangan menaruh credential sungguhan di repository.
- Jangan menambahkan Docker/deployment production yang tidak diminta. Compose PostgreSQL untuk development boleh disiapkan jika memang mempermudah, tetapi dokumentasikan.
- Jangan menambah library besar tanpa alasan.

## Acceptance criteria

- Backend dapat di-install dan dijalankan mengikuti README/petunjuk di `apps/api/`.
- `GET /api/health` mengembalikan status API dan database secara aman.
- Setup owner berhasil hanya satu kali; request setup selanjutnya mendapat HTTP 409.
- Dua request setup yang bersamaan tidak dapat membuat dua owner.
- Login benar membuat sesi; login salah memberi pesan generik dan tidak membocorkan apakah email terdaftar.
- `/api/auth/me` hanya berhasil dengan cookie sesi valid.
- Logout menghapus sesi server dan cookie.
- Password dan token sesi tidak pernah disimpan plaintext.
- CORS/origin/cookie sesuai konfigurasi environment.
- Migration dapat diterapkan ke database kosong.
- Unit/integration tests, lint, dan typecheck lulus.
- Tidak ada perubahan di `apps/web/`.

## Format laporan setelah selesai

Berikan:
1. Ringkasan implementasi.
2. Daftar file utama yang dibuat/diubah.
3. Migration/schema dan cara menjalankannya.
4. Environment variables yang diperlukan.
5. Perintah test/lint/typecheck beserta hasil aktual.
6. Endpoint yang tersedia dan contoh request singkat.
7. Risiko, keterbatasan, dan keputusan yang perlu persetujuan Head Manager.
8. Commit hash atau diff summary.

Jangan mengklaim test lulus jika belum benar-benar dijalankan. Hentikan pekerjaan setelah Task 001; jangan mengerjakan milestone berikutnya tanpa instruksi.
