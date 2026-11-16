# Product Requirements Document (PRD)

## Aplikasi Catatan Keuangan Bisnis — MVP

- Versi: 0.1
- Status: Draft untuk persetujuan sebelum implementasi
- Tanggal: 16 November 2026
- Nama kerja: Business Finance Ledger

## 1. Ringkasan produk

Aplikasi web untuk membantu satu pemilik bisnis mencatat dan memantau arus kas bisnis dalam Rupiah (IDR). Pengguna dapat mengelola akun kas/rekening, mencatat pemasukan, pengeluaran, dan transfer antar-akun, melihat ringkasan saldo, serta membuat laporan berdasarkan periode.

MVP ini adalah **buku kas / pencatatan arus kas**, bukan sistem akuntansi penuh. MVP tidak menghasilkan laporan neraca, jurnal double-entry, laporan pajak, payroll, atau laporan akuntansi yang telah diverifikasi sesuai standar tertentu.

## 2. Keputusan dan asumsi produk

| Area | Keputusan MVP |
|---|---|
| Pengguna | Satu pemilik (single-user), tanpa role staf |
| Cakupan bisnis | Satu bisnis per instalasi aplikasi |
| Mata uang | IDR saja; tidak ada pecahan desimal |
| Platform | Web responsif untuk desktop dan browser mobile |
| Bahasa UI | Bahasa Indonesia |
| Zona waktu tampilan | Asia/Jakarta |
| Model pencatatan | Cash-basis sederhana: kas masuk, kas keluar, transfer |
| Autentikasi | Login wajib; pendaftaran pemilik hanya tersedia saat setup pertama |
| Penyimpanan | PostgreSQL |

Asumsi deployment awal: satu instalasi untuk satu bisnis. Multi-user, multi-bisnis, dan multi-tenant tidak termasuk MVP.

## 3. Tujuan dan ukuran keberhasilan

### Tujuan
1. Pengguna dapat mencatat transaksi keuangan tanpa spreadsheet manual.
2. Saldo setiap akun dan total saldo dapat dihitung konsisten dari saldo awal dan transaksi.
3. Pengguna dapat membedakan pemasukan, pengeluaran, dan transfer internal.
4. Pengguna dapat melihat ringkasan dan laporan arus kas berdasarkan rentang tanggal.

### Kriteria keberhasilan MVP
- Pengguna dapat menyelesaikan setup awal dan login.
- Pengguna dapat membuat minimal satu akun dengan saldo awal.
- Pengguna dapat membuat, melihat, memfilter, dan membatalkan transaksi.
- Pencatatan transfer mengurangi saldo akun sumber dan menambah saldo akun tujuan tanpa mengubah total pemasukan/pengeluaran bisnis.
- Dashboard dan laporan menampilkan angka yang sama untuk periode dan data yang sama.
- Pengujian otomatis memverifikasi aturan perhitungan saldo dan transaksi.

## 4. Pengguna sasaran dan user stories

**Persona:** pemilik satu bisnis yang perlu mengetahui uang masuk, uang keluar, dan saldo kas/rekening.

- Sebagai pemilik, saya ingin login agar data keuangan tidak dapat dilihat orang lain.
- Saya ingin membuat beberapa akun seperti Kas, Bank, dan E-Wallet agar uang bisnis dapat dipantau terpisah.
- Saya ingin memasukkan saldo awal setiap akun agar saldo aplikasi sesuai kondisi saat mulai digunakan.
- Saya ingin mencatat pemasukan dan memilih kategorinya.
- Saya ingin mencatat pengeluaran dan memilih kategorinya.
- Saya ingin memindahkan uang antar-akun tanpa menganggap transfer sebagai pemasukan atau pengeluaran.
- Saya ingin mencari dan memfilter transaksi berdasarkan tanggal, jenis, akun, dan kategori.
- Saya ingin melihat total saldo, pemasukan, pengeluaran, dan arus kas bersih.
- Saya ingin melihat laporan arus kas untuk rentang tanggal tertentu.
- Saya ingin membatalkan catatan transaksi yang salah tanpa menghapus jejaknya secara permanen.

## 5. Ruang lingkup MVP

### 5.1 Autentikasi dan setup awal
- Setup pertama membuat satu akun pemilik.
- Endpoint setup harus tertutup permanen setelah akun pemilik berhasil dibuat.
- Login menggunakan email dan password.
- Logout mengakhiri sesi.
- Halaman aplikasi yang memuat data keuangan wajib terlindungi autentikasi.
- Tidak ada fitur registrasi publik, undangan, atau reset password melalui email pada MVP.

