# KiloCode — Task 002: Frontend UI Foundation & MVP Screens

**Role:** Sole Implementation Agent  
**Milestone:** M0 — Foundation  
**Branch:** `feat/002-frontend-ui-shell`  
**Owner area:** `apps/web/`  
**Precondition:** Task 001 backend foundation telah direview dan di-freeze. Prompt desain dari Claude telah direview dan disetujui ChatGPT.

## Konteks wajib

Baca sampai selesai:
- `docs/PRD.md`
- `docs/API_CONTRACT.md`
- Prompt implementasi UI/UX hasil Claude yang telah disetujui Head Manager.

PRD dan API contract adalah sumber kebenaran produk dan integrasi. Prompt Claude adalah arahan visual, bukan otoritas untuk mengubah business rules atau arsitektur.

KiloCode adalah satu-satunya AI yang mengubah kode repository. Implementasikan sendiri frontend berdasarkan spesifikasi visual yang disetujui. Jangan meminta Claude mengubah repository.

## Stack frontend

Gunakan baseline yang disetujui PRD:
- React + Vite + TypeScript
- Tailwind CSS
- React Router
- Recharts
- Lucide React

Gunakan versi package yang kompatibel dan stabil saat pengerjaan. Hindari dependency besar tanpa alasan.

## Tujuan task

Membangun UI web responsif untuk MVP menggunakan mock data terpisah. Task ini adalah frontend prototype; belum mengintegrasikan endpoint backend.

## Layar yang harus dibuat

1. **Setup awal** — form membuat akun pemilik pertama.
2. **Login** — email/password dan state validasi/error/loading.
3. **Dashboard** — total saldo, pemasukan periode, pengeluaran periode, arus kas bersih, saldo per akun, grafik arus kas, dan transaksi terbaru.
4. **Transaksi** — daftar, pencarian, filter tanggal/jenis/akun/kategori/status, serta form tambah transaksi dengan mode Pemasukan, Pengeluaran, dan Transfer.
5. **Akun** — daftar Kas/Bank/E-Wallet, saldo, serta form tambah/edit.
6. **Kategori** — kategori pemasukan/pengeluaran, form tambah/edit/nonaktifkan.
7. **Laporan** — rentang tanggal, ringkasan pemasukan/pengeluaran/arus kas bersih, rincian kategori, visualisasi, dan tombol ekspor CSV (prototype UI saja).

Prioritas implementasi: app shell dan navigasi, dashboard, transaksi, akun, login/setup. Kategori dan laporan tetap memiliki routing dan layar placeholder yang pantas bila harus dipisah ke task berikutnya.

## Implementasi desain

- Terapkan design tokens, layout, komponen, dan responsive behavior dari prompt Claude yang telah disetujui.
- Bila prompt Claude bertentangan dengan PRD, API contract, aksesibilitas, atau keamanan, ikuti PRD/API contract dan laporkan konflik.
- Jangan mengarang metrik atau visualisasi.
- Bahasa UI Indonesia dan format nominal `Rp 1.250.000` tanpa desimal.
- Sediakan state loading, empty, error, success, disabled, dan validation pada area utama.
- Gunakan komponen reusable dan struktur folder yang mudah dirawat.

## Data dan aturan domain

- Gunakan mock data lokal yang jelas ditandai sebagai DEMO atau data contoh.
- Pisahkan mock data dari komponen dan siapkan batas data agar mudah diganti API client pada task integrasi.
- Ikuti tipe dan bentuk objek di `API_CONTRACT.md`.
- Nominal pada batas data harus string integer Rupiah; hindari floating-point untuk perhitungan uang.
- Status transaksi: `POSTED` dan `VOIDED`.
- Transfer tidak dihitung sebagai pemasukan/pengeluaran.
- Transfer memerlukan akun sumber dan akun tujuan berbeda serta tidak memakai kategori.
- Pemasukan dan pengeluaran memerlukan kategori yang sesuai.
- Transaksi `VOIDED` tidak boleh memengaruhi saldo atau ringkasan.

## Batasan integrasi

- Jangan memanggil API backend pada task ini.
- Jangan membuat mock service yang berpura-pura sebagai backend aktif.
- Jangan mengubah `apps/api/`, `docs/PRD.md`, atau `docs/API_CONTRACT.md`.
- Jangan menambah fitur di luar MVP tanpa persetujuan Head Manager.
- Jangan mengubah konfigurasi root kecuali mutlak diperlukan; jelaskan alasan dan diff-nya.

## Acceptance criteria

- Frontend dapat di-install dan dijalankan secara lokal dari `apps/web/`.
- Routing layar MVP berjalan.
- Layout nyaman di desktop dan mobile.
- Dashboard menampilkan empat metrik utama dan transaksi terbaru dengan label data demo.
- Form transaksi memiliki mode Pemasukan/Pengeluaran/Transfer dan field sesuai jenisnya.
- Transfer tidak masuk total pemasukan/pengeluaran pada ringkasan mock.
- `VOIDED` dibedakan dan tidak dihitung sebagai transaksi aktif.
- Format IDR tanpa desimal.
- Tersedia state loading/empty/error pada bagian data utama.
- Komponen reusable, typecheck, lint, dan test yang tersedia lulus.
- Tidak ada request ke backend.
- Tidak ada perubahan di `apps/api/` atau dokumen sumber kebenaran.

## Format laporan setelah selesai

Berikan:
1. Ringkasan implementasi dan kesesuaian dengan prompt desain Claude.
2. Daftar halaman dan komponen yang dibuat.
3. Struktur folder utama.
4. Cara menjalankan frontend.
5. Perintah lint/typecheck/test beserta output aktual.
6. Screenshot desktop dan mobile bila lingkungan mendukung.
7. Perbedaan desain, asumsi, keterbatasan, dan keputusan yang perlu Head Manager.
8. Commit hash dan ringkasan diff.

Jangan mengklaim test lulus tanpa menjalankannya. Hentikan pekerjaan setelah Task 002. Jangan mengintegrasikan API atau mengerjakan milestone berikutnya tanpa instruksi.
