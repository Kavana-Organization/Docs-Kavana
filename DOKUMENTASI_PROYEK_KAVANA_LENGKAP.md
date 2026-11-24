# DOKUMENTASI LENGKAP ARSITEKTUR, SISTEM, DAN SPESIFIKASI PROYEK KAVANA (KAVANAHUB)

> **Status Dokumen:** Master Reference & Canonical System Specification  
> **Institusi:** Program Studi D4 Teknik Informatika, Sekolah Teknologi Informasi — Universitas Logistik dan Bisnis Internasional (ULBI), Bandung  
> **Disusun Berdasarkan:** Seluruh PRD, Plan Fitur, Desain Sistem, ERD, Security Access Control, dan Dokumentasi Teknis Repository Kavana

---

## 1. RINGKASAN PRODUK & LATAR BELAKANG

### 1.1 Profil Produk
**Kavana (KavanaHub)** adalah sistem platform web terintegrasi yang dirancang untuk mengelola seluruh siklus akademik mata kuliah vokasi berbasis proyek (**Proyek 1, Proyek 2, Proyek 3**) dan magang industri (**Internship 1 dan Internship 2**) pada Program Studi D4 Teknik Informatika ULBI.

Sistem ini menyatukan dan mendigitalkan seluruh tahapan akademik:
* Pendaftaran jalur (*regular* / *RPL*) dan pemilihan *track*.
* Pembentukan kelompok proyek (maksimal 2 mahasiswa) atau pendaftaran individu (internship).
* Pengajuan dan validasi proposal berbasis Google Drive link.
* Penugasan (*plotting*) dosen pembimbing secara manual oleh koordinator.
* Pencatatan logbook bimbingan online (dengan syarat kelayakan minimal 8 kali bimbingan berstatus disetujui/ACC).
* Ekspor resmi formulir bimbingan akademik format PDF ber-kop surat resmi kampus.
* Pengunggahan laporan akhir dan 1 link folder luaran/artefak proyek.
* Penjadwalan sidang otomatis bebas bentrok (*anti-conflict schedule validation*).
* Penilaian sidang dewan penguji dan pembimbing serta penerbitan berita acara.
* Pemantauan perbaikan dan approval revisi pasca-sidang.
* Audit trail, notifikasi lintas peran, dan penguncian periode akademik (*completed lock*).

### 1.2 Masalah yang Diselesaikan
Sebelum sistem Kavana dikembangkan, proses bimbingan akademik menghadapi kendala operasional dan risiko keamanan informasi:
1. **Data Tersebar & Tidak Terdokumentasi:** Progres bimbingan dan draf laporan tersebar di aplikasi perpesanan instan (WhatsApp), spreadsheet pribadi, dan Google Drive personal tanpa jejak audit (*audit trail*).
2. **Kesulitan Pemantauan Progres:** Dosen dan koordinator sulit memantau status aktual mahasiswa bimbingan secara real-time, terutama pada kelompok proyek kemitraan (2 orang).
3. **Potensi Bentrok Jadwal Sidang Tinggi:** Penjadwalan sidang secara manual sering memicu bentrok waktu, ruangan, atau dosen penguji/pembimbing yang bertabrakan pada jam yang sama.
4. **Celah Otorisasi & Keamanan Data (Security Risk):** Dokumen proposal dan penilaian akademik rentan diakses atau dimanipulasi pihak tidak berwenang tanpa mekanisme *role-based access control* (RBAC) dan *data ownership* yang ketat.
5. **Ketiadaan Kontrol Terpusat Kaprodi:** Ketua Program Studi tidak memiliki visibilitas terpusat atas penugasan koordinator multi-semester serta konversi status mahasiswa mengulang (*repeat/parallel*).

### 1.3 Tujuan Produk
1. Menyediakan satu portal tunggal (*single source of truth*) untuk seluruh civitas akademika D4TI ULBI.
2. Menjamin setiap mutasi data akademik memiliki pencatatan riwayat (*immutable audit log*).
3. Menegakkan aturan akademik secara terprogram (*business rules enforcement*) langsung pada lapisan backend API.
4. Mengeliminasi redundansi pengunggahan berkas melalui konsep kelompok agregasi (*group-aggregated submission*).
5. Mencegah manipulasi data kelulusan dengan verifikasi prasyarat bertingkat (*prerequisite gates*).

