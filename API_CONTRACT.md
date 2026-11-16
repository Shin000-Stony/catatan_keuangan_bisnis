# API Contract — Business Finance Ledger

- Versi: 0.1 (draft baseline)
- Base path: `/api`
- Format: JSON
- Auth: server-side session dengan cookie HttpOnly
- Semua tanggal transaksi: `YYYY-MM-DD` (tanggal kalender Asia/Jakarta)
- Timestamp audit: ISO 8601 UTC
- Semua nominal IDR: string integer positif, contoh `"150000"`; jangan kirim nominal sebagai floating-point number.

Dokumen ini adalah kontrak awal. Endpoint keuangan merupakan target untuk milestone berikutnya dan **tidak harus diimplementasikan pada Task 001**.

## 1. Bentuk response umum

Sukses:

```json
{
  "data": {},
  "meta": {}
}
```

Error:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Periksa kembali data yang dikirim.",
    "details": []
  }
}
```

Gunakan HTTP status yang sesuai: 400 input tidak valid, 401 belum login, 403 tidak diizinkan, 404 tidak ditemukan, 409 konflik, 429 rate limit, 500 kesalahan internal.

## 2. Auth endpoints — scope implementasi M0

### `GET /api/health`

Memeriksa API dan koneksi database. Jangan mengembalikan secret, credential, atau detail internal.

### `GET /api/auth/setup-status`

Response:

```json
{ "data": { "setupRequired": true } }
```

`setupRequired` bernilai false setelah owner berhasil dibuat.

### `POST /api/auth/setup`

Hanya aktif jika belum ada owner.

Request:

```json
{ "email": "owner@example.com", "password": "password-kuat" }
```

Sukses: buat satu owner, buat sesi login, set cookie sesi, dan kembalikan profil owner tanpa password hash.

Jika owner sudah ada, kembalikan `409 SETUP_COMPLETED`. Proteksi harus tetap benar jika dua request setup terjadi bersamaan.

### `POST /api/auth/login`

Request:

```json
{ "email": "owner@example.com", "password": "password-kuat" }
```

Sukses: set cookie sesi dan kembalikan profil owner. Pesan kredensial salah harus generik.

### `POST /api/auth/logout`

Akhiri sesi di server, hapus cookie, dan kembalikan sukses.

### `GET /api/auth/me`

Membutuhkan sesi valid. Response berisi ID dan email owner, tanpa hash password/token.

## 3. Aturan session

- Token sesi harus dibuat dengan cryptographic random generator.
- Simpan hash token di database, bukan token mentah.
- Cookie default: `HttpOnly`, `SameSite=Lax`, `Path=/`; `Secure=true` di production.
- Masa berlaku sesi baseline: 7 hari, dapat dikonfigurasi.
- Logout menghapus sesi terkait di database dan menghapus cookie browser.
- Request yang mengubah state harus menjalani pemeriksaan Origin/CSRF yang sesuai.
- Rate-limit endpoint login dan setup.

## 4. Tipe data domain

### Account

```json
{
  "id": "uuid",
  "name": "Kas Utama",
  "type": "CASH",
  "openingBalance": "500000",
  "active": true,
  "balance": "725000"
}
```

`type`: `CASH | BANK | E_WALLET`.

### Category

```json
{
  "id": "uuid",
  "name": "Penjualan",
  "type": "INCOME",
  "active": true
}
```

`type`: `INCOME | EXPENSE`.

### Transaction

```json
{
  "id": "uuid",
  "type": "INCOME",
  "amount": "150000",
  "transactionDate": "2026-11-16",
  "accountId": "uuid",
  "destinationAccountId": null,
  "categoryId": "uuid",
  "description": "Penjualan tunai",
  "status": "POSTED",
  "voidReason": null,
  "createdAt": "2026-11-16T03:00:00.000Z",
  "updatedAt": "2026-11-16T03:00:00.000Z"
}
```

`type`: `INCOME | EXPENSE | TRANSFER`; `status`: `POSTED | VOIDED`.

## 5. Endpoint keuangan — target M1–M3

### Accounts

- `GET /api/accounts` — daftar akun dan saldo hasil perhitungan.
- `POST /api/accounts` — buat akun.
- `PATCH /api/accounts/:id` — ubah nama/jenis/status sesuai aturan.

Create request:

```json
{ "name": "Kas Utama", "type": "CASH", "openingBalance": "500000" }
```

### Categories

- `GET /api/categories?type=INCOME|EXPENSE`
- `POST /api/categories`
- `PATCH /api/categories/:id`

Create request:

```json
{ "name": "Penjualan", "type": "INCOME" }
```

### Transactions

- `GET /api/transactions` — mendukung `from`, `to`, `type`, `accountId`, `categoryId`, `status`, `search`, `page`, `pageSize`.
- `POST /api/transactions` — buat income, expense, atau transfer.
- `GET /api/transactions/:id` — detail.
- `POST /api/transactions/:id/void` — batalkan transaksi dengan alasan.

Income request:

```json
{
  "type": "INCOME",
  "amount": "150000",
  "transactionDate": "2026-11-16",
  "accountId": "uuid-akun",
  "categoryId": "uuid-kategori-income",
  "description": "Penjualan tunai"
}
```

Expense request memiliki bentuk sama dengan `type: "EXPENSE"` dan `categoryId` kategori EXPENSE.

Transfer request:

```json
{
  "type": "TRANSFER",
  "amount": "50000",
  "transactionDate": "2026-11-16",
  "accountId": "uuid-akun-sumber",
  "destinationAccountId": "uuid-akun-tujuan",
  "description": "Setor ke rekening bank"
}
```

Void request:

```json
{ "reason": "Salah memasukkan nominal" }
```

Transfer tidak menerima `categoryId`. `accountId` berarti akun terkait untuk income/expense dan akun sumber untuk transfer.

### Dashboard dan laporan

- `GET /api/dashboard?from=YYYY-MM-DD&to=YYYY-MM-DD`
- `GET /api/reports/cash-flow?from=YYYY-MM-DD&to=YYYY-MM-DD`
- `GET /api/reports/cash-flow.csv?from=YYYY-MM-DD&to=YYYY-MM-DD`

Dashboard memuat total saldo saat ini, pemasukan/pengeluaran periode, arus kas bersih, saldo per akun, dan transaksi terbaru. Laporan dan dashboard harus menggunakan aturan kalkulasi yang sama.

## 6. Pagination

Parameter default: `page=1`, `pageSize=20`; batas maksimum `pageSize` ditentukan backend (baseline 100).

Meta response:

```json
{ "page": 1, "pageSize": 20, "totalItems": 42, "totalPages": 3 }
```

## 7. Aturan konsistensi

- Semua endpoint keuangan memerlukan sesi owner.
- Validasi dilakukan di server, bukan hanya frontend.
- Nominal diserialisasi sebagai string integer.
- Tanggal transaksi disimpan sebagai PostgreSQL `DATE` agar tidak bergeser akibat konversi timezone.
- Timestamp audit menggunakan `timestamptz`.
- Operasi transfer dan void dijalankan atomik di database.
- Transaksi VOIDED dikecualikan dari seluruh agregasi saldo dan laporan.
- Jangan menghitung transfer sebagai pemasukan atau pengeluaran.