### 5.2 Manajemen akun keuangan
- Buat, lihat, dan ubah akun.
- Jenis akun: CASH, BANK, E_WALLET.
- Setiap akun memiliki nama, saldo awal, status aktif/nonaktif.
- Saldo awal dan nilai uang disimpan sebagai bilangan bulat Rupiah.
- Akun yang telah memiliki transaksi tidak boleh dihapus secara permanen.
- Akun nonaktif tidak dapat dipilih untuk transaksi baru, tetapi riwayat dan saldonya tetap dihitung.

### 5.3 Kategori
- Kategori dipisahkan menjadi INCOME dan EXPENSE.
- Pengguna dapat membuat, melihat, mengubah nama, dan menonaktifkan kategori.
- Kategori INCOME hanya dapat digunakan untuk pemasukan.
- Kategori EXPENSE hanya dapat digunakan untuk pengeluaran.
- Transfer tidak memakai kategori pemasukan/pengeluaran.
- Kategori yang sudah dipakai transaksi tidak dihapus permanen; gunakan status aktif/nonaktif.

Kategori awal yang disarankan untuk seed atau setup: Penjualan, Pendapatan Lain, Pembelian Barang, Operasional, Transportasi, Gaji, dan Biaya Lain. Pengguna dapat menambah atau mengubahnya.

### 5.4 Transaksi
Jenis transaksi:
- **INCOME:** uang masuk ke satu akun; wajib memiliki kategori INCOME.
- **EXPENSE:** uang keluar dari satu akun; wajib memiliki kategori EXPENSE.
- **TRANSFER:** uang dipindahkan dari satu akun ke akun lain; wajib memiliki akun sumber dan tujuan yang berbeda; tidak memakai kategori.

Field transaksi:
- ID
- Jenis transaksi
- Jumlah (integer Rupiah positif)
- Tanggal transaksi
- Akun sumber/akun terkait
- Akun tujuan (khusus transfer)
- Kategori (khusus income/expense)
- Deskripsi/catatan (opsional)
- Status POSTED atau VOIDED
- Waktu pembuatan dan pembaruan
- Alasan pembatalan (khusus VOIDED)

Aturan:
- Jumlah wajib lebih besar dari nol.
- Jumlah tidak boleh berupa pecahan desimal.
- Transaksi tidak boleh menggunakan akun nonaktif.
- Akun sumber dan tujuan transfer tidak boleh sama.
- Transaksi POSTED tidak dihapus permanen. Kesalahan diperbaiki dengan membatalkan transaksi dan membuat transaksi pengganti.
- Transaksi VOIDED tidak ikut perhitungan saldo, dashboard, atau laporan.
- Pembatalan wajib menyimpan alasan dan waktu pembatalan.
- Pembuatan transfer harus atomik: tidak boleh hanya debit atau hanya kredit.

### 5.5 Dashboard
Menampilkan:
- Total saldo seluruh akun (termasuk akun yang dinonaktifkan agar saldo historis tidak hilang).
- Total pemasukan pada periode terpilih.
- Total pengeluaran pada periode terpilih.
- Arus kas bersih periode tersebut (pemasukan dikurangi pengeluaran).
- Saldo per akun.
- Daftar transaksi terbaru.
- Grafik arus kas sesuai periode.

Default periode dashboard: bulan berjalan berdasarkan tanggal lokal Asia/Jakarta. Transfer tidak masuk total pemasukan atau pengeluaran.

### 5.6 Riwayat transaksi
- Daftar transaksi dengan pagination.
- Filter tanggal mulai/akhir, jenis transaksi, akun, kategori, dan status.
- Pencarian teks pada deskripsi.
- Detail transaksi.
- Pembatalan transaksi POSTED dengan alasan.
- Status VOIDED tetap dapat dilihat.

### 5.7 Laporan
- Laporan arus kas berdasarkan rentang tanggal.
- Ringkasan pemasukan, pengeluaran, dan arus kas bersih.
- Rincian pemasukan per kategori.
- Rincian pengeluaran per kategori.
- Transfer ditampilkan terpisah atau dapat ditelusuri, tetapi tidak dijumlahkan sebagai income/expense.
- Dashboard dan laporan harus menggunakan aturan perhitungan backend yang sama.

### 5.8 Ekspor
Ekspor CSV sederhana untuk daftar transaksi sesuai filter aktif. Ekspor tidak mengubah data. Format tanggal dan nominal harus konsisten dan nominal tetap berupa angka Rupiah utuh.

