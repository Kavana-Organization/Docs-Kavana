# Bimbingan Online API

Backend API untuk Sistem Bimbingan Online D4 Teknik Informatika Polindra.

## 🚀 Fitur

### Auth & User Management
- ✅ Register mahasiswa (D4 TI saja)
- ✅ Login multi-role (mahasiswa, dosen, kaprodi, koordinator, penguji, admin)
- ✅ Admin hardcoded via environment variable

### Mahasiswa
- ✅ Pilih track (proyek1-3, internship1-2)
- ✅ Create/join kelompok (max 2 anggota untuk proyek)
- ✅ Submit proposal + usulan dosen pembimbing
- ✅ Bimbingan online (max 8x)
- ✅ Submit laporan sidang
- ✅ Upload foto profil

### Dosen & Kaprodi
- ✅ Lihat mahasiswa bimbingan (termasuk sebagai pembimbing 2)
- ✅ Approve/reject bimbingan dan laporan
- ✅ Dashboard statistik

### Koordinator
- ✅ Validasi proposal
- ✅ Assign dosen pembimbing
- ✅ Jadwalkan sidang
- ✅ Manage jadwal periode

## 🛠️ Tech Stack

| Komponen | Teknologi |
|----------|-----------|
| Runtime | Node.js |
| Framework | Express.js 5.x (ES Modules) |
| Database | MySQL2 |
| Auth | JWT + bcryptjs |
| Storage | Cloudinary |
| Docs | Swagger UI |

## 📦 Instalasi

```bash
# Install dependencies
npm install

# Copy environment file
cp .env.example .env

# Edit .env dengan konfigurasi database & Cloudinary

# Jalankan development server
npm run dev
```

## ⚙️ Environment Variables

```env
# Database
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=password
DB_NAME=bimbingan_online

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=7d

# Admin (hardcoded)
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=admin123

# Cloudinary
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret
```

## 📚 API Documentation

Swagger UI tersedia di: `http://localhost:3000/docs`

## 🧪 Testing

```bash
# Test semua endpoint

```

## 📁 Struktur Folder

```
├── controllers/      # Business logic
├── routes/           # API endpoints
├── middleware/       # Auth & upload middleware
├── utils/            # Helper functions (Cloudinary, JWT)
├── config/           # Database config
├── swagger.js        # API documentation
├── index.js          # Main app
└── local.js          # Local server entry
```

## 👥 Roles

| Role | Deskripsi |
|------|-----------|
| mahasiswa | Mahasiswa D4 TI |
| dosen | Dosen pembimbing |
| kaprodi | Kepala Program Studi |
| koordinator | Koordinator proyek/internship |
| penguji | Penguji sidang |
| admin | Administrator sistem |

## 📝 License

MIT