---

## 2. ARSITEKTUR REPOSITORY & TECH STACK WORKSPACE

Kavana dikelola sebagai **workspace multi-repository independen** (bukan monorepo tunggal), di mana setiap komponen memiliki remote repository tersendiri di GitHub Organization Kavana:

| Direktori Workspace | Nama Repository | Peran & Tanggung Jawab Arsitektur |
| :--- | :--- | :--- |
| `New-Frontend/` | **Frontend-Kavana** | Antarmuka pengguna (Next.js 16 App Router, React 19, Tailwind CSS v4, Radix UI, TanStack Query, Zustand) |
| `nggk-tau-ini-apa/` | **Backend-Kavana** | REST API Engine (Node.js 22/24, Express 5 ESM, Better Auth, mysql2 pool, Upstash Redis, OpenTelemetry) |
| `Test-Kavana/` | **Test-Kavana** | Automation Testing Suite (Selenium WebDriver E2E, Playwright API/UI, Security Regression Test) |
| `Infra-Kavana/` | **Infra-Kavana** | Konfigurasi Docker, Docker Compose (Dev/Prod), Makefile, Reverse Proxy, dan Nginx Environment |
| `Docs-Kavana/` | **Docs-Kavana** | Pusat arsip dokumentasi teknis, user manual, panduan kontribusi, dan laporan QA |

### 2.1 Spesifikasi Teknologi Backend
* **Runtime:** Node.js v22/v24 (ECMAScript Modules / ESM `"type": "module"`).
* **Framework:** Express.js v5.
* **Target Deployment:** Google Cloud Functions (Gen 2) / Cloud Run (`functions.http('kavana', app)`) dan Vercel Serverless (`api/index.js`).
* **Database Utama:** MySQL 8 dengan connection pooling (`mysql2/promise`). Primary Key menggunakan format Custom ID berbasis prefix string terindeks (`MHS`, `DSN`, `BIM`, `JDW`, `SDG`, `KLP`, dll.).
* **Caching & Rate Limiting:** Upstash Redis (Serverless Redis REST) dengan mekanisme fallback in-memory cache saat offline/development.
* **Autentikasi:** Model hybrid Better Auth (Session cookie httpOnly + Bearer plugin) didukung legacy JWT token verification.
* **Observabilitas & Telemetri:** OpenTelemetry (`initTelemetry()`) diinisialisasi pada baris pertama eksekusi sebelum modul lain dimuat.
* **Proteksi Bot & Abuse:** Cloudflare Turnstile CAPTCHA server-side validation.
* **Layanan Eksternal:** Nodemailer / Mailtrap (notifikasi email & OTP), Cloudinary (avatar pengguna), Upstash QStash (asynchronous background queue), Google Drive (penyimpanan dokumen eksternal).

### 2.2 Spesifikasi Teknologi Frontend
* **Framework:** Next.js 16 (App Router) berbasis React 19 (JavaScript murni `.jsx` / `.js`, `jsconfig.json` alias `@/*` -> `src/*`).
* **Styling & Design System:** Tailwind CSS v4 (`@theme inline`), Shadcn UI primitives berbasis Radix UI, Lucide React Icons.
* **State Management & Caching:** Zustand store (`auth-store.js` dengan rehidrasi session storage), TanStack React Query v5.
* **Event-Driven Architecture:** `src/lib/realtime.js` menggerakkan pembaruan antarmuka secara presisi (*targeted refetch*) berdasarkan event mutasi data spesifik, **bukan melalui interval polling berkala** yang membebani server.
* **Sistem Notifikasi:** Sonner toasts wrapper (`src/lib/alert.js`).

---

## 3. IDENTIFIKASI PERAN (7 ROLES) & HIERARKI AKSES KONTROL (RBAC)

Kavana menerapkan pemisahan tugas secara tegas (*Separation of Duties*) yang membedakan **Kewenangan Akademik** dan **Kewenangan Administratif/Teknis**.