## 6. Aturan perhitungan keuangan

Untuk akun tertentu:

`Saldo akun = saldo awal + pemasukan - pengeluaran - transfer keluar + transfer masuk`

Untuk satu periode:

`Arus kas bersih = total pemasukan - total pengeluaran`

Ketentuan penting:
- Transfer internal tidak memengaruhi total pemasukan, total pengeluaran, atau arus kas bersih bisnis.
- Total saldo bisnis adalah jumlah saldo semua akun yang tersimpan, termasuk akun nonaktif.
- Hanya transaksi POSTED yang dihitung.
- Saldo akun tidak boleh menjadi tidak konsisten akibat proses transfer yang gagal sebagian.
- Perhitungan uang menggunakan integer/bigint, bukan floating point.

## 7. Model data awal

Model konseptual:

- `User`: satu pemilik, email unik, password hash.
- `Session`: sesi login, hash token, waktu kedaluwarsa, relasi ke User.
- `Account`: nama, jenis, saldo awal BIGINT, status.
- `Category`: nama, tipe INCOME/EXPENSE, status.
- `Transaction`: tipe, jumlah BIGINT, tanggal transaksi, account sumber/terkait, account tujuan opsional, kategori opsional, deskripsi, status, alasan pembatalan, timestamp.

Relasi dan validasi:
- Satu User maksimum untuk satu instalasi.
- Setiap Transaction memiliki satu akun terkait/sumber.
- Transfer memiliki satu akun tujuan dan tidak boleh memiliki kategori.
- INCOME/EXPENSE wajib memiliki kategori sesuai tipe.
- Foreign key harus menjaga integritas relasi.
- Nominal transaksi harus positif.

## 8. Hal di luar MVP (non-goals)

- Multi-user, role, permission staf, dan multi-tenant.
- Multi-currency atau konversi valuta.
- Double-entry bookkeeping, jurnal umum, neraca, buku besar, dan laporan laba-rugi akuntansi formal.
- Piutang, utang, invoice, dan pengingat pembayaran.
- Inventaris, HPP, payroll, dan manajemen karyawan.
- Pajak/e-Faktur dan integrasi perpajakan.
- Integrasi mutasi bank, payment gateway, atau open banking.
- Aplikasi native Android/iOS.
- Sinkronisasi offline.

## 9. Persyaratan non-fungsional

### Keamanan
- Password disimpan menggunakan password hashing yang sesuai (Argon2id direkomendasikan), bukan plaintext.
- Sesi menggunakan token acak yang tidak disimpan plaintext di database.
- Cookie sesi `HttpOnly`, `SameSite=Lax` atau lebih ketat, dan `Secure` pada production.
- Validasi input server-side dengan schema validation.
- Batasi percobaan login (rate limit).
- CORS hanya mengizinkan origin yang dikonfigurasi; jangan memakai wildcard dengan credentials.
- Cegah CSRF dengan kombinasi SameSite dan pemeriksaan Origin untuk request yang mengubah data.
- Jangan mencatat password, token sesi, atau rahasia ke log.
- Semua endpoint data keuangan wajib membutuhkan sesi valid.

### Integritas data
- Gunakan PostgreSQL transaction untuk operasi yang melibatkan beberapa perubahan.
- Gunakan foreign key, unique constraint, check constraint, dan index yang relevan.
- Tidak ada hard-delete untuk transaksi keuangan yang telah diposting.
- Semua nominal disimpan sebagai integer Rupiah (`BIGINT`) dan dikirim melalui API sebagai string desimal agar aman dari batas presisi JavaScript.

### Kualitas dan operasional
- TypeScript untuk frontend dan backend.
- Linting dan formatting konsisten.
- Unit test untuk formula saldo dan validasi transaksi.
- Integration test untuk endpoint dan operasi database penting.
- Konfigurasi rahasia melalui environment variables; sediakan `.env.example` tanpa secret.
- Sediakan instruksi setup lokal, migrasi, testing, dan menjalankan aplikasi.
- Tampilkan error yang dapat dipahami pengguna tanpa membocorkan detail internal.

## 10. Stack teknologi yang disepakati sebagai baseline

| Lapisan | Teknologi |
|---|---|
| Runtime | Node.js LTS yang kompatibel dengan dependensi |
| Bahasa | TypeScript |
| Backend | Express |
| ORM | Prisma |
| Database | PostgreSQL |
| Validasi | Zod |
| Frontend | React + Vite + TypeScript |
| Styling | Tailwind CSS |
| Routing UI | React Router |
| Grafik | Recharts |
| Ikon | Lucide React |
| Testing backend | Vitest + Supertest |
| Testing frontend | Vitest + React Testing Library |

