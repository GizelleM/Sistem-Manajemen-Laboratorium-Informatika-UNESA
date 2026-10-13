# SIM-LAB — Sistem Informasi Manajemen Laboratorium

Sistem Informasi Manajemen Laboratorium Rumpun Informatika, Universitas Negeri Surabaya (UNESA). Mencakup peminjaman ruang dan alat, inventaris, jadwal, absensi, nilai praktikum, laporan kerusakan dan insiden, pemeliharaan, serta notifikasi multi-peran.

## Tech Stack

| Bagian   | Teknologi |
|----------|-----------|
| Backend  | Laravel (PHP 8.2+), Laravel Sanctum, MySQL 8.0 / MariaDB |
| Frontend | Vue.js 3 (Composition API), Vite, Pinia, Vue Router, Axios |

> Cek versi Laravel yang terpasang dengan `php artisan --version` di folder `backend`, lalu sesuaikan tabel di atas.

## Prasyarat

- PHP 8.2 atau lebih baru (ekstensi: `pdo_mysql`, `mbstring`, `fileinfo`, `zip`, `intl`)
- Composer
- Node.js 18 atau lebih baru, beserta npm
- MySQL 8.0 / MariaDB (bisa lewat XAMPP atau Laragon)
- Git

## Struktur Proyek

```
Sistem-Manajemen-Laboratorium-Rumpun-Informatika-UNESA/
├── backend/                         # Laravel (REST API)
│   ├── app/
│   │   ├── Http/Controllers/        # Auth, User, Profil, Inventaris, Peminjaman,
│   │   │                            # Kerusakan, Insiden, Modul, Notifikasi, Matkul,
│   │   │                            # Jadwal, Ruangan, Absensi, Nilai, Pemeliharaan
│   │   └── Models/
│   ├── database/
│   │   ├── migrations/
│   │   └── seeders/
│   └── routes/api.php
└── frontend/                        # Vue 3 (SPA)
    ├── src/
    │   ├── App.vue                  # Shell, sidebar per role, titik notifikasi
    │   ├── main.js
    │   ├── api/index.js             # Instance Axios
    │   ├── router/index.js
    │   ├── stores/                  # auth.js, ui.js, data.js (Pinia)
    │   └── views/                   # LoginView, DashboardView, ProfilView, dst.
    └── vite.config.js
```

> Sesuaikan struktur di atas dengan isi repo kamu (misalnya letak controller atau ada tidaknya file `database.sql` dan `prototype.html`).

## Fitur Utama

- **Login berbasis peran**: Mahasiswa, Asisten Praktikum, Asisten Lab, Dosen, Admin.
- **Menu per peran**: Aslab tanpa absensi, nilai, dan modul. Dosen tanpa jadwal (absensi dan kerusakan hanya baca). Admin hanya baca untuk ruangan, mata kuliah, dan jadwal.
- **Pop-up "Laporan Terkirim"** setelah laporan kerusakan atau insiden berhasil dikirim.
- **Titik merah notifikasi** yang hilang otomatis setelah notifikasi dibuka.
- **Edit profil real-time**: nama di sidebar berubah saat diketik, foto profil dengan preview instan.
- **Validasi peminjaman oleh dosen**, dengan badge jumlah peminjaman berstatus `menunggu`.

## Instalasi

### 1. Clone repository

```bash
git clone https://github.com/<username>/<nama-repo>.git
cd <nama-repo>
```

### 2. Siapkan database

Nyalakan MySQL (XAMPP Control Panel: Start pada MySQL), lalu buat database kosong:

```bash
mysql -u root -e "CREATE DATABASE IF NOT EXISTS simlab CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
```

Di Windows dengan XAMPP, jika `mysql` tidak dikenali, pakai path lengkap: `C:\xampp\mysql\bin\mysql.exe`. Database juga bisa dibuat lewat phpMyAdmin.

### 3. Backend

```bash
cd backend
composer install
cp .env.example .env          # Windows CMD/PowerShell: copy .env.example .env
php artisan key:generate
```

Edit `backend/.env`:

```env
APP_URL=http://127.0.0.1:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=simlab
DB_USERNAME=root
DB_PASSWORD=
```

Lalu jalankan migrasi, seeder, dan server:

```bash
php artisan migrate --seed
php artisan storage:link
php artisan serve
```

Backend berjalan di `http://127.0.0.1:8000`. Biarkan terminal ini tetap terbuka.

### 4. Frontend

Buka terminal baru:

```bash
cd frontend
npm install
cp .env.example .env          # Windows CMD/PowerShell: copy .env.example .env
```

Isi `frontend/.env`:

```env
VITE_API_URL=http://127.0.0.1:8000/api
```

Jalankan:

```bash
npm run dev
```

Buka alamat yang tampil di terminal (biasanya `http://localhost:5173`). Setiap kali `.env` frontend diubah, restart `npm run dev`.

## Akun Demo

Password semua akun: `password`

| Role       | Email                   | Pilih di "Login sebagai" |
|------------|-------------------------|--------------------------|
| Mahasiswa  | mahasiswa@unesa.ac.id   | Mahasiswa                |
| Asprak     | asprak@unesa.ac.id      | Asisten Praktikum        |
| Aslab      | aslab@unesa.ac.id       | Asisten Lab              |
| Dosen      | dosen@unesa.ac.id       | Dosen                    |
| Admin      | admin@unesa.ac.id       | Admin (Pengelola)        |

Pilihan **Login sebagai** harus sama dengan role akun. Jika tidak cocok, login ditolak dengan pesan kredensial tidak valid.

## Troubleshooting

| Masalah | Penyebab dan solusi |
|---------|---------------------|
| `SQLSTATE[HY000] [2002] connection refused` | MySQL belum jalan. Start MySQL di XAMPP, lalu cek `DB_HOST` dan `DB_PORT` di `.env`. |
| `Unknown column '...'` saat seeding | Tabel dibuat dari migrasi versi lama. Jalankan `php artisan migrate:fresh --seed` (menghapus semua data). |
| `could not find driver` | Aktifkan `extension=pdo_mysql` di `php.ini`, lalu restart terminal. |
| `ERR_CONNECTION_REFUSED` di browser | `php artisan serve` belum jalan atau sudah mati. |
| Login 422 atau "Kredensial tidak valid" | Role di dropdown tidak sama dengan role akun. Gunakan tabel akun demo di atas. |
| Error CORS di console | Tambahkan origin frontend ke `allowed_origins` di `backend/config/cors.php`, lalu `php artisan config:clear`. |
| Perubahan `.env` tidak terbaca | Jalankan `php artisan config:clear` (backend) atau restart `npm run dev` (frontend). |
| Error 500 | Lihat pesan asli di `backend/storage/logs/laravel.log`. |


## Kolaborasi dengan Git

Alur singkat untuk anggota tim:

```bash
git checkout main
git pull origin main                  # ambil perubahan terbaru
git checkout -b fitur/nama-fitur      # buat branch baru
# ... edit kode ...
git add .
git commit -m "feat: deskripsi singkat perubahan"
git push -u origin fitur/nama-fitur   # push, lalu buka Pull Request di GitHub
```

Aturan tim:
- Jangan push langsung ke `main`. Selalu lewat branch dan Pull Request.
- Satu branch untuk satu fitur atau perbaikan.
- Jangan pernah commit `.env`, `vendor/`, atau `node_modules/`.
- Jalankan `git pull origin main` sebelum mulai bekerja.
Format pesan commit: `feat:` (fitur baru), `fix:` (perbaikan bug), `docs:` (dokumentasi), `refactor:` (perapihan kode).

