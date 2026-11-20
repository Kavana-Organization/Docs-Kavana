# Laporan Implementasi PRD Rombak Desain UI/UX KAVANA v1.2

**Dokumen Acuan:** `Docs-Kavana/Frontend-Kavana/PRD_Rombak_Desain_UI_UX_KAVANA_v1.2.md`  
**Tanggal Implementasi:** 20 November 2026 (WIB)  
**Arah Visual:** *Institutional Academic Operations — Warm Slate & ULBI Blue*  
**Status Gate & DoD:** Fase 1 (`DoD-F1`) s.d. Fase 6 (`DoD-F6`) Selesai 100%

---

## 1. Fase 1 (`DoD-F1`) — Discovery, Audit, Gate §13, & Inventaris Aktual

### 1.1 Validasi Gate & Keputusan Tertunda (§13)

| Item Gate §13 | Hasil Investigasi Repositori Aktual (`Frontend-Kavana`) | Keputusan Implementasi |
| --- | --- | --- |
| **Stack Frontend Aktual** | Next.js 16 (App Router), React 19, Tailwind CSS v4 (`@theme inline`), Radix UI (`radix-ui`), `class-variance-authority`, Lucide React, Recharts, Zustand (`auth-store.js`), Playwright. | Dipertahankan 100%; seluruh pembaruan menggunakan primitif dan token semantik tanpa dependensi baru. |
| **Palet Resmi ULBI / Prodi** | Sistem menggunakan variabel `--ctp-*` (di-alias ke token semantik `--surface-*`, `--text-*`, `--accent`, `--brand`, `--status-*`) dengan warna primer **ULBI Academic Blue** (`217 91% 50%` light / `217 91% 60%` dark) dan **Warm Academic Slate**. | Seluruh hue ungu/indigo/violet (`258°`) pada `--ctp-mauve` dan `--ctp-lavender` digeser ke **Academic Cyan-Teal** (`195°` dan `205°`) sesuai larangan §8 dan `ENF-3`. |
| **Format NPM/NIM** | Data mahasiswa mendukung NPM 7–9 digit numerik (contoh: `121400088`) maupun kelompok proyek. | Seluruh tampilan NPM menggunakan `font-mono tabular-nums` (`JetBrains Mono`) tanpa pemotongan digit. |
| **Metrik Turunan yang Belum Tersedia (`DD-1..3`)** | Endpoint backend tidak menyediakan: (1) *SLA rata-rata hari review dosen*, (2) *Persentase penyelesaian revisi agregat di `/kaprodi/stats`*, (3) *Tren mingguan historis multi-semester*. | Diterapkan **Degradasi Data Mentah (`DD-2`)**: Kaprodi menampilkan daftar mahasiswa yang membutuhkan perhatian langsung dari `kaprodiAPI.getMahasiswaList()` disertai catatan eksplisit *"Ringkasan belum tersedia"*, tanpa angka fiktif (`DD-3`). |

### 1.2 Inventaris Fitur & Halaman Aktual per Role (Menggantikan Asumsi §4 & §7)

Seluruh rute di bawah ini bersumber langsung dari `MENU_CONFIG` pada `src/lib/constants.js` (yang dibekukan untuk menjaga kontrak API dan navigasi):

1. **Mahasiswa (`/dashboard/mahasiswa/*`)**
   - `/dashboard/mahasiswa` — Dashboard Progres Akademik & Checklist Tahapan (`DB-1`)
   - `/dashboard/mahasiswa/periode` — Informasi Periode Aktif
   - `/dashboard/mahasiswa/track` — Pemilihan Track (Proyek 1/2/3 & Internship 1/2)
   - `/dashboard/mahasiswa/kelompok` — Manajemen Kelompok Proyek
   - `/dashboard/mahasiswa/proposal` — Pengajuan Proposal & Bukti Pembayaran
   - `/dashboard/mahasiswa/pembimbing` — Pengajuan Dosen Pembimbing 1 & 2
   - `/dashboard/mahasiswa/bimbingan` — Logbook Bimbingan Mingguan dengan Vertical Timeline (`PG-3`)
   - `/dashboard/mahasiswa/laporan` — Unggah Laporan Sidang & Tautan Luaran
   - `/dashboard/mahasiswa/sidang` — Jadwal, Hasil, & Pengumpulan Revisi Sidang
   - `/dashboard/mahasiswa/profile` — Profil Akademik Mahasiswa
