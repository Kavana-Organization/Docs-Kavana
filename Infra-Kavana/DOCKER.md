# Docker Setup Guide untuk Kavana

## 📋 Prerequisites

- Docker Engine 20.10+
- Docker Compose 2.0+
- 4GB RAM minimum
- Ports available: 3000, 3001, 3306, 6379

## 🚀 Quick Start

### Production Mode (Frontend + Backend + DB + Redis)

```bash
# Clone semua repo dalam folder yang sama
# Struktur folder:
# kavana/
#   ├── Backend-Kavana/
#   ├── Frontend-Kavana/
#   └── Infrastructure/   <-- repo ini

cd Infrastructure

# Build and start all services
docker compose --env-file .env.docker up --build

# First time only - wait for DB to initialize (~30s)
# Then access:
# - Frontend: http://localhost:3001
# - Backend API: http://localhost:3000
# - Backend Docs: http://localhost:3000/docs
```

### Development Mode (with hot reload)

```bash
cd Infrastructure

# Start with development containers
docker compose -f docker-compose.dev.yml up --build

# Changes to code are reflected immediately
# - Frontend hot reload: http://localhost:3001
# - Backend hot reload: http://localhost:3000
```

### Single Service

```bash
# Backend only
docker compose --env-file .env.docker up backend

# Frontend only
docker compose --env-file .env.docker up frontend

# Database only
docker compose --env-file .env.docker up db

# Redis only
docker compose --env-file .env.docker up redis
```

## 📁 File Structure

```
kavana/
├── Backend-Kavana/              # Backend repository
│   ├── Dockerfile               # Multi-stage production build
│   └── .dockerignore            # Files to exclude
├── Frontend-Kavana/             # Frontend repository
│   ├── Dockerfile               # Next.js production build
│   ├── Dockerfile.dev           # Next.js development build
│   └── .dockerignore            # Files to exclude
└── Infrastructure/              # Repository ini (private)
    ├── docker-compose.yml       # Production compose
    ├── docker-compose.dev.yml   # Development compose
    ├── Dockerfile               # (deprecated - gunakan Backend/Dockerfile)
    ├── Makefile                 # Command shortcuts
    ├── .env.docker.example      # Environment template
    └── README.md
```

## 🔧 Configuration

### Environment Variables

Copy and customize `.env` files:

```bash
# Backend
cp ../Backend-Kavana/.env.example ../Backend-Kavana/.env

# Frontend
cp ../Frontend-Kavana/.env.example ../Frontend-Kavana/.env
```

### Database Initialization

Database is auto-initialized from `../Backend-Kavana/bimbingan_online.sql` on first run.

To reset:
```bash
docker compose down -v          # Remove volumes
docker compose --env-file .env.docker up db            # Reinitialize
```

## 🐳 Docker Images

### Backend (`kavana-backend`)
- Base: `node:24-alpine`
- Entry: `node local.js`
- Ports: 3000
- Health: `/health` endpoint

### Frontend (`kavana-frontend`)
- Base: `node:24-alpine`
- Entry: `npm start`
- Ports: 3001
- Health: `/` endpoint

### Database (`kavana-mysql`)
- Image: `mysql:8.4`
- Ports: 3306
- Volume: `mysql-data`

### Cache (`kavana-redis`)
- Image: `redis:8-alpine`
- Ports: 6379
- Volume: `redis-data`

## 📊 Service Health

Check service status:
```bash
docker compose ps

docker compose logs backend -f      # Follow backend logs
docker compose logs frontend -f     # Follow frontend logs
docker compose logs db -f           # Follow database logs
```

## 🔌 Network Communication

Services communicate via service names (Docker networking):
- Backend → Database: `db:3306`
- Backend → Redis: `redis:6379`
- Frontend → Backend: `http://backend:3000`

## 💾 Volume Persistence

```bash
# Volumes created automatically:
mysql-data          # Database files
redis-data          # Cache data
backend-logs        # Application logs (production only)

# List volumes
docker volume ls

# Inspect volume
docker volume inspect kavana_mysql-data

# Remove unused volumes
docker volume prune
```

## 🧹 Cleanup

```bash
# Stop all services
docker compose down

# Remove volumes (data will be lost)
docker compose down -v

# Remove images
docker compose down --rmi all
```

## 🐛 Troubleshooting

### Port Already in Use
```bash
# Find process using port
lsof -i :3000       # Linux/macOS
netstat -ano | findstr :3000  # Windows
```

### Database Connection Failed
```bash
# Check DB logs
docker compose logs db

# Verify credentials in .env
# Wait 30s for initialization
```

### Out of Disk Space
```bash
docker system prune -a  # Clean unused images/containers
docker volume prune     # Clean unused volumes
```

### Permission Denied
```bash
# Grant Docker permissions (Linux)
sudo usermod -aG docker $USER
newgrp docker
```

## 📈 Production Deployment

For production on servers:

```bash
# Use production compose
docker compose --env-file .env.docker up -d

# Enable auto-restart
docker update --restart unless-stopped kavana-backend
docker update --restart unless-stopped kavana-frontend

# Monitor
docker stats
docker compose logs -f
```

## 🔐 Security Notes

- Non-root user: `kavana:nodejs` (UID 1001)
- Read-only volumes where applicable
- Health checks enabled on all services
- `.dockerignore` excludes sensitive files

## 📝 Build Arguments

Frontend build supports build args:

```bash
docker compose build --build-arg NEXT_PUBLIC_API_BASE_URL=http://backend:3000
```

## ✅ Verification

After startup:

```bash
# Backend health
curl http://localhost:3000/health

# Frontend
curl http://localhost:3001/

# Database
docker exec kavana-mysql mysql -u kavana -p -e "SELECT 1;"

# Redis
docker exec kavana-redis redis-cli ping
```

---

**Created**: 2026-07-06
**Updated**: 2026-07-10 (dipindahkan ke Infrastructure/)
**Status**: Production Ready