Versi paket harus dipilih sebagai versi stabil yang kompatibel saat implementasi; jangan menebak atau mem-pin versi lama tanpa alasan.

## 11. UX dan halaman MVP

- `/setup`: setup pemilik pertama kali (hanya sebelum owner dibuat).
- `/login`: login.
- `/dashboard`: ringkasan saldo dan arus kas.
- `/transactions`: riwayat, filter, tambah, detail, dan batalkan transaksi.
- `/accounts`: daftar dan pengelolaan akun.
- `/categories`: pengelolaan kategori.
- `/reports`: laporan periode dan ekspor CSV.

UI harus responsif, berbahasa Indonesia, memiliki state loading/empty/error, kontras memadai, label form jelas, dan konfirmasi sebelum membatalkan transaksi.

## 12. Milestone pengembangan

1. **M0 — Foundation:** repository, dokumentasi, standar coding, backend bootstrap/auth, frontend shell/design system.
2. **M1 — Core data:** akun, kategori, dan validasi.
3. **M2 — Transactions:** income, expense, transfer, void, daftar dan filter.
4. **M3 — Dashboard & reports:** agregasi, grafik, laporan dan CSV.
5. **M4 — Hardening:** keamanan, integration test, usability, dokumentasi deployment dan backup/restore.

## 13. Acceptance criteria MVP

- Setup awal hanya dapat berhasil sekali.
- Pengguna yang belum login tidak dapat mengakses data keuangan.
- Saldo awal akun diterapkan tepat.
- Pemasukan menambah saldo akun dan kategori wajib bertipe INCOME.
- Pengeluaran mengurangi saldo akun dan kategori wajib bertipe EXPENSE.
- Transfer mengurangi akun sumber dan menambah akun tujuan dengan total saldo bisnis tetap.
- Transfer tidak memengaruhi angka pemasukan/pengeluaran/laporan arus kas.
- Nominal nol, negatif, pecahan, atau format tidak valid ditolak.
- Akun sumber dan tujuan transfer yang sama ditolak.
- Transaksi VOIDED tidak lagi memengaruhi saldo atau laporan, tetapi jejaknya tetap tersedia.
- Filter periode menggunakan tanggal transaksi, bukan waktu pembuatan record.
- Dashboard dan laporan menghasilkan nilai yang konsisten.
- Seluruh acceptance criteria penting ditopang automated tests.

## 14. Risiko dan keputusan yang masih terbuka

- **Backup dan restore:** strategi operasional perlu diputuskan sebelum production.
- **Deployment:** target hosting, domain, reverse proxy, dan TLS belum ditetapkan.
- **Reset password:** belum termasuk MVP; perlu prosedur admin yang aman jika dibutuhkan.
- **Audit trail perubahan non-transaksi:** dapat ditambahkan setelah kebutuhan operasional jelas.

Jangan memperluas scope karena asumsi. Catat pertanyaan atau kebutuhan baru di `TASKS.md` dan minta persetujuan Head Manager sebelum mengubah PRD.

## 11. Model kerja AI dalam pengembangan

Pembagian tanggung jawab pengembangan proyek ini ditetapkan sebagai berikut:

- **ChatGPT — Head Manager / Technical Lead:** menyusun dan menjaga PRD, arsitektur, prioritas task, acceptance criteria, review desain, code review, security review, dan keputusan freeze.
- **Claude Website — Visual Design Consultant / Prompt Generator:** menganalisis kebutuhan UI/UX dan menghasilkan design brief serta prompt implementasi visual untuk KiloCode. Claude tidak memiliki tanggung jawab mengubah repository, menulis kode aplikasi, atau membuat commit.
- **KiloCode — Sole Executor:** satu-satunya agen yang mengubah repository. KiloCode mengimplementasikan backend, database, frontend, komponen visual, integrasi, dan automated tests berdasarkan dokumen yang telah disetujui.

Alur kerja: PLAN → DESIGN PROMPT (Claude) → DESIGN REVIEW (ChatGPT) → CODE (KiloCode) → TEST → REVIEW (ChatGPT) → FREEZE. Prompt desain dari Claude merupakan arahan presentasi dan interaksi, bukan sumber kebenaran business rules. Jika terjadi konflik, PRD dan API contract harus diikuti dan konflik dilaporkan kepada Head Manager.