2. **Dosen Pembimbing (`/dashboard/dosen/*`)**
   - `/dashboard/dosen` — Dashboard Antrean Kerja (*High-Density Work Inbox*, `DB-2`)
   - `/dashboard/dosen/mahasiswa-bimbingan` — Daftar & Split-View Detail Mahasiswa Bimbingan (`PG-1`)
   - `/dashboard/dosen/bimbingan-approve` — Persetujuan Logbook Bimbingan + Drawer Detail + QR Signature (`PG-5`)
   - `/dashboard/dosen/laporan-approve` — Persetujuan Naskah Laporan Sidang + Drawer Detail (`PG-5`)
   - `/dashboard/dosen/revisi-approve` — Persetujuan Revisi Pasca Sidang (`PG-5`)
   - `/dashboard/dosen/profile` — Profil & Tanda Tangan Digital Dosen
3. **Ketua Program Studi / Kaprodi (`/dashboard/kaprodi/*`)**
   - `/dashboard/kaprodi` — Dashboard Analitik Kohort, Tabel Pendamping, & Degradasi Data Mentah (`DB-3`, `DV-2`, `DD-2`)
   - `/dashboard/kaprodi/monitoring` — Monitoring Tahapan Mahasiswa (Tabel Status & Board Tahapan, `PG-2`)
   - `/dashboard/kaprodi/kelola-dosen` — Daftar & Beban Bimbingan Dosen
   - `/dashboard/kaprodi/penetapan-pembimbing` — Penetapan Pembimbing Mahasiswa
   - `/dashboard/kaprodi/koordinator` — Pengaturan Hak Akses Koordinator Semester
   - *Rute Pembimbing & Operasional Koordinator (bila `is_koordinator === true`)*: `mahasiswa-bimbingan`, `bimbingan-approve`, `laporan-approve`, `revisi-approve`, `periode`, `validasi-proposal`, `pembimbing`, `daftar-penguji`, `jadwal-sidang`, `profile`.
4. **Koordinator Proyek & Internship (`/dashboard/koordinator/*`)**
   - `/dashboard/koordinator` — Dashboard Kontrol Operasional 3-Kolom: Kalender Periode, Penempatan Pembimbing, & Bentrok Sidang (`DB-4`)
   - `/dashboard/koordinator/periode` — Kelola Kalender & Jadwal Periode
   - `/dashboard/koordinator/validasi-proposal` — Validasi Proposal Mahasiswa
   - `/dashboard/koordinator/pembimbing` — Persetujuan Penetapan Pembimbing
   - `/dashboard/koordinator/daftar-penguji` — Daftar Dosen Penguji
   - `/dashboard/koordinator/jadwal-sidang` — Penjadwalan Sidang + Kalender Pelaksanaan Responsif (`PG-4`)
   - *Rute Pembimbing*: `mahasiswa-bimbingan`, `bimbingan-approve`, `laporan-approve`, `revisi-approve`, `profile`.
5. **Dosen Penguji (`/dashboard/penguji/*`)**
   - `/dashboard/penguji` — Dashboard Fokus Hari-H (*Focus Spotlight Card*) & Daftar Sidang (`DB-5`)
   - `/dashboard/penguji/sidang` — Evaluasi Sidang 2-Panel dengan Formulir Tersegmen & Auto-Save Draf (`PG-6`)
   - *Rute Pembimbing*: `mahasiswa-bimbingan`, `bimbingan-approve`, `laporan-approve`, `revisi-approve`, `profile`.
6. **Admin Akademik (`/dashboard/admin/*`)**
   - `/dashboard/admin` — Dashboard Utilitarian & Direktori Cepat Pengguna/Role (`DB-6`)
   - `/dashboard/admin/users` — Manajemen Akun Pengguna
   - `/dashboard/admin/mahasiswa` — Direktori Data Mahasiswa
   - `/dashboard/admin/dosen` — Direktori Data Dosen
   - `/dashboard/admin/pembayaran` — Verifikasi Bukti Pembayaran
   - `/dashboard/admin/proposal` — Administrasi Proposal
   - `/dashboard/admin/laporan` — Administrasi Laporan Sidang
7. **Developer (`/dashboard/developer/*`)**
   - `/dashboard/developer` — Panel Diagnostik, Latensi Endpoint, & Dokumentasi OpenAPI (`DB-7`)

### 1.3 Risk Register & Pemetaan Skill (§9 & §10)

