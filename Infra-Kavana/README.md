# Kavana Infrastructure

Repository ini berisi seluruh konfigurasi Docker dan infrastruktur untuk project **Kavana** - platform bimbingan online.

> ⚠️ **Private Repository** - Hanya untuk tim developer Kavana. Jangan di-share ke publik.

---

## 📁 Struktur Repository

```
Infrastructure/
├── docker-compose.yml        # Production Docker Compose
├── docker-compose.dev.yml    # Development Docker Compose (hot reload)
├── Makefile                  # Command shortcuts untuk Docker
├── .env.docker.example       # Template environment variables
├── DOCKER.md                 # Dokumentasi lengkap Docker setup
├── DOCKER_SUMMARY.md         # Ringkasan konfigurasi Docker
└── README.md                 # File ini
```

---

## 🚀 Quick Start

### Prasyarat
- Docker Engine 20.10+
- Docker Compose 2.0+
- Clone **Backend-Kavana** dan **Frontend-Kavana** di folder yang sama

### Struktur folder yang diharapkan:
```
kavana/
├── Backend-Kavana/     # Backend repository (Express.js)
├── Frontend-Kavana/    # Frontend repository (Next.js)
└── Infrastructure/     # Repository ini
```

### Menjalankan

```bash
# Production
cd Infrastructure
docker compose up --build

# Development (hot reload)
docker compose -f docker-compose.dev.yml up --build

# Gunakan Makefile untuk shortcut
make help     # Lihat semua command
make up       # Start services
make down     # Stop services
```

---

## 🐳 Services

| Service | Port | Deskripsi |
|---------|------|-----------|
| Backend API | 3000 | Express.js REST API |
| Frontend | 3001 | Next.js web app |
| MySQL | 3306 | Database |
| Redis | 6379 | Cache & rate limiting |

---

## 🔧 Konfigurasi

### Environment Variables

1. Copy template:
```bash
cp .env.docker.example .env
```

2. Isi kredensial di `.env`

3. Backend dan Frontend juga butuh `.env` masing-masing di folder repository-nya

---

## 📚 Dokumentasi Lengkap

Lihat [DOCKER.md](./DOCKER.md) untuk dokumentasi lengkap termasuk:
- Setup guide
- Troubleshooting
- Production deployment
- Security notes

---

## 🔄 Development Workflow

1. Developer clone 3 repository: `Backend-Kavana`, `Frontend-Kavana`, `Infrastructure`
2. Developer mengerjakan fitur di Backend atau Frontend
3. Untuk testing dengan Docker: `cd Infrastructure && make dev`
4. Hot reload aktif - perubahan kode langsung terlihat

---

## 👥 Akses Developer

Repository ini adalah **private** di organisasi Kavana.
Untuk mendapatkan akses, hubungi lead developer.

---

**Organisasi**: Kavana
**Visibility**: Private
**Maintainer**: Kavana Team
