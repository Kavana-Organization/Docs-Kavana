# Spesifikasi Desain Wireframe Lo-Fi (Paper Sketch) Kavana v3.5
> **Status:** Lengkap 100% Seluruh Peran termasuk **Role Developer Superuser**, Mahasiswa, Dosen, Koordinator, Kaprodi, Admin, Penguji, Auth & Landing Page dengan **Sidebar Navigasi Kiri Otentik** sesuai [`MENU_CONFIG`](file:///c:/Project/Kavana/New-Frontend/src/lib/constants.js) dan [`developer-client.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/developer/developer-client.jsx).

Tampilan visual interaktif dapat diakses pada: [**`kavana_lofi_wireframes.html`**](file:///c:/Project/Kavana/Docs-Kavana/Frontend-Kavana/kavana_lofi_wireframes.html).

---

## 1. Pemetaan Lengkap Rute & Komponen Kodingan

| No | Modul / Halaman | Rute Frontend (`New-Frontend`) | Komponen Kodingan Utama |
| :--- | :--- | :--- | :--- |
| 1 | **Landing Page** | `/` | [`src/app/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/page.jsx) |
| 2 | **Auth (Login & Register)** | `/login`, `/register` | [`src/app/login/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/login/page.jsx) |
| 3 | **Dashboard Mahasiswa** | `/dashboard/mahasiswa/*` | [`src/app/dashboard/mahasiswa/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/mahasiswa/page.jsx) |
| 4 | **Dashboard Dosen** | `/dashboard/dosen/*` | [`src/app/dashboard/dosen/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/dosen/page.jsx) |
| 5 | **Dashboard Koordinator** | `/dashboard/koordinator/*` | [`src/app/dashboard/koordinator/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/koordinator/page.jsx) |
| 6 | **Dashboard Kaprodi** | `/dashboard/kaprodi/*` | [`src/app/dashboard/kaprodi/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/kaprodi/page.jsx) |
| 7 | **Dashboard Admin** | `/dashboard/admin/*` | [`src/app/dashboard/admin/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/admin/page.jsx) |
| 8 | **Dashboard Penguji** | `/dashboard/penguji` | [`src/app/dashboard/penguji/page.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/penguji/page.jsx) |
| 9 | **Dashboard Developer** | `/dashboard/developer/*` | [`src/app/dashboard/developer/developer-client.jsx`](file:///c:/Project/Kavana/New-Frontend/src/app/dashboard/developer/developer-client.jsx) |

---

## 2. Rincian Khusus: Dashboard Developer (`/dashboard/developer`)

### 2.1 Sidebar Developer (`MENU_CONFIG.developer`)
Sidebar dirancang untuk pengembang sistem dan diagnostik teknis superuser:
```text
+------------------------------------+
| [</>] KAVANA DEV                   |
| Developer Center                   |
+------------------------------------+
| Peran: Developer (Root)            |
+------------------------------------+
| [*] [💻] Developer Center          |
| [ ] [⚡] System Health (/health)   |
| [ ] [📋] Audit Logs (/audit-logs)  |
| [ ] [🛡️] Auth Logs (/auth-logs)   |
| [ ] [🌐] Auth Tracker (/trackers)  |
| [ ] [🔑] Device Lock (/devices)    |
| [ ] [🗄️] Redis Cache (/redis-cache)|
| [ ] [📊] Permission Matrix         |
| ---------------------------------- |
| [ ] [⚙️] Pengaturan Sistem         |
+------------------------------------+
| (DEV) Developer Superuser          |
| [✓] Device Enrolled                |
| [ 🚪 Keluar Console ]              |
+------------------------------------+
```

### 2.2 Tata Letak 2-Kolom Developer (ASCII Wireframe)

```text
+----------------------------------+----------------------------------------------------------------------------+
| [</>] KAVANA DEV                 | TOPBAR: Developer Center | Node 22 • Express 5 • OTel    [✓ System Green]  |
+----------------------------------+----------------------------------------------------------------------------+
| Peran: Developer (Root)          | ★ 4 STATCARD DIAGNOSTIK UTAMA                                              |
+----------------------------------+ +-----------------+ +---------------+ +---------------+ +---------------+ |
| [*] Developer Center             | | SYSTEM:         | | DB LATENCY:   | | REDIS CACHE:  | | DEVICE LOCK:  | |
| [ ] System Health (/health)      | | Healthy ✓       | | 12 ms (Pool OK| | Active (18 ms)| | 1 Enrolled    | |
| [ ] Audit Logs (/audit-logs)     | +-----------------+ +---------------+ +---------------+ +---------------+ |
| [ ] Auth Logs (/auth-logs)       |                                                                            |
| [ ] Auth Tracker (/trackers)     | ★ RINGKASAN RUNTIME & KEAMANAN                                             |
| [ ] Device Lock (/devices)       | +-----------------------------------------+ +----------------------------+ |
| [ ] Redis Cache (/redis-cache)   | | Runtime Config:                         | | Security Flags:            | |
| [ ] Permission Matrix            | | • NODE_ENV: development / local         | | • Turnstile: Active        | |
| -------------------------------- | | • Better Auth: Mounted /api/auth/better | | • Rate Limiter: Redis On   | |
| [ ] Pengaturan Sistem            | | • CORS: localhost:3000, kavana.my.id    | | • Token Denylist: Active   | |
+----------------------------------+ +-----------------------------------------+ +----------------------------+ |
| (DEV) Developer Superuser        |                                                                            |
| [✓] Device Enrolled              | ★ PETA MATRIKS AKSES EFEKTIF (PERMISSION MATRIX)                           |
| [ 🚪 Keluar Console ]            | Fitur: Bimbingan [Dev,Adm,Kap,Kor,Dsn,Mhs] | Kelola Koord [Kaprodi,Dev]    |
+----------------------------------+----------------------------------------------------------------------------+
```

### 2.3 Rincian Fitur Sub-View Developer:
1. **System Health (`/health`)**: Menampilkan hasil synthetic tests (database ping 12ms, redis ping 18ms, qstash, auth secret strength).
2. **Technical Audit Logs (`/audit-logs`)**: Riwayat audit teknis dengan rincian metadata JSON dan penanda hasil aksi (*success/failed*).
3. **Authentication Logs & Trackers (`/auth-logs`, `/auth-trackers`)**: Log jejak akses login/logout, IP address pengguna, dan corong konversi pendaftaran.
4. **Device Lock (`/devices`)**: Perlindungan hardware level untuk akun developer menggunakan SHA-256 MAC/device token hash yang diverifikasi via variabel `DEVELOPER_DEVICE_IDS`.
5. **Redis Cache Manager (`/redis-cache`)**: Manajemen kunci cache aktif Upstash Redis dengan tombol pembersihan per prefix (misal: `/api/mahasiswa/`, `/api/dosen/`).
6. **Permission Matrix (`/permission-matrix`)**: Matriks visual hak akses seluruh 7 role sistem Kavana untuk memastikan integritas RBAC.

---

*Seluruh tombol sidebar pada role Developer dan role lainnya di [**`kavana_lofi_wireframes.html`**](file:///c:/Project/Kavana/Docs-Kavana/Frontend-Kavana/kavana_lofi_wireframes.html) dapat diklik secara interaktif untuk melihat setiap sub-halamannya.*