| ID | Risiko | Mitigasi yang Diterapkan | Status |
| --- | --- | --- | --- |
| `R-1` | Regresi fungsi akibat perubahan kontrak API, store auth, atau form field `name`. | `scripts/ui-guard.mjs` membekukan `src/lib/api.js`, `src/store/auth-store.js`, `src/lib/constants.js` (`MENU_CONFIG`), `src/lib/validators.js`, dan `AuthGuard`. Seluruh atribut `name` form dipertahankan. | Terkendali (0 pelanggaran) |
| `R-2` | Inkonsistensi visual antar halaman dan residu kelas kartu lama. | Token semantik terpusat di `src/app/globals.css`, `scripts/slop-check.mjs` memblokir `#hex` mentah, `rounded-2xl`/`3xl`, `backdrop-blur`, `font-black`, dan hue ungu/indigo/violet. | Terkendali (0 temuan) |
| `R-3` | Asumsi fitur dashboard meleset dari data backend. | Setiap widget mencantumkan `data-source` eksplisit (`DD-4`) dan menerapkan degradasi data mentah (`DD-2`) serta `ErrorState` parsial (`DoD-F4`). | Terkendali |

---

## 2. Fase 2 (`DoD-F2`) — Sistem Desain, Token, & Glosarium Akademik (§5 & §6)

- **Dokumen `Docs-Kavana/Frontend-Kavana/DESIGN.md`:** Diperbarui lengkap dengan rasional desain (*Institutional Academic Operations*), tabel token warna Light/Dark, skala tipografi `12px..36px` (`Public Sans` & `JetBrains Mono`), skala spasi `4px..64px` (kelipatan 4px), sistem radius 2-tingkat (`--radius-control: 8px`, `--radius-container: 12px`), dan katalog 11 komponen inti.
- **Glosarium Status Akademik Baku (§5 & `A11Y-4`):**
  - `Menunggu` — Amber (`--status-warning`) + ikon `Clock`
  - `Revisi` — Orange-Amber (`--ctp-peach`) + ikon `RotateCcw` (berbeda bentuk dari `Menunggu`)
  - `ACC` — Emerald (`--status-success`) + ikon `CheckCircle2`
  - `Ditolak` — Rose-Red (`--status-danger`) + ikon `XCircle`
  - `Selesai` — Blue/Green (`--status-info`/`--status-success`) + ikon `CheckCircle2`
- **Format Tanggal & Zona Waktu (§5):** Fungsi `formatAcademicDate()` di `src/lib/status-tone.js` memformat seluruh tanggal ke `"16 Nov 2026"` (atau `"16 Nov 2026, 09:30 WIB"`) dalam zona waktu `Asia/Jakarta` (`WIB`).
- **Penegakan Token (`ENF-1..3`):** `npm run guard` memastikan **0** `#hex` mentah, **0** `rounded-2xl`/`rounded-3xl`/`rounded-[`, dan **0** warna ungu/indigo/violet di seluruh `src/`.

---

## 3. Fase 3 (`DoD-F3`) — App Shell & Navigasi (`NAV-1..8`, `§6.6`)

- **Dimensi Sidebar (`NAV-1`, `§6.6`):**
  - Desktop Expanded: `264px` (`w-[264px]`)
  - Desktop Collapsed (Icon Rail): `72px` (`w-[72px]`)
  - Persistensi status di `localStorage` (`kavana-sidebar-collapsed`) + pintasan keyboard **`Ctrl+B` / `Cmd+B`**.
- **Pengelompokan & Indikator Aktif (`NAV-2..4`, `A11Y-5`):**
  - Grup menu dapat dilipat (*collapsible*) di mode expanded.
  - Saat sebuah grup dilipat atau berada di mode rail `72px` dan memiliki sub-halaman aktif, header grup menampilkan **indikator titik aktif (`data-testid="sidebar-group-active-dot-<id>"`)** serta gaya teks aksen (`NAV-4`).
  - Di mode collapsed `72px`, setiap item menampilkan tooltip saat *hover* maupun *focus* keyboard (`A11Y-5`), dan grup menampilkan *popover flyout* (`NAV-3`).
- **Mobile Drawer (`NAV-5`):** Off-canvas drawer (`<Sheet side="left">`) dengan backdrop dan tombol tutup otomatis saat navigasi.
- **Sticky Header `56px` (`NAV-7..8`, `§6.6`):**
  - Tinggi `56px` (`h-14`), memuat Breadcrumb, **Quick Search (`Ctrl/Cmd+K`)** (`data-testid="quick-search-trigger"` & `data-testid="quick-search-dialog"`), **Multi-Role Switcher** (`data-testid="role-switcher"`) bagi pengguna dengan >1 role aktif, stempel tanggal WIB, Notifikasi, Tema, dan Profil.

---

## 4. Fase 4 (`DoD-F4`) — 7 Dashboard Berbeda Bentuk (`DB-1..7`, `DD-1..4`, `DV-1..3`)