### 3.1 Hierarki Otorisasi (`middleware/role.js`)
1. **Developer (`developer`):** Superuser teknis tingkat tertinggi. Melewati seluruh pemeriksaan otorisasi (`requireRole`), termasuk rute eksklusif kaprodi. Terikat pada penguncian perangkat fisik (*device-locked*).
2. **Admin (`admin`):** Superuser administratif. Memiliki hak penuh atas data akun pengguna, master data dosen, log audit, dan monitoring sistem. **Secara eksplisit dilarang mengeksekusi keputusan akademik** (dikecualikan oleh rute `kaprodiOnly()`).
3. **Kepala Program Studi (`kaprodi`):** Otoritas akademik tertinggi. Mewarisi seluruh rute dosen dan koordinator. Berwenang menetapkan koordinator multi-semester, mengelola periode, mengubah status mengulang, dan konversi track.
4. **Koordinator Track (`koordinator`):** Dosen dengan tugas tambahan mengelola track/semester spesifik. Mewarisi rute dosen. Berwenang memvalidasi proposal, plotting pembimbing, menjadwalkan sidang anti-bentrok, dan monitoring revisi.
5. **Dosen Penguji (`penguji`):** Dosen yang ditugaskan dalam majelis sidang. Berwenang mengakses jadwal sidang yang diuji, menginput nilai komponen mutu (0–100), menetapkan keputusan kelulusan, dan memberikan catatan revisi. Mewarisi rute dosen pembimbing.
6. **Dosen Pembimbing (`dosen`):** Tenaga pengajar akademik. Berwenang melihat mahasiswa bimbingannya, menyetujui/menolak logbook bimbingan, menyetujui laporan akhir sidang, dan menyetujui lembar revisi pasca-sidang.
7. **Mahasiswa (`mahasiswa`):** Role dengan hak akses paling terbatas. Menerapkan isolasi kepemilikan data (*Data Ownership*): hanya dapat membaca dan memodifikasi entitas miliknya sendiri. Tidak memiliki hak hapus (*no delete permission*).

---

## 4. ALUR BISNIS AKADEMIK UTAMA (END-TO-END WORKFLOW)

### 4.1 Registrasi Akun, Validasi Email & Login Keamanan
1. Mahasiswa mendaftar mandiri via form registrasi dengan mengisi Nama, NPM, Email Institusi, WhatsApp, Angkatan, Jalur (*regular* / *RPL*), dan Password.
2. Sistem memvalidasi keabsahan format email: domain kampus, struktur local-part, dan secara otomatis menolak penyedia email sementara (*disposable/temp mail*).
3. Sistem membangkitkan 6 digit One-Time Password (OTP) yang dikirimkan via email dengan masa kedaluwarsa 10 menit dan pembatasan frekuensi pengiriman (*throttle*).
4. Setelah verifikasi OTP berhasil, akun mahasiswa diaktifkan.
5. Pada saat login, sistem memverifikasi token Cloudflare Turnstile untuk memastikan interaksi manusia dan menolak automated credential stuffing.
6. Backend memvalidasi kredensial (bcrypt hash) dan menerbitkan sesi autentikasi serta menetapkan dashboard tujuan sesuai role.

### 4.2 Pemilihan Track, Kelompok Proyek & Usulan Pembimbing
1. Mahasiswa memilih track aktif sesuai semester dan jalur akademik:
   * **Proyek 1 (Semester 2):** Aplikasi implementor, nilai tambah CRUD, draft buku, dan poster.
   * **Proyek 2 (Semester 3):** Aplikasi Code Coverage CRUD RDBMS, draft jurnal, laporan metodologi riset, dan poster.
   * **Proyek 3 (Semester 5):** Web Services, dokumentasi API (Swagger/Postman), buku tutorial (bebas penerbit), draft jurnal, dan poster.
   * **Internship 1 (Semester 7) & Internship 2 (Semester 8):** Magang industri mandiri dengan luaran laporan industri dan sertifikasi.
2. **Aturan Kelompok Proyek:**
   * Proyek 1, 2, dan 3 beranggotakan **maksimal 2 mahasiswa** (1 Ketua dan 1 Anggota).
   * Internship dikerjakan secara **individual (1 mahasiswa)**.
   * Pembentukan kelompok dilakukan via kode undangan (*group invite code*).
