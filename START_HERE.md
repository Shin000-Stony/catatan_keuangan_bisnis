# Start Here — Business Finance Ledger MVP

## Keputusan awal
- Single-user: satu pemilik bisnis per instalasi.
- Satu bisnis per instalasi.
- IDR saja.
- Web responsif: desktop dan browser mobile.
- Bahasa UI Indonesia.
- Scope: buku kas (income, expense, transfer), akun, kategori, dashboard, laporan, CSV.

## Dokumen
- `PRD.md` — kebutuhan produk, batas scope, dan acceptance criteria.
- `API_CONTRACT.md` — kontrak API serta aturan data.
- `KILO_TASK_001.md` — prompt KiloCode untuk backend foundation dan autentikasi.
- `CLAUDE_TASK_001.md` — brief Claude Website untuk menghasilkan spesifikasi visual dan prompt KiloCode; bukan prompt coding untuk Claude.
- `KILO_TASK_002_FRONTEND.md` — prompt KiloCode untuk membangun frontend berdasarkan prompt desain Claude yang telah disetujui.

## Pembagian AI

- **ChatGPT — Head Manager / Technical Lead:** pemilik keputusan produk dan arsitektur, menyusun task, mereview prompt Claude, mereview kode KiloCode, dan menyetujui freeze.
- **Claude Website — Visual Design Consultant:** hanya menganalisis kebutuhan UI/UX dan menghasilkan design brief serta prompt implementasi untuk KiloCode. Tidak mengedit repository, tidak membuat commit, dan bukan coding agent.
- **KiloCode — Sole Executor:** satu-satunya AI yang mengubah repository dan menulis seluruh kode aplikasi: backend, database, frontend, integrasi, dan test.

## Urutan pengerjaan

1. Buat repository Git baru.
2. Salin seluruh dokumen paket ini ke repository; letakkan `PRD.md` dan `API_CONTRACT.md` di `docs/`.
3. Buat struktur aplikasi `apps/api/` dan `apps/web/`.
4. Jalankan **Claude Task 001** di website Claude dengan memberikan PRD dan API contract sebagai konteks. Minta keluaran design brief dan prompt implementasi KiloCode. Claude tidak perlu mendapat akses repository.
5. Bawa hasil Claude ke ChatGPT untuk design review. Setelah disetujui, simpan prompt final ke `docs/UI_DESIGN_BRIEF.md`.
6. Jalankan **KiloCode Task 001** pada branch `feat/001-backend-foundation`. KiloCode mengerjakan backend, database schema, migration, autentikasi, dan test.
7. Minta ChatGPT mereview diff, migration, output test, dan keamanan. Merge/freeze hanya setelah disetujui.
8. Dari baseline hasil Task 001 yang telah di-freeze, buat branch `feat/002-frontend-ui-shell`. Jalankan **KiloCode Task 002** dengan `docs/UI_DESIGN_BRIEF.md`. KiloCode membangun seluruh frontend berdasarkan prompt desain tersebut.
9. Review hasil, jalankan test, lalu merge/freeze.
10. Integrasi frontend-backend dilakukan pada task/milestone tersendiri setelah kedua fondasi stabil.

## Aturan kerja

- Satu coding agent saja: KiloCode.
- Claude menghasilkan spesifikasi desain/prompt, bukan kode aplikasi.
- PRD dan API contract adalah sumber kebenaran. Prompt Claude tidak boleh mengubah business rules.
- Semua perubahan scope dan kontrak API harus disetujui ChatGPT sebagai Head Manager.
- Satu task = satu branch = satu hasil yang dapat direview.
- Alur: PLAN → DESIGN PROMPT → REVIEW DESIGN → CODE → TEST → REVIEW → FREEZE.
- Tidak ada klaim test lulus tanpa output test aktual.