| Role | Pola Struktur (`DB-1..7`) | Sumber Data (`data-source`, `DD-4`) | Degradasi & Partial Failure (`DD-2`, `DoD-F4`) |
| --- | --- | --- | --- |
| **Mahasiswa (`DB-1`)** | Banner status aktif + Hero Progres 5-Tahap + Strip 4 metrik logbook + Checklist Tahapan + Ringkasan Pengajuan. | `mahasiswaAPI.getProfile`, `getBimbinganList`, `getLaporan`, `getSidang`, `getPeriodeStatus` | `widgetErrors` menampilkan `<ErrorState onRetry={loadDashboard} />` per bagian tanpa memblokir widget lain. |
| **Dosen (`DB-2`)** | *High-Density Work Inbox* (filter `Semua`/`Logbook`/`Laporan`, urutan terlama di atas) + Tabel Mahasiswa Bimbingan (`density="compact"`, `44px`). Tanpa grafik dekoratif. | `dosenAPI.getStats`, `getBimbinganList`, `getLaporanList`, `getMahasiswaBimbingan` | `widgetErrors` per bagian dengan tombol *"Coba Lagi"*. |
| **Kaprodi (`DB-3`)** | Strip KPI Kohort + Grafik Distribusi Tahapan & **Tabel Data Pendamping (`DV-2`)** + **Degradasi Data Mentah (`DD-2`)** mahasiswa yang butuh tindak lanjut + Beban Dosen. | `kaprodiAPI.getStats`, `getMahasiswaList`, `getDosenList`, `getPengajuanPembimbing` | Widget degradasi menampilkan daftar mahasiswa langsung dengan catatan *"Ringkasan belum tersedia"*. |
| **Koordinator (`DB-4`)** | **Grid Kontrol Operasional 3-Kolom**: (1) Kalender Periode, (2) Penempatan Pembimbing Belum Lengkap, (3) Jadwal Sidang & Deteksi Bentrok + Grafik Tahapan. | `koordinatorAPI.getStats`, `getJadwalList`, `getMahasiswaList`, `getAllSidang` | Deteksi bentrok dihitung langsung dari data mentah `getAllSidang()`; `ErrorState` parsial aktif. |
| **Penguji (`DB-5`)** | **Focus Spotlight Card** (sesi sidang terdekat/hari ini + CTA langsung *"Isi Form Penilaian"*) + Tabel Daftar Sidang dengan aksi *"Nilai Sidang"*. | `pengujiAPI.getProfile`, `getSidangList` | `ErrorState` dengan retry langsung bila daftar sidang gagal dimuat. |
| **Admin (`DB-6`)** | Strip Metrik Utilitarian + Antrean Verifikasi + **Direktori Cepat Pengguna & Role** (pencarian + filter role langsung di dashboard). | `adminAPI.getStats`, `getUsers`, `getPendingPayments` | Bila `/admin/stats` gagal, `ErrorState` muncul sementara Direktori Cepat Pengguna tetap berfungsi penuh. |
| **Developer (`DB-7`)** | Strip Telemetri Teknis (`font-mono`) + Tabel Latensi Endpoint (`density="compact"`) + Explorer OpenAPI. | `GET /api/docs`, `GET /api/openapi.json` | `ErrorState` dengan retry pada kegagalan probe endpoint. |

---

## 5. Fase 5 (`DoD-F5`) — Halaman Kunci (`PG-1..6`) & Kelengkapan State (`ST-1`)

1. **`PG-1` Daftar Mahasiswa Bimbingan (`/dashboard/dosen/mahasiswa-bimbingan`):**
   - Pola **Tabel + Panel Detail (*Split View*)** (`data-testid="mahasiswa-bimbingan-split-view"` & `data-testid="mahasiswa-detail-pane"`), menampilkan progres 8 slot logbook dan pintasan review tanpa meninggalkan konteks tabel.
2. **`PG-2` Monitoring Progres Mahasiswa (`/dashboard/kaprodi/monitoring`):**
   - Dilengkapi sakelar tampilan **Tabel Status** dan **Board Tahapan** (`data-testid="monitoring-stage-board"`) dengan indikator progres 6 tahap dan pencarian instan.
3. **`PG-3` Logbook & Detail Bimbingan (`/dashboard/mahasiswa/bimbingan`):**
   - Menggunakan **Vertical Timeline** bernomor (`<ol data-testid="bimbingan-logbook-timeline">`) dari `"Bimbingan ke-1"` s.d. `"Bimbingan ke-8"`, tanggal WIB (`"16 Nov 2026"`), serta badge status dengan ikon berbeda (`ACC`, `Menunggu`, `Revisi`).