3. **Pengajuan Proposal:**
   * Proposal diunggah sebagai tautan dokumen Google Drive (berisi latar belakang, rumusan masalah, dan metodologi).
   * Untuk proyek kelompok, **proposal cukup diunggah 1 kali oleh salah satu anggota kelompok**. Anggota kelompok lainnya secara otomatis terhubung pada proposal yang sama (*group aggregated*).
   * Mahasiswa memilih 1 dosen usulan pembimbing (untuk proyek) atau 2 dosen usulan pembimbing (untuk internship).

### 4.3 Validasi Proposal & Penugasan Pembimbing Manual
1. Koordinator track mengakses daftar proposal yang masuk **hanya untuk track yang ditugaskan kepadanya** (*scoped track access*). Koordinator Proyek 1 tidak dapat mengakses proposal Proyek 3.
2. Koordinator memeriksa dokumen proposal, kesesuaian topik, dan riwayat mahasiswa (reguler vs mengulang).
3. Koordinator mengambil keputusan: **Setujui (Approved)** atau **Tolak (Rejected)** disertai catatan perbaikan.
4. **Penugasan Dosen Pembimbing (Supervisor Plotting):**
   * Penugasan dosen pembimbing **wajib dilakukan secara manual oleh koordinator**, tidak boleh ditetapkan otomatis oleh sistem.
   * Koordinator mempertimbangkan usulan mahasiswa dan kuota bimbingan dosen yang sedang aktif (maksimal 8 kelompok per dosen).
   * Penetapan pembimbing pada kelompok otomatis berlaku untuk kedua mahasiswa di dalam kelompok tersebut.

### 4.4 Bimbingan Online & Logbook Aggregation
1. Mahasiswa yang proposal dan pembimbingnya telah disetujui berhak mencatat sesi bimbingan mingguan.
2. **Aturan Penambahan Sesi Bimbingan:**
   * Tanggal sesi bimbingan tidak boleh terjadi sebelum tanggal proposal disetujui / pembimbing diplot.
   * Tanggal bimbingan tidak boleh melampaui batas akhir periode akademik yang sedang berjalan.
   * Apabila status periode akademik telah ditandai **"Completed / Terkunci"**, backend menolak penambahan sesi bimbingan baru.
   * Untuk kelompok proyek, **cukup salah satu anggota menginput catatan bimbingan**, dan sesi tersebut otomatis tercatat untuk kedua mahasiswa.
3. **Review & Approval Dosen Pembimbing:**
   * Dosen memeriksa topik bahasan, progres kemajuan, dan tautan berkas revisi pada Google Drive.
   * Dosen memutuskan **Setujui (Approved)** atau **Tolak (Rejected) dengan catatan**.
   * Sesi yang ditolak **tidak dihitung dalam progres kumulatif kelayakan sidang**. Mahasiswa wajib mengajukan sesi bimbingan pengganti.
   * **Threshold Prasyarat Sidang:** Mahasiswa wajib mengumpulkan **minimal 8 sesi bimbingan berstatus Approved**.

### 4.5 Pengunggahan Laporan Akhir & Verifikasi Kelayakan Sidang
1. Mahasiswa yang telah memenuhi ambang batas 8 bimbingan approved berhak membuka modul pengunggahan berkas laporan sidang.
2. Mahasiswa mengisi:
   * Tautan laporan sidang lengkap (PDF Google Drive).
   * Satu link folder Google Drive yang memuat seluruh artefak luaran (source code, API doc, video demo, buku tutorial/jurnal).
3. Dosen pembimbing meninjau draf laporan akhir dan luaran proyek.
4. Dosen menyetujui laporan akhir sebagai rekomendasi resmi bahwa mahasiswa layak menempuh ujian sidang.

