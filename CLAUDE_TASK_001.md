# Claude Website — Task 001: UI/UX Design Brief & KiloCode Prompt

**Role:** Visual Design Consultant / Prompt Generator  
**Milestone:** M0 — Foundation  
**Output:** Teks spesifikasi desain dan prompt implementasi untuk KiloCode  
**Repository access:** Tidak ada. Jangan mengedit file atau menulis kode aplikasi.

## Konteks wajib

Baca dan pahami dokumen berikut yang akan diberikan sebagai konteks:
- `PRD.md` — sumber kebenaran kebutuhan produk.
- `API_CONTRACT.md` — referensi bentuk data dan endpoint untuk integrasi nanti.

Produk adalah aplikasi buku kas web berbahasa Indonesia untuk satu pemilik bisnis, satu bisnis per instalasi, dan mata uang IDR. Target platform adalah browser desktop dan mobile.

## Tujuan task

Buat arahan visual UI/UX yang matang dan satu prompt implementasi yang bisa langsung diberikan kepada KiloCode. KiloCode adalah satu-satunya AI yang mengubah repository dan menulis kode aplikasi.

Kamu hanya bertugas menghasilkan desain dalam bentuk penjelasan terstruktur dan prompt. Jangan mengklaim telah membuat atau mengubah file di repository.

## Layar yang perlu dirancang

1. Setup awal — membuat akun pemilik pertama.
2. Login — email dan password.
3. Dashboard — total saldo, pemasukan, pengeluaran, arus kas bersih, saldo per akun, grafik, dan transaksi terbaru.
4. Transaksi — daftar, pencarian, filter, serta form Pemasukan, Pengeluaran, dan Transfer.
5. Akun — akun Kas, Bank, dan E-Wallet.
6. Kategori — kategori pemasukan dan pengeluaran.
7. Laporan — rentang tanggal, ringkasan arus kas, rincian kategori, dan ekspor CSV.

Prioritaskan dashboard dan transaksi sebagai layar inti. Tetap berikan arahan konsisten untuk layar lainnya.

## Arahan desain

- Desain profesional, jelas, sederhana, dan cocok untuk pemilik bisnis kecil.
- Bahasa UI: Bahasa Indonesia.
- Format nominal: Rupiah tanpa desimal, misalnya `Rp 1.250.000`.
- Tanggal mengikuti format Indonesia.
- Utamakan keterbacaan angka dan kemudahan mencatat transaksi.
- Tentukan design tokens konkret: warna (HEX), tipografi, skala spacing, radius, border, shadow, ukuran kontrol, dan breakpoint.
- Tentukan layout desktop dan adaptasi mobile, termasuk pola navigasi.
- Jelaskan hierarchy, grid, ukuran/penempatan komponen, tabel, kartu, form, modal/drawer, grafik, dan icon style.
- Jelaskan state loading, empty, error, success, disabled, hover, focus, dan validation.
- Perhatikan kontras, label form, keyboard navigation, dan target sentuh.
- Hindari dekorasi berlebihan dan visualisasi data yang tidak punya arti bisnis.

## Aturan domain yang wajib tercermin dalam desain

- Jenis transaksi adalah Pemasukan, Pengeluaran, dan Transfer.
- Transfer antar-akun tidak boleh ditampilkan sebagai pemasukan atau pengeluaran bisnis.
- Transaksi yang dibatalkan (`VOIDED`) harus terlihat berbeda dari transaksi aktif (`POSTED`).
- Form Transfer meminta akun sumber dan akun tujuan, tanpa kategori pemasukan/pengeluaran.
- Form Pemasukan dan Pengeluaran meminta kategori yang sesuai jenisnya.
- Jangan tampilkan saldo atau angka contoh seolah-olah berasal dari backend. Semua contoh harus diberi label data demo/mock.

## Format keluaran wajib

Susun jawaban dengan urutan berikut:

1. **Design direction** — konsep visual dan alasan pemilihan.
2. **Design tokens** — tabel warna HEX, font, ukuran teks, spacing, radius, shadow, dan breakpoint.
3. **App shell & navigation** — layout desktop dan mobile.
4. **Screen-by-screen specification** — struktur dan komponen setiap layar.
5. **Reusable component inventory** — komponen yang perlu dibuat KiloCode.
6. **Interaction & state specification** — perilaku tombol, form, tabel, modal, filter, dan semua state UI.
7. **Responsive & accessibility requirements** — perilaku layar kecil serta kebutuhan aksesibilitas.
8. **KiloCode implementation prompt** — satu prompt lengkap, eksplisit, dan siap salin yang memerintahkan KiloCode mengimplementasikan desain di `apps/web/` menggunakan stack yang disetujui PRD.

Prompt KiloCode harus meminta implementasi komponen React + TypeScript yang reusable, bukan hanya mockup statis. Pada tahap UI prototype, gunakan mock data yang dipisahkan dan diberi label demo; jangan menghubungkan API sebelum task integrasi disetujui.

## Batasan

- Jangan menulis kode React, HTML, CSS, atau konfigurasi sebagai deliverable utama.
- Jangan meminta akses repository atau mengklaim membuat commit.
- Jangan mengubah PRD, API contract, arsitektur, atau business rules.
- Jangan menciptakan fitur di luar MVP.
- Bila ada kebutuhan visual yang bertentangan dengan PRD atau API contract, tandai sebagai pertanyaan untuk ChatGPT, jangan memutuskan sendiri.

Akhiri dengan prompt KiloCode yang berdiri sendiri sehingga dapat disalin tanpa perlu menyusun ulang jawabanmu.