4. **`PG-4` Penjadwalan Sidang (`/dashboard/koordinator/jadwal-sidang`):**
   - Dilengkapi **Kalender Pelaksanaan Sidang** terkelompok per tanggal (`data-testid="jadwal-kalender-strip"`), deteksi bentrok penguji/ruangan, dan tabel responsif `stackOnMobile` tanpa *horizontal overflow* di `390x844`.
5. **`PG-5` Halaman Persetujuan (`bimbingan-approve`, `laporan-approve`, `revisi-approve`):**
   - Menggunakan glosarium status baku (`ACC`, `Menunggu`, `Revisi`, `Ditolak`), tanggal WIB, *right-side Sheet drawer* untuk pratinjau dokumen/catatan, dialog alasan revisi terikat `<Field>`, dan `<ErrorState onRetry={loadData} />`.
6. **`PG-6` Form Penilaian Sidang (`/dashboard/penguji/sidang`):**
   - Layout 2-panel (dokumen & jadwal di kiri, **Formulir Tersegmen** `data-testid="sidang-segmented-form"` di kanan) dengan **Auto-Save Draf ke `localStorage`** (`data-testid="sidang-autosave-indicator"`) dan **Validasi Inline** (`data-testid="sidang-inline-validation"`).

---

## 6. Fase 6 (`DoD-F6`) — Checklist Anti-AI-Slop (§8), Uji 6 Viewport (§6.6), & Sign-Off

### 6.1 Audit Checklist Anti-AI-Slop (§8)

| Butir Larangan §8 | Pemeriksaan Otomatis & Manual | Hasil |
| --- | --- | --- |
| Tidak ada gradien ungu/indigo/neon (`258°`) | `scripts/slop-check.mjs` + inspeksi `src/app/globals.css` | **LULUS (0 temuan)** |
| Tidak ada kartu glassmorphism / `backdrop-blur` | `scripts/slop-check.mjs` (`backdrop-blur`) | **LULUS (0 temuan)** |
| Tidak ada 4 kartu KPI identik di setiap role | 7 dashboard memiliki struktur berbeda (`DB-1..7`) | **LULUS** |
| Tidak ada angka/persentase tren fiktif (`+12%`) | Seluruh metrik bersumber dari API atau degradasi data mentah (`DD-2..4`) | **LULUS** |
| Tidak ada ilustrasi/avatar generik tak relevan | Hanya menggunakan ikon fungsional `lucide-react` `1.75px` | **LULUS** |
| Tidak ada radius berlebihan (`> 12px`) | Sistem 2-tier `8px`/`12px`; `rounded-2xl`/`3xl` diblokir `slop-check.mjs` | **LULUS (0 temuan)** |
| Tidak ada `#hex` mentah di file komponen | `scripts/slop-check.mjs` (`raw-hex`) | **LULUS (0 temuan)** |
| Tidak ada campuran istilah Inggris-Indonesia | Glosarium §5 diterapkan konsisten (`Menunggu`, `Revisi`, `ACC`, `Ditolak`, `Selesai`) | **LULUS** |

### 6.2 Hasil Verifikasi Otomatis & 6 Viewport Uji (§6.6)

- **Guard & Anti-Slop (`npm run guard`):** Lulus (`ui-guard: clean`, `slop-check: clean — 0 hits across src/`).
- **6 Viewport Uji (`tests/ui/prd-v1-2-compliance.spec.js`):**
  - `1366x768` (Laptop Standar) — Lulus, 0 horizontal body overflow.
  - `1440x900` (Desktop Lebar) — Lulus, 0 horizontal body overflow.
  - `1920x1080` (Monitor Full HD) — Lulus, 0 horizontal body overflow.
  - `1024x768` (Tablet Landscape) — Lulus, 0 horizontal body overflow.
  - `768x1024` (Tablet Portrait) — Lulus, 0 horizontal body overflow.
  - `390x844` (Mobile) & `320x640` (Mobile Sempit) — Lulus, 0 horizontal body overflow.
- **Production Build (`npm run build`):** Lulus tanpa error kompilasi.

### 6.3 Sign-Off Review Independen (§9 & §12 #12)

- **Pelaksana Implementasi:** Main Implementation Agent (`conversation://814df6af-9729-4f31-a3a5-52ccb0d78ccb`)
- **Auditor Independen (1x Final Review):** Independent PRD v1.2 Compliance Reviewer (`conversation://21c025e8-8eac-40a4-846a-fcf184aa537f`) — **VERDICT: APPROVED (0 Blocking Defects)** across `DoD-F1` through `DoD-F6`.