### 4.6 Penjadwalan Sidang Anti-Bentrok (Anti-Conflict Validation)
1. Koordinator membuka modul penjadwalan sidang. Sistem secara otomatis menyaring kandidat: **hanya mahasiswa yang memiliki 8 bimbingan ACC dan laporan akhir berstatus Approved yang muncul di daftar kandidat**.
2. Koordinator menentukan Tanggal, Jam Mulai, Jam Selesai, Ruangan Sidang (atau tautan virtual meeting), Dosen Penguji 1 (Ketua Penguji), dan Dosen Penguji 2.
3. **Validasi Anti-Bentrok Sistemik (`SIDANG_CONFLICT_VALIDATION_PLAN.md`):**
   Backend melakukan verifikasi silang pada rentang waktu (Jam Mulai s/d Jam Selesai):
   * Menolak jadwal jika **Dosen Pembimbing** telah memiliki jadwal menguji/membimbing di ruangan/kelompok lain pada waktu yang sama.
   * Menolak jadwal jika **Dosen Penguji 1 atau Penguji 2** telah terjadwal di sidang lain pada waktu yang sama.
   * Menolak jadwal jika **Ruangan Sidang** telah dialokasikan untuk sidang kelompok lain pada waktu yang sama.
4. Jadwal yang lolos validasi otomatis dipublikasikan ke kalender sidang dosen dan dashboard mahasiswa.

### 4.7 Penilaian Sidang & Penetapan Hasil Akhir
1. Majelis dewan penguji (Penguji 1, Penguji 2, dan Dosen Pembimbing) melaksanakan sidang evaluasi.
2. Masing-masing penguji memasukkan nilai angka (skala 0–100) pada 4 komponen rubrik:
   * Presentasi & Sikap Profesional (Bobot 20%).
   * Penguasaan Materi & Pemahaman Teori (Bobot 30%).
   * Kualitas Produk / Kode / Web Services (Bobot 30%).
   * Format & Kelengkapan Penulisan Dokumen (Bobot 20%).
3. Sistem mengkalkulasi Nilai Akhir Mutu:
   * Komposisi Nilai: (Nilai Pembimbing x 40%) + (Rata-rata Nilai Penguji x 60%).
   * Konversi Huruf Mutu: >= 85 (A), 80–84 (AB), 70–79 (B), 65–69 (BC), 55–64 (C), 40–54 (D), < 40 (E).
4. Keputusan Dewan Penguji: **Lulus Murni**, **Lulus dengan Catatan Revisi**, atau **Tidak Lulus (Sidang Ulang)**.

### 4.8 Monitoring Revisi Pasca-Sidang
1. Mahasiswa dengan hasil "Lulus Revisi" menerima daftar catatan perbaikan dari dewan penguji.
2. Batas waktu penyelesaian revisi ditetapkan **maksimal 14 hari kalender**.
3. Mahasiswa mengunggah berkas laporan yang telah diperbaiki ke Google Drive dan mengajukan verifikasi.
4. Dosen penguji dan pembimbing memeriksa berkas perbaikan dan memberikan approval revisi digital.

### 4.9 Ekspor Formulir Bimbingan Resmi (PDF Generation)
* Mahasiswa dan dosen dapat mengunduh formulir rekapitulasi bimbingan resmi berformat PDF.
* Layout dokumen mengikuti standar baku D4 Teknik Informatika ULBI:
  * Kop resmi universitas dengan logo ULBI resolusi tinggi.
  * Judul dokumen dinamis sesuai track (contoh: *"FORMULIR BIMBINGAN KEGIATAN PROYEK 3"*).
  * Tabel identitas mahasiswa, judul proyek, kelompok, dan dosen pembimbing.
  * Rekapitulasi 8 sesi bimbingan lengkap dengan tanggal, materi bahasan, catatan dosen, dan tanda tangan digital QR Code.
  * QR Code digital signature diverifikasi menggunakan algoritma kriptografi HMAC-SHA256 yang tertaut pada endpoint verifikasi keaslian dokumen.

---

## 5. ACCESS CONTROL MATRIX (ACM) & ATURAN CRUD KAVANA

Tabel matriks hak akses berikut merupakan aturan definitif yang berlaku pada antarmuka (UI) dan ditegakkan pada lapisan backend API:

