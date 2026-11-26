# QalaMu Backend

REST API (Node.js + Express + SQLite) untuk aplikasi QalaMu.

## Persyaratan
Node.js **22.13 atau lebih baru** (memakai modul bawaan `node:sqlite`).

## Menjalankan
```bash
npm install
cp .env.example .env     # lalu isi JWT_SECRET (dan GEMINI_API_KEY bila ada)
npm run dev
```
Server: http://localhost:3000

## Endpoint
Header untuk endpoint bertanda 🔒: `Authorization: Bearer <token>`

| Layar | Method | Endpoint | Body |
|---|---|---|---|
| Create Acc 1 | POST | /api/auth/register | name, email, password |
| Login | POST | /api/auth/login | email, password |
| Create Acc 2 🔒 | PUT | /api/profile | username, gender (`laki-laki`/`perempuan`) |
| Dashboard 🔒 | GET | /api/me | - |
| Dashboard 🔒 | GET | /api/reminder/today | - |
| Koreksi 🔒 | POST | /api/corrections | multipart, field `image` |
| Riwayat 🔒 | GET | /api/corrections | - |
| Detail 🔒 | GET | /api/corrections/:id | - |
| Foto 🔒 | GET | /api/corrections/:id/image | - |
| Laporan peningkatan 🔒 | GET | /api/report | - |
| Hapus 🔒 | DELETE | /api/corrections/:id | - |

Tanpa `GEMINI_API_KEY` (atau `ANTHROPIC_API_KEY`), endpoint koreksi mengembalikan data mock.

### Model Gemini yang Direkomendasikan
Fitur koreksi menganalisis foto tulisan tangan Bahasa Arab, membaca harakat, serta mengecek tata bahasa (nahwu & sharaf) dan kaidah penulisan.
- **`gemini-2.5-flash`** (Default): Model multimodal terbaik untuk OCR gambar, sangat cepat (latensi rendah), akurat mengenali tanda baca/harakat Arab, dan sangat hemat biaya/kuota gratis Google AI Studio.
- **`gemini-2.5-pro`**: Alternatif jika membutuhkan penalaran linguistik yang lebih mendalam untuk kalimat-kalimat Arab tingkat lanjut.

## Aturan kata sandi
Kata sandi baru (saat daftar) minimal 8 karakter, mengandung 1 huruf besar dan 1 angka. Login tidak memeriksa aturan ini, jadi akun lama dengan sandi lama tetap bisa masuk.

## Pengaturan hosting (opsional)
Semua diatur lewat environment variable; bila tidak diisi, perilakunya sama seperti biasa.

| Variabel | Fungsi |
|---|---|
| `TRUST_PROXY` | Jumlah proxy di depan server (umumnya `1` di hosting) agar pembatas login membaca IP pengguna dengan benar |
| `UPLOAD_DIR` | Lokasi penyimpanan foto tulisan (default: folder `uploads/`) |
| `QALAMU_DB_PATH` | Lokasi file database SQLite (default: `data/qalamu.db`) |

Untuk disk permanen, arahkan `UPLOAD_DIR` dan `QALAMU_DB_PATH` ke folder di disk tersebut; folder induknya harus sudah ada.

## Tes otomatis
```bash
npm test
```
Tes memakai database sementara, jadi data asli tidak tersentuh.