| Entitas / Fitur Resource | Mahasiswa | Dosen Pembimbing | Penguji | Koordinator | Kaprodi | Admin | Developer |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Profil & Biodata Pribadi** | R, U (Milik Sendiri) | R, U (Milik Sendiri) | R, U | R, U | R, U | R, U | R, U |
| **Track & Jalur Akademik** | R, U (Saat Belum Dikunci) | R | — | R, U | R, U | R, U | CRUD* |
| **Kelompok Proyek (Group)** | C, R (Milik Sendiri) | R | — | R, U | R, U | R, U | CRUD* |
| **Proposal Proyek (Drive Link)** | C, R (Milik Sendiri) | — | — | R, U (Track-nya) | R, U | R, U | CRUD* |
| **Plotting Pembimbing** | — | — | — | C, R, U | C, R, U | — | CRUD* |
| **Logbook Bimbingan (Sesi 1–8)**| C, R (Milik Sendiri) | R, U (ACC Sesi) | — | R | R | — | CRUD* |
| **Laporan Akhir Sidang** | C, R (Milik Sendiri) | R, U (Rekomendasi) | R | R | R | — | CRUD* |
| **Penjadwalan Sidang** | R (Jadwal Sendiri) | R (Jadwal Bimbingan)| R (Jadwal Diuji) | C, R, U | C, R, U | — | CRUD* |
| **Nilai & Hasil Sidang** | R (Setelah Terbit) | R, U (Input Nilai) | R, U (Input Nilai)| R, U | C, R, U | — | CRUD* |
| **Lembar Revisi Sidang** | C, R (Milik Sendiri) | R, U (ACC Revisi) | R, U (ACC Revisi)| R | R | — | CRUD* |
| **Periode Akademik** | R | R | — | C, R, U (Track-nya)| C, R, U (Semua)| — | CRUD* |
| **Penugasan Koordinator** | — | — | — | — | C, R, U, D | — | CRUD* |
| **Manajemen User & Password** | — | — | — | — | — | R, U, D | CRUD* |
| **Audit Trail & Log Aktivitas** | — | — | — | — | — | R | R |
| **System Health & Config** | — | — | — | — | — | — | R, U, D |
| **Device Lock & Hardware Key** | — | — | — | — | — | — | R, U, D |

*Keterangan Aturan Matriks:*
* **Append-Only Academic Record:** Tidak ada peran yang memiliki hak **Delete (D)** terhadap proposal, bimbingan, laporan, nilai, atau jadwal sidang. Seluruh data akademik bersifat permanen (*append-only* with status change) guna melindungi integritas rekonsiliasi audit.
* **Hak Delete Terbatas:** Hak Delete hanya diberikan secara ketat kepada:
  1. **Admin:** Menghapus akun pengguna yang tidak valid.
  2. **Kaprodi:** Mencabut penugasan koordinator track.
  3. **Developer:** Mengosongkan cache Redis dan mencabut otorisasi perangkat hardware.
* **Isolasi Kepemilikan (Data Ownership):** Akses mahasiswa pada seluruh data dibatasi dengan klausul verifikasi `req.user.id === data.mahasiswa_id`. Mahasiswa yang mencoba membaca data mahasiswa lain langsung ditolak dengan respon **HTTP 403 Forbidden**.

---

## 6. KETENTUAN KHUSUS JALUR AKADEMIK & MAHASISWA MENGULANG

Sistem mengakomodasi kompleksitas jalur akademik D4TI ULBI:

### 6.1 Jalur Reguler vs Jalur RPL
* **Mahasiswa Reguler:** Mengikuti tahapan berurutan sesuai semester normal kurikulum (Semester 2: Proyek 1, Semester 3: Proyek 2, Semester 5: Proyek 3, Semester 7: Internship 1, Semester 8: Internship 2). Mahasiswa reguler tidak dapat melompati track sebelum track prasyarat dinyatakan lulus.
* **Mahasiswa Rekognisi Pembelajaran Lampau (RPL):** Memiliki fleksibilitas pengambilan track berdasarkan hasil konversi sks akademik oleh Kaprodi. Mahasiswa RPL dapat langsung mengambil Proyek 2 atau Proyek 3 tanpa terikat semester reguler.

### 6.2 Mekanisme Mahasiswa Mengulang (Repeat Flow)
* Mahasiswa yang dinyatakan tidak lulus pada suatu track ditandai dengan status `repeat_required`.
* Mahasiswa reguler yang mengulang tetap diwajibkan mendaftar pada periode aktif yang membuka track tersebut.
* **Fleksibilitas Periode RPL:** Mahasiswa reguler yang mengulang diizinkan mengambil track ulang pada periode yang dibuka untuk mahasiswa RPL apabila jadwal semester regulernya tidak membuka track bersangkutan.
* **Alur Paralel (Parallel Repeat Flow):** Kaprodi dan koordinator dapat memberikan dispensasi kepada mahasiswa untuk mengambil track mengulang secara paralel bersamaan dengan track semester berjalan (contoh: Mengulang Proyek 1 secara paralel bersamaan dengan menempuh Proyek 2) apabila memenuhi syarat beban sks akademik.

---

## 7. ARSITEKTUR KEAMANAN, DEVICE LOCKING & PENANGANAN INSIDEN

### 7.1 Keamanan Multi-Lapis (Defense-in-Depth)
1. **Lapisan Jaringan & Edge:** Cloudflare Turnstile CAPTCHA mencegah brute force dan bot traffic pada endpoint otentikasi. Helmet middleware menegakkan Content Security Policy (CSP) dan HTTP Strict Transport Security (HSTS).
2. **Lapisan Batas Frekuensi (Rate Limiting):** Upstash Redis rate limiter mengisolasi tiga zona:
   * Zona Autentikasi: Pembatasan ketat untuk mencegah serangan penipisan kata sandi.
   * Zona Admin/Developer: Pembatasan operasional khusus.
   * Zona Publik/API Umum: Perlindungan terhadap banjir permintaan (*traffic flooding*).
3. **Lapisan Otorisasi Aplikasi:** Setiap endpoint memverifikasi autentikasi identitas pengguna, hierarki peran (`requireRole`), dan relasi kepemilikan data sebelum menjalankan query database.
4. **Lapisan Validasi Data:** Validasi skema input yang ketat menggunakan Zod pada frontend dan backend guna mencegah SQL Injection dan Cross-Site Scripting (XSS).

### 7.2 Fitur Keamanan Khusus Role Developer (Device-Locking)
Role developer memiliki akses teknis menyeluruh ke database dan cache. Untuk mencegah pengambilalihan akun akibat kebocoran kredensial, role developer dilindungi mekanisme **Device Binding**:
* Akun developer tidak dapat melakukan login hanya dengan memasukkan email dan password.
* Sistem memeriksa **Hardware Fingerprint / MAC Hash Token** perangkat yang melakukan request terhadap daftar perangkat yang terdaftar resmi di tabel `developer_devices`.
* Akses dari perangkat yang tidak dikenal akan langsung diblokir dan memicu log peringatan keamanan berprioritas tinggi.

### 7.3 Pencatatan Jejak Audit (Audit Logging)
Seluruh aksi mutasi data penting dicatat pada tabel `audit_logs` yang mencakup:
* Identitas aktor (`actor_id` dan peran aktif).
* Kategori aksi (misal: `PROPOSAL_VALIDATED`, `SUPERVISOR_ASSIGNED`, `SIDANG_SCHEDULED`, `PASSWORD_RESET`).
* Target entitas dan ID data terkait.
* Alamat IP pengakses dan informasi User-Agent peramban.
* Status eksekusi (*SUCCESS* / *BLOCKED_403*).

---

## 8. STRUKTUR DATABASE & ENTITAS UTAMA (ERD OVERVIEW)

Basis data relasional MySQL `bimbingan_online` terdiri atas 19 tabel utama yang dinormalisasi:

1. **`account` & `session`:** Manajemen akun autentikasi terpusat Better Auth.
2. **`mahasiswa`:** Profil data pokok mahasiswa (NPM, nama, kelas, angkatan, jalur, status mengulang, dsb.).
3. **`dosen` & `dosen_role`:** Profil tenaga pengajar dan tabel pivot penugasan multi-peran (seorang dosen dapat memegang peran dosen pembimbing, penguji, koordinator, dan kaprodi sekaligus).
4. **`jadwal_proyek`:** Pengaturan periode akademik, semester ajaran, scope jalur (reguler/RPL), tanggal pembukaan/penutupan bimbingan dan sidang.
5. **`kelompok` & `kelompok_anggota`:** Pengelompokan mahasiswa proyek (maksimal 2 anggota) beserta kode invite kelompok.
6. **`proposal` & `dosen_pembimbing`:** Dokumen usulan judul proyek, tautan Google Drive, status verifikasi, dan penugasan resmi pembimbing.
7. **`bimbingan` & `bimbingan_signature`:** Rekam jejak logbook mingguan sesi 1 sampai 8, catatan mahasiswa, approval dosen, dan tanda tangan digital QR Code.
8. **`laporan_sidang`:** Berkas laporan akhir, satu link Google Drive untuk seluruh luaran/artefak, dan status approval pembimbing.
9. **`sidang` & `sidang_penguji`:** Penjadwalan sidang anti-bentrok, alokasi ruangan, majelis penguji, komponen nilai, dan berita acara hasil ujian.
10. **`revisi_sidang`:** Catatan perbaikan pasca-sidang, tautan berkas perbaikan, tenggat waktu H+14, dan status verifikasi revisi.
11. **`audit_logs` & `auth_trackers`:** Pencatatan jejak audit aktivitas sistem, histori login, dan telemetri keamanan.
12. **`developer_devices`:** Registrasi dan hashing token perangkat hardware untuk penguncian akses role developer.

---

## 9. PEDOMAN INSTALASI & MENJALANKAN SISTEM SECARA LOKAL

### 9.1 Menjalankan Backend API (`nggk-tau-ini-apa/`)
```bash
cd nggk-tau-ini-apa
npm install
# Pastikan konfigurasi .env telah disesuaikan (DB_HOST, DB_USER, DB_PASSWORD, JWT_SECRET, dll.)
npm run dev      # Menjalankan server lokal via local.js pada PORT 3000
npm start        # Menjalankan Functions Framework target kavana (GCF simulator)
```

### 9.2 Menjalankan Frontend Next.js (`New-Frontend/`)
```bash
cd New-Frontend
npm install
# Konfigurasi .env.local dengan NEXT_PUBLIC_API_BASE_URL=http://localhost:3000/api
npm run dev      # Menjalankan server development Next.js pada http://localhost:3001 atau :3000
npm run build    # Kompilasi build production
npm run lint     # Pengecekan standar kode ESLint
```

### 9.3 Menjalankan Full-Stack via Docker Compose (`Infra-Kavana/`)
```bash
cd Infra-Kavana
make up          # Menjalankan seluruh kontainer backend, frontend, database MySQL, dan Redis
make dev         # Menjalankan lingkungan lokal dengan fitur hot-reload aktif
make health      # Memeriksa status kesehatan seluruh kontainer layanan
make down        # Menghentikan seluruh kontainer infrastruktur
```

### 9.4 Menjalankan Testing Otomatis (`Test-Kavana/`)
```bash
cd Test-Kavana
npm install
npm test                       # Menjalankan seluruh test runner otomatis
npm run test:security          # Menjalankan pengujian khusus RBAC, autentikasi, dan validasi bypass
npm run test:academic:bimbingan# Menjalankan pengujian fungsional alur bimbingan online
npm run test:headless          # Menjalankan pengujian browser dalam mode headless
```

---

## 10. KESIMPULAN SISTEM

Proyek **KAVANA (KavanaHub)** dirancang secara komprehensif untuk memadukan keunggulan **efisiensi proses akademik** dan **ketatnya standar keamanan sistem informasi**. 

Melalui penerapan arsitektur berlapis (*Defense-in-Depth*), pemisahan kewenangan akademik-teknis yang kaku, validasi anti-bentrok cerdas, agregasi bimbingan berbasis kelompok, serta penegakan hak akses pada tingkat antarmuka maupun backend API, Kavana menjadi platform akademik modern yang andal, transparan, dan tahan terhadap manipulasi data di lingkungan Universitas Logistik dan Bisnis Internasional (ULBI).
