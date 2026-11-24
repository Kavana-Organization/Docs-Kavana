# KAVANA MASTER DOCUMENTATION (COMPLETE & UNABRIDGED)

> Dokumen ini berisi kompilasi seluruh dokumentasi, Product Requirement Document (PRD), rencana teknis, arsitektur keamanan, proses bisnis, desain sistem, dan pedoman operasional proyek Kavana (KavanaHub) secara lengkap tanpa ada teks yang dikurangi atau dipersingkat.

> **Target Penggunaan:** Sumber basis pengetahuan lengkap untuk AI, NotebookLM, dan referensi kanonikal pengembang.

---

## DAFTAR ISI DOKUMEN KOMPILASI

1. [PRD UTAMA KAVANA (prd.md)](#prd-utama-kavana-prd-md-)
2. [AGENTS & REPOSITORY GUIDELINES (AGENTS.md)](#agents-repository-guidelines-agents-md-)
3. [TUGAS DAN ANALISIS ACCESS CONTROL (Tugas_Access_Control_Kavana.md)](#tugas-dan-analisis-access-control-tugas-access-control-kavana-md-)
4. [DEVELOPER ROLE & DEVICE LOCKING PLAN (DEVELOPER_ROLE_ACCESS_PLAN.md)](#developer-role-device-locking-plan-developer-role-access-plan-md-)
5. [SIDANG CONFLICT VALIDATION PLAN (SIDANG_CONFLICT_VALIDATION_PLAN.md)](#sidang-conflict-validation-plan-sidang-conflict-validation-plan-md-)
6. [SIDANG PREREQUISITE PLAN (SIDANG_PREREQUISITE_PLAN.md)](#sidang-prerequisite-plan-sidang-prerequisite-plan-md-)
7. [SIDANG REVISION FLOW PLAN (SIDANG_REVISION_FLOW_PLAN.md)](#sidang-revision-flow-plan-sidang-revision-flow-plan-md-)
8. [REPEAT & PARALLEL ACADEMIC FLOW PLAN (REPEAT_PARALLEL_FLOW_PLAN.md)](#repeat-parallel-academic-flow-plan-repeat-parallel-flow-plan-md-)
9. [KOORDINATOR MULTI-ASSIGNMENT PLAN (KOORDINATOR_MULTI_ASSIGN_PLAN.md)](#koordinator-multi-assignment-plan-koordinator-multi-assign-plan-md-)
10. [BIMBINGAN EXPORT PDF & WORD PLAN (BIMBINGAN_EXPORT_PDF_WORD_PLAN.md)](#bimbingan-export-pdf-word-plan-bimbingan-export-pdf-word-plan-md-)
11. [CLOUDFLARE TURNSTILE LOGIN PLAN (CLOUDFLARE_TURNSTILE_LOGIN_PLAN.md)](#cloudflare-turnstile-login-plan-cloudflare-turnstile-login-plan-md-)
12. [EMAIL NOTIFICATION GAP PLAN (EMAIL_NOTIFICATION_GAP_PLAN.md)](#email-notification-gap-plan-email-notification-gap-plan-md-)
13. [EMAIL REGISTER & VALIDATION PLAN (EMAIL_REGISTER_VALIDATION_PLAN.md)](#email-register-validation-plan-email-register-validation-plan-md-)
14. [EVENT-DRIVEN REALTIME REFRESH PLAN (EVENT_DRIVEN_REALTIME_REFRESH_PLAN.md)](#event-driven-realtime-refresh-plan-event-driven-realtime-refresh-plan-md-)
15. [PROSES BISNIS BACKEND (Docs-Kavana/Backend-Kavana/PROSES_BISNIS.md)](#proses-bisnis-backend-docs-kavana-backend-kavana-proses-bisnis-md-)
16. [SETUP BACKEND & DATABASE (Docs-Kavana/Backend-Kavana/SETUP.md)](#setup-backend-database-docs-kavana-backend-kavana-setup-md-)
17. [FRONTEND DESIGN SYSTEM & UI/UX ARCHITECTURE (Docs-Kavana/Frontend-Kavana/DESIGN.md)](#frontend-design-system-ui-ux-architecture-docs-kavana-frontend-kavana-design-md-)
18. [LAPORAN IMPLEMENTASI PRD FRONTEND (Docs-Kavana/Frontend-Kavana/LAPORAN_IMPLEMENTASI_PRD_v1.2.md)](#laporan-implementasi-prd-frontend-docs-kavana-frontend-kavana-laporan-implementasi-prd-v1-2-md-)
19. [INFRASTRUKTUR DOCKER (Docs-Kavana/Infra-Kavana/DOCKER.md)](#infrastruktur-docker-docs-kavana-infra-kavana-docker-md-)
20. [TESTING & QA SPECIFICATIONS (Docs-Kavana/Test-Kavana/README.md)](#testing-qa-specifications-docs-kavana-test-kavana-readme-md-)

---

## 1. PRD UTAMA KAVANA (prd.md)

> **Sumber Berkas Asli:** `prd.md`

# PRD Kavana - Sistem Bimbingan Online D4 Teknik Informatika

## 1. Ringkasan Produk

Kavana adalah sistem bimbingan online untuk Program Studi D4 Teknik Informatika ULBI yang mengelola proses akademik mahasiswa pada track Proyek 1, Proyek 2, Proyek 3, Internship 1, dan Internship 2. Sistem ini menyatukan proses pemilihan track, pembentukan kelompok, upload proposal, validasi proposal, assign pembimbing, bimbingan online, upload laporan sidang, penjadwalan sidang, penilaian, revisi sidang, notifikasi, dan monitoring lintas role.

Kavana dibuat untuk menggantikan proses manual yang tersebar di chat, spreadsheet, file drive pribadi, dan koordinasi lisan. Target utamanya adalah membuat semua proses bimbingan terdokumentasi, mudah dipantau, memiliki status yang jelas, dan dapat dikendalikan oleh mahasiswa, dosen, koordinator, kaprodi, serta admin dalam satu sistem.

Produk ini menggunakan frontend berbasis Next.js dan backend Express.js yang berjalan di Google Cloud Functions/Cloud Run. Database utama menggunakan MySQL, sedangkan Redis digunakan sebagai cache, rate limiter, throttle OTP, token revocation, dan penyimpanan state sementara. Sistem juga mendukung Cloudflare Turnstile untuk proteksi login, email notification, Swagger API documentation, serta export PDF untuk formulir bimbingan.

## 2. Latar Belakang Masalah

Proses bimbingan akademik pada proyek dan internship memiliki banyak tahapan yang saling bergantung. Mahasiswa harus memilih track, membuat kelompok, mengajukan proposal, menunggu validasi koordinator, menunggu assign pembimbing, melakukan bimbingan minimal sesuai ketentuan, mengunggah laporan, menunggu approval, kemudian masuk ke tahap sidang dan revisi bila diperlukan. Tanpa sistem terpusat, status tiap mahasiswa sulit dipantau secara real time.

Masalah utama yang ingin diselesaikan:

- Mahasiswa tidak punya satu tempat untuk melihat progres proposal, bimbingan, laporan, sidang, dan revisi.
- Dosen sulit memantau mahasiswa bimbingan, terutama bila ada kelompok proyek berisi dua mahasiswa.
- Koordinator sulit membatasi akses proposal berdasarkan track yang ia tangani.
- Kaprodi membutuhkan kontrol atas assign koordinator, periode, dan monitoring mahasiswa.
- Admin membutuhkan pengelolaan user, dosen, password, status akun, log aktivitas, dan audit sistem.
- Track regular dan RPL memiliki aturan akademik yang berbeda dan perlu dikelola secara eksplisit.
- Mahasiswa yang mengulang perlu flow khusus, termasuk kemungkinan parallel dengan track berjalan.
- Jadwal sidang harus mencegah bentrok tanggal, jam, ruangan, pembimbing, dan penguji.
- Notifikasi akademik perlu dikirim ke pengguna yang relevan agar status tidak terlewat.
- Export dokumen bimbingan perlu mengikuti template resmi kampus.

## 3. Tujuan Produk

Tujuan utama Kavana:

- Menyediakan sistem bimbingan online terpusat untuk mahasiswa, dosen, koordinator, kaprodi, dan admin.
- Menjamin setiap proses akademik memiliki status, riwayat, dan audit trail.
- Mengurangi proses manual yang rawan hilang, salah input, atau tidak sinkron.
- Membantu koordinator dan kaprodi memantau progres mahasiswa berdasarkan track, periode, jalur, dan status akademik.
- Mendukung perbedaan jalur regular dan RPL, termasuk flow mahasiswa mengulang.
- Membuat proses proposal, bimbingan, laporan, sidang, dan revisi lebih terstruktur.
- Menyediakan mekanisme notifikasi aplikasi dan email untuk perubahan penting.
- Menyediakan dokumen bimbingan PDF sesuai template resmi.

## 4. Sasaran Pengguna

### 4.1 Mahasiswa

Mahasiswa menggunakan sistem untuk memilih track, membuat atau bergabung kelompok, mengunggah proposal, mengajukan dosen pembimbing, mencatat bimbingan, mengunggah laporan sidang, melihat jadwal sidang, melihat nilai akhir, mengelola revisi, dan melakukan export PDF bimbingan.

### 4.2 Dosen

Dosen menggunakan sistem untuk melihat mahasiswa bimbingan, menyetujui atau menolak catatan bimbingan, menyetujui atau menolak laporan sidang, melihat jadwal sidang, memberikan catatan akademik, dan memproses revisi sidang.

### 4.3 Koordinator

Koordinator menggunakan sistem untuk mengelola periode, memvalidasi proposal sesuai track assignment, assign pembimbing, melihat daftar mahasiswa, menjadwalkan sidang, memonitor revisi, dan menjalankan fungsi dosen pembimbing bila ia juga menjadi pembimbing.

### 4.4 Kaprodi

Kaprodi menggunakan sistem untuk mengelola koordinator, melihat daftar dosen, memonitor mahasiswa, mengelola periode, memvalidasi proposal bila diperlukan, assign pembimbing, menjadwalkan sidang, dan menjalankan fungsi koordinator atau dosen bila memiliki akses terkait.

### 4.5 Admin

Admin menggunakan sistem untuk mengelola user, mengelola dosen, mengubah password user, mengaktifkan atau menonaktifkan akun, melihat log aktivitas, melihat audit log, melihat monitoring sistem, dan mengakses laporan sistem.

### 4.6 Developer

Role developer direncanakan sebagai super-role dengan akses penuh di atas admin dan kaprodi. Role ini hanya boleh diakses oleh identitas yang terdaftar secara aman melalui konfigurasi secret dan validasi perangkat. Detail implementasi developer role harus mengikuti dokumen rencana khusus agar tidak membuka celah keamanan.

## 5. Scope Produk

### 5.1 In Scope

- Landing page publik Kavana.
- Halaman login, register, lupa password, OTP, dan validasi email.
- Dashboard per role: mahasiswa, dosen, koordinator, kaprodi, admin.
- Pemilihan track Proyek 1, Proyek 2, Proyek 3, Internship 1, dan Internship 2.
- Jalur mahasiswa: regular dan RPL.
- Flow mahasiswa mengulang dan parallel repeat.
- Kelompok proyek maksimal dua anggota.
- Internship satu mahasiswa per kegiatan.
- Upload proposal berbasis link Google Drive.
- Usulan pembimbing oleh mahasiswa.
- Validasi proposal oleh koordinator sesuai track assignment.
- Assign pembimbing manual oleh koordinator.
- Bimbingan online maksimal delapan bimbingan disetujui.
- Approval atau rejection bimbingan oleh dosen.
- Export PDF formulir bimbingan.
- Upload laporan sidang dan link luaran.
- Approval laporan sidang oleh dosen.
- Prasyarat sidang: bimbingan cukup dan laporan disetujui.
- Jadwal sidang dengan validasi bentrok.
- Penilaian dan hasil akhir.
- Flow revisi sidang.
- Notifikasi aplikasi dan email untuk event penting.
- Admin management user, dosen, password, status, log, dan report.
- Swagger API documentation.
- Redis untuk cache, throttle, rate limit, token revocation, dan state sementara.
- Cloudflare Turnstile pada login.
- Deployment backend ke Google Cloud Functions/Cloud Run.

### 5.2 Out of Scope

- Pembayaran, marketplace, atau fitur transaksi non-akademik.
- Sistem tanda tangan digital tersertifikasi pemerintah.
- Integrasi SSO kampus penuh, kecuali direncanakan kemudian.
- Video meeting bimbingan built-in.
- Penyimpanan file internal sebagai pengganti Google Drive, kecuali GCS dipakai untuk kebutuhan tertentu.
- Mobile app native Android/iOS.

## 6. Definisi Role dan Hak Akses

| Role | Akses Utama |
| --- | --- |
| Mahasiswa | Track, kelompok, proposal, bimbingan, laporan, hasil, revisi, profile, settings |
| Dosen | Mahasiswa bimbingan, approval bimbingan, approval laporan, approval revisi, profile, settings |
| Koordinator | Kelola periode, validasi proposal, assign pembimbing, daftar mahasiswa, jadwal sidang, monitoring revisi, fitur dosen |
| Kaprodi | Kelola koordinator, daftar dosen, monitoring mahasiswa, fitur koordinator, fitur dosen |
| Admin | Kelola users, kelola dosen, monitoring sistem, audit log, pengaturan sistem |
| Developer | Super-admin teknis, full access, terbatas pada akun dan perangkat yang diizinkan |

## 7. Aturan Jalur Akademik

### 7.1 Regular

Mahasiswa regular mengikuti urutan proyek berdasarkan semester. Contoh umum:

- Semester 2: Proyek 1.
- Semester 3: Proyek 2.
- Semester 4 atau setelahnya: Proyek 3, sesuai aturan akademik yang berlaku.

Regular tidak bebas mengambil proyek lanjutan bila proyek sebelumnya belum lulus, kecuali dalam skenario repeat parallel yang diizinkan oleh aturan akademik.

### 7.2 RPL

Mahasiswa RPL dapat mengambil proyek lebih fleksibel. RPL dapat langsung mengambil Proyek 2 atau Proyek 3 sesuai penetapan akademik, tanpa harus terkunci penuh pada semester reguler.

### 7.3 Mengulang

Mahasiswa yang tidak lulus suatu track dapat ditandai sebagai wajib ulang. Flow mengulang:

- Mahasiswa regular yang mengulang tetap mengikuti jadwal periode yang tersedia.
- Mahasiswa regular dapat mengikuti periode RPL untuk mengulang bila periode tersebut membuka track yang sama.
- Mahasiswa RPL dapat mengulang lebih fleksibel sesuai periode yang tersedia.
- Mahasiswa yang mengulang proyek sebelumnya tidak boleh lanjut ke proyek berikutnya sampai track ulang dinyatakan lulus, kecuali sistem parallel repeat diizinkan oleh periode.
- Jika proyek sudah selesai secara periode, mahasiswa tidak boleh menambah bimbingan, proposal, laporan, atau data akademik baru untuk periode tersebut.

### 7.4 Parallel Repeat

Parallel repeat mengizinkan mahasiswa mengambil track ulang dan track berjalan pada periode yang sama bila aturan akademik membolehkan. Contoh: mahasiswa regular semester 3 dapat mengambil Proyek 1 ulang dan Proyek 2 secara parallel bila periode menyediakan keduanya dan kaprodi/koordinator mengizinkan.

## 8. Track dan Luaran Akademik

### 8.1 Proyek 1

Luaran Proyek 1:

- Aplikasi implementor.
- CRUD sebagai nilai tambah.
- Draft buku.
- Poster.

### 8.2 Proyek 2

Luaran Proyek 2:

- Aplikasi Code Coverage (CRUD RDBMS).
- Draft jurnal.
- Laporan dengan metodologi riset.
- Poster.

Upload laporan sidang Proyek 2 menggunakan satu link Google Drive yang dapat berisi seluruh luaran tersebut.

### 8.3 Proyek 3

Luaran Proyek 3:

- Aplikasi Web Services (Doc API).
- Draft jurnal.
- Buku tutorial, bebas memilih penerbit.
- Poster.

Upload laporan sidang Proyek 3 menggunakan satu link Google Drive yang dapat berisi seluruh luaran tersebut.

### 8.4 Internship 1 dan Internship 2

Internship dikelola sebagai track individual. Internship membutuhkan dua dosen pembimbing. Detail luaran mengikuti ketentuan akademik prodi dan dapat disimpan dalam link laporan serta luaran.

## 9. Flow Utama Produk

### 9.1 Registrasi dan Login

1. Mahasiswa membuka halaman register.
2. Mahasiswa mengisi nama, email, NPM, password, program studi, angkatan, dan data pendukung.
3. Sistem memvalidasi format email, domain, nama email, dan menolak pola email sementara atau tidak wajar.
4. Sistem mengirim OTP register.
5. Mahasiswa memverifikasi OTP.
6. Akun dibuat dan dapat login.
7. Saat login, sistem memvalidasi Cloudflare Turnstile bila fitur aktif.
8. Backend mengembalikan token dan data role.
9. Frontend mengarahkan user ke dashboard sesuai role.

### 9.2 Pemilihan Track dan Kelompok

1. Mahasiswa memilih track akademik.
2. Sistem mengecek jalur regular/RPL, status repeat, periode aktif, dan eligibility.
3. Untuk proyek, mahasiswa dapat membuat kelompok sendiri atau bersama satu partner.
4. Untuk internship, sistem hanya mengizinkan mahasiswa individual.
5. Jika salah satu anggota kelompok sudah upload proposal, anggota lain tidak perlu upload ulang.
6. Data kelompok harus tampil konsisten di proposal, assign pembimbing, bimbingan, laporan, sidang, dan hasil akhir.

### 9.3 Upload Proposal

1. Mahasiswa mengisi judul proposal.
2. Mahasiswa mengisi link proposal Google Drive.
3. Untuk proyek, mahasiswa memilih satu dosen usulan pembimbing.
4. Untuk internship, mahasiswa memilih dua dosen usulan pembimbing.
5. Proposal disimpan sebagai satu proposal per kelompok untuk proyek dan satu proposal per mahasiswa untuk internship.
6. Status awal proposal adalah menunggu validasi.

### 9.4 Validasi Proposal

1. Koordinator melihat daftar proposal sesuai track yang di-assign.
2. Koordinator tidak boleh melihat proposal dari track di luar assignment.
3. Koordinator meninjau proposal, anggota kelompok, judul, link, dan usulan pembimbing.
4. Koordinator dapat approve atau reject.
5. Jika reject, mahasiswa memperbaiki dan submit ulang.
6. Jika approve, proposal masuk tahap assign pembimbing.

### 9.5 Assign Pembimbing

1. Koordinator melihat proposal yang telah valid.
2. Sistem menampilkan usulan pembimbing dari mahasiswa.
3. Koordinator memilih pembimbing secara manual, tidak otomatis.
4. Untuk proyek, hanya satu dosen pembimbing wajib.
5. Untuk internship, dua dosen pembimbing wajib.
6. Untuk proyek berkelompok, assignment berlaku untuk seluruh anggota kelompok.
7. Setelah assign, mahasiswa dapat mulai menambah catatan bimbingan.

### 9.6 Bimbingan Online

1. Mahasiswa menambah catatan bimbingan berdasarkan track aktif.
2. Untuk proyek berkelompok, cukup salah satu anggota menambah catatan dan data berlaku untuk seluruh kelompok.
3. Tanggal bimbingan tidak boleh sebelum proposal disetujui atau sebelum pembimbing diassign.
4. Tanggal bimbingan tidak boleh melebihi akhir jadwal periode.
5. Jika periode sudah selesai, mahasiswa tidak boleh menambah bimbingan walaupun tanggal yang dipilih masih di dalam rentang periode.
6. Bimbingan yang ditolak tidak dihitung sebagai bimbingan disetujui.
7. Mahasiswa tetap dapat menambah bimbingan pengganti bila jumlah bimbingan disetujui belum mencapai delapan dan periode masih aktif.
8. Batas progres sidang adalah delapan bimbingan yang disetujui.
9. Dosen dapat approve atau reject bimbingan.

### 9.7 Export Formulir Bimbingan PDF

1. Mahasiswa membuka menu bimbingan.
2. Mahasiswa memilih export PDF.
3. Sistem menghasilkan formulir bimbingan sesuai template resmi.
4. Header menampilkan logo ULBI.
5. Judul dokumen menyesuaikan track, contoh: "FORMULIR BIMBINGAN KEGIATAN PROYEK 1".
6. Data nama, NPM, pembimbing, judul, kegiatan, tanggal, catatan, dan tanda tangan/QR diambil otomatis dari database.
7. Bagian keterangan bawah menyesuaikan track mahasiswa.
8. Tanggal export menggunakan format Bandung, tanggal bulan tahun saat export.

### 9.8 Upload Laporan Sidang

1. Mahasiswa dapat upload laporan hanya jika syarat bimbingan terpenuhi.
2. Mahasiswa mengisi judul laporan, link laporan, dan link luaran.
3. Link luaran adalah satu link Google Drive yang dapat berisi semua artefak.
4. Untuk Proyek 2 dan Proyek 3, sistem tidak memecah input per artefak.
5. Dosen pembimbing meninjau laporan dan luaran.
6. Dosen dapat approve atau reject.
7. Jika laporan disetujui dan bimbingan mencukupi, mahasiswa eligible untuk dijadwalkan sidang.

### 9.9 Penjadwalan Sidang

1. Koordinator membuka menu jadwal sidang.
2. Sistem hanya menampilkan mahasiswa atau kelompok yang eligible.
3. Eligible berarti laporan sidang sudah approved dan jumlah bimbingan approved memenuhi syarat.
4. Koordinator memilih tanggal, jam, ruangan, pembimbing, dan penguji.
5. Sistem menolak jadwal bila ada bentrok pada tanggal dan jam yang sama untuk dosen yang sama.
6. Sistem juga harus mencegah bentrok ruangan bila tanggal dan jam sama.
7. Untuk proyek berkelompok, jadwal tampil sebagai kelompok.
8. Untuk mahasiswa individual, jadwal tampil sebagai nama mahasiswa.

### 9.10 Sidang, Nilai, dan Hasil Akhir

1. Setelah sidang, dosen/penguji menginput nilai dan catatan.
2. Sistem menghitung atau menyimpan hasil akhir berdasarkan aturan akademik.
3. Status hasil dapat berupa lulus, revisi, atau tidak lulus.
4. Mahasiswa dapat melihat nilai dan hasil akhir.
5. Jika revisi, mahasiswa masuk flow revisi sidang.

### 9.11 Revisi Sidang

1. Mahasiswa dengan status revisi mengunggah dokumen revisi.
2. Dosen atau penguji terkait meninjau revisi.
3. Revisi dapat disetujui atau ditolak dengan catatan.
4. Jika ditolak, mahasiswa memperbaiki dan upload ulang.
5. Jika disetujui, status akhir dapat berubah menjadi selesai/lulus sesuai aturan.

### 9.12 Notifikasi

Sistem harus mengirim notifikasi aplikasi dan email untuk event penting:

- Akun/register/OTP.
- Proposal berhasil diajukan.
- Proposal approved/rejected.
- Pembimbing diassign.
- Bimbingan diajukan.
- Bimbingan approved/rejected.
- Laporan sidang diajukan.
- Laporan sidang approved/rejected.
- Jadwal sidang dibuat atau diubah.
- Hasil sidang tersedia.
- Revisi sidang diminta.
- Revisi approved/rejected.
- Periode selesai.

## 10. Kebutuhan Fungsional

### 10.1 Authentication dan Account

- Sistem harus mendukung login multi-role.
- Sistem harus mendukung register mahasiswa.
- Sistem harus mendukung OTP email untuk registrasi dan reset password.
- Sistem harus mendukung ganti password oleh user.
- Sistem harus mendukung reset password user oleh admin.
- Sistem harus mendukung Cloudflare Turnstile pada login.
- Sistem harus menolak email temp mail, format tidak valid, dan local-part email yang mencurigakan.
- Sistem harus menyimpan token secara aman dan mendukung token revocation.

### 10.2 Dashboard Mahasiswa

- Menampilkan ringkasan progres track.
- Menampilkan status proposal, bimbingan, laporan, sidang, dan revisi.
- Menampilkan jumlah bimbingan approved dari target delapan.
- Menampilkan pembimbing aktif.
- Menampilkan jadwal sidang bila ada.
- Menampilkan notifikasi terbaru.
- Menyediakan akses ke track, kelompok, proposal, bimbingan, laporan, hasil, revisi, profile, dan settings.

### 10.3 Dashboard Dosen

- Menampilkan mahasiswa/kelompok bimbingan.
- Menampilkan daftar bimbingan yang menunggu review.
- Menampilkan laporan sidang yang menunggu review.
- Menampilkan revisi yang menunggu review.
- Menampilkan statistik ringkas.
- Mengizinkan approve/reject dengan catatan.

### 10.4 Dashboard Koordinator

- Menampilkan statistik proposal, mahasiswa, bimbingan, laporan, dan sidang sesuai track assignment.
- Mengelola periode berdasarkan track dan jalur.
- Memvalidasi proposal sesuai track.
- Assign pembimbing manual.
- Melihat daftar mahasiswa sesuai scope.
- Menjadwalkan sidang.
- Memonitor revisi.

### 10.5 Dashboard Kaprodi

- Mengelola koordinator.
- Satu koordinator dapat diassign ke lebih dari dua track.
- Assignment koordinator dapat mencakup proyek, internship, atau kombinasi.
- Mengelola periode untuk regular, RPL, atau keduanya.
- Melihat daftar dosen.
- Monitoring mahasiswa.
- Mengakses fitur koordinator dan dosen sesuai kebutuhan.

### 10.6 Dashboard Admin

- Melihat statistik sistem.
- Melihat recent activity.
- Melihat audit logs.
- Mengelola semua users.
- Mengubah status user.
- Mengubah profile user.
- Reset password user.
- Menghapus user bila valid.
- Mengelola data dosen.
- Melihat report sistem.

### 10.7 Periode Akademik

- Sistem harus mendukung pembuatan periode berdasarkan track.
- Saat membuat periode, pengelola dapat memilih scope jalur: regular, RPL, atau regular dan RPL.
- Periode memiliki tanggal mulai, tanggal selesai, semester, status, track, jalur, dan deskripsi.
- Periode completed mengunci proses baru pada periode tersebut.
- Mahasiswa tidak boleh menambah bimbingan, proposal, laporan, atau jadwal baru pada periode completed.

### 10.8 Realtime Refresh

- Sistem harus memperbarui data ketika ada perubahan data, bukan berdasarkan polling interval terus-menerus.
- Event perubahan data harus membuat halaman terkait memperbarui cache atau melakukan refetch terarah.
- Realtime event harus relevan dengan role dan halaman.
- Data tidak boleh refresh tanpa alasan karena dapat membebani backend dan Redis.

### 10.9 Swagger API Documentation

- Swagger harus menampilkan endpoint aktif.
- Swagger harus mendukung auth token.
- Endpoint dengan Turnstile perlu dokumentasi khusus untuk mode testing.
- Response error harus jelas dan tidak hanya "Internal server error".

## 11. Kebutuhan Non-Fungsional

### 11.1 Performance

- Halaman dashboard harus memuat data utama dengan cepat.
- Backend harus menggunakan Redis untuk cache response yang aman dicache.
- Query database harus menggunakan index pada kolom yang sering dicari.
- Payload API harus ringkas dan tidak mengirim data di luar kebutuhan role.
- Frontend harus melakukan lazy loading untuk komponen non-kritis bila diperlukan.

### 11.2 Security

- API harus menggunakan JWT atau mekanisme auth yang setara.
- Password harus di-hash dengan bcrypt.
- CORS hanya mengizinkan origin frontend resmi.
- Rate limit harus aktif pada endpoint sensitif.
- Login harus dilindungi Turnstile saat production.
- Input user harus divalidasi di backend.
- Role-based access control wajib diterapkan di setiap endpoint.
- Koordinator hanya boleh mengakses track yang diassign.
- Data mahasiswa tidak boleh bocor ke role yang tidak berwenang.
- Secret tidak boleh tersimpan di repository.
- Environment production harus fail-fast bila konfigurasi wajib tidak valid.

### 11.3 Reliability

- Redis harus memiliki fallback in-memory bila tidak tersedia, tetapi production sebaiknya menggunakan Redis aktif.
- Error backend harus dikembalikan dengan format yang konsisten.
- Audit log harus mencatat aksi penting.
- Upload berbasis link harus tetap tersimpan walaupun Google Drive tidak diintegrasikan penuh.
- Deployment gagal harus tidak mengalihkan traffic ke revision yang tidak sehat.

### 11.4 Usability

- Semua alert harus menggunakan toast/dialog yang konsisten, bukan alert bawaan browser.
- Form harus memberikan error spesifik.
- Status akademik harus mudah dibaca.
- Kelompok proyek harus selalu terlihat sebagai kelompok, bukan mahasiswa terpisah, pada konteks proposal, assign, bimbingan, laporan, dan sidang.
- Mobile layout harus tetap dapat digunakan.

### 11.5 Maintainability

- Business rule akademik harus dipusatkan pada service/helper, bukan tersebar di UI.
- Endpoint harus terdokumentasi di Swagger.
- Migration database harus berurutan dan aman dijalankan.
- Constant track, jalur, status, dan role harus konsisten antara frontend dan backend.

## 12. Data Model Konseptual

Entitas utama:

- User: akun login, role, status, email, password hash.
- Mahasiswa: NPM, nama, prodi, angkatan, jalur, track aktif, status akademik.
- Dosen: nama, email, gelar, role akademik, status aktif.
- KoordinatorAssignment: dosen/koordinator, track, semester, jalur, status.
- Periode/Jadwal: track, semester, jalur scope, tanggal mulai, tanggal selesai, status.
- Kelompok: group id, track, anggota, status.
- Proposal: kelompok/mahasiswa, judul, link proposal, status, catatan.
- ProposalSupervisorRequest: dosen usulan mahasiswa.
- SupervisorAssignment: pembimbing final dari koordinator.
- Bimbingan: sesi, tanggal, topik, catatan, status, reviewer.
- LaporanSidang: judul, link laporan, link luaran, status, catatan.
- JadwalSidang: mahasiswa/kelompok, tanggal, jam, ruangan, pembimbing, penguji.
- NilaiSidang: nilai, catatan, status hasil.
- SidangRevision: dokumen revisi, status, catatan.
- Notification: penerima, tipe, judul, pesan, status baca.
- EmailLog: event, penerima, status kirim, error.
- AuditLog: actor, aksi, resource, metadata, timestamp.

## 13. Integrasi

### 13.1 Google Cloud Functions/Cloud Run

Backend dijalankan sebagai HTTP function dengan Functions Framework. Deployment harus memastikan service listen pada port yang diberikan oleh environment `PORT`.

### 13.2 MySQL

MySQL adalah database utama untuk data akademik, user, proposal, bimbingan, laporan, sidang, revisi, notifikasi, dan audit.

### 13.3 Upstash Redis

Redis digunakan untuk:

- Rate limiting.
- OTP throttle.
- Token revocation.
- Cache profile/session.
- Cache response tertentu.
- State sementara untuk fitur realtime/event-driven bila diperlukan.

### 13.4 Cloudflare Turnstile

Turnstile digunakan untuk proteksi login dari bot dan automated abuse. Frontend harus memiliki `NEXT_PUBLIC_TURNSTILE_SITE_KEY`, backend harus memiliki `TURNSTILE_SECRET_KEY`, dan `TURNSTILE_ENABLED=true` pada production.

### 13.5 Email Provider

Email provider digunakan untuk OTP, reset password, dan notifikasi akademik. Sistem harus dapat menonaktifkan email notification melalui environment bila dibutuhkan.

### 13.6 Google Drive Link

Dokumen proposal, laporan, dan luaran disimpan sebagai link Google Drive. Sistem tidak perlu memecah luaran Proyek 2 dan Proyek 3 menjadi banyak input; satu folder/link dapat berisi semua artefak.

## 14. Validasi dan Business Rules Kritis

- Mahasiswa wajib login untuk mengakses dashboard.
- Mahasiswa hanya boleh memiliki satu track aktif utama, kecuali flow repeat/parallel yang valid.
- Proyek dapat berkelompok maksimal dua orang.
- Internship harus individual.
- Proposal proyek cukup diupload satu kali oleh salah satu anggota kelompok.
- Proposal yang sudah ada untuk kelompok harus terlihat oleh semua anggota kelompok.
- Koordinator hanya melihat proposal sesuai track assignment.
- Assign pembimbing proyek hanya satu dosen.
- Assign pembimbing internship wajib dua dosen.
- Bimbingan proyek cukup dibuat oleh salah satu anggota kelompok.
- Bimbingan ditolak tidak dihitung sebagai progres approved.
- Mahasiswa boleh menambah bimbingan pengganti jika approved kurang dari delapan dan periode masih aktif.
- Tanggal bimbingan tidak boleh sebelum assignment pembimbing/proposal approved.
- Tanggal bimbingan tidak boleh setelah akhir periode.
- Periode completed mengunci semua input baru.
- Sidang hanya bisa dijadwalkan jika bimbingan approved mencukupi dan laporan approved.
- Jadwal sidang tidak boleh bentrok untuk dosen yang sama pada tanggal dan jam yang sama.
- Jadwal sidang tidak boleh bentrok untuk ruangan yang sama pada tanggal dan jam yang sama.
- Proyek berkelompok harus tampil sebagai kelompok pada jadwal sidang.
- Link luaran Proyek 2 dan Proyek 3 cukup satu link Google Drive.
- Admin dapat reset password user.
- Audit log harus mencatat aksi penting.

## 15. Acceptance Criteria

### 15.1 Mahasiswa

- Mahasiswa dapat register dengan email valid dan OTP.
- Mahasiswa tidak dapat register dengan temp mail atau email berpola aneh.
- Mahasiswa dapat memilih track sesuai jalur dan periode yang valid.
- Mahasiswa proyek dapat membuat kelompok dua orang.
- Anggota kelompok kedua tidak dapat upload proposal duplikat bila proposal kelompok sudah ada.
- Mahasiswa dapat melihat status proposal, bimbingan, laporan, sidang, dan revisi.
- Mahasiswa dapat menambah bimbingan jika periode aktif dan aturan tanggal valid.
- Mahasiswa tidak dapat menambah bimbingan setelah periode completed.
- Mahasiswa dapat export PDF formulir bimbingan.
- Mahasiswa dapat upload laporan dan satu link luaran.

### 15.2 Dosen

- Dosen dapat melihat mahasiswa/kelompok bimbingan.
- Dosen dapat approve/reject bimbingan.
- Bimbingan rejected tidak menambah progres approved.
- Dosen dapat approve/reject laporan sidang.
- Dosen dapat approve/reject revisi sidang.

### 15.3 Koordinator

- Koordinator hanya melihat proposal dari track yang diassign.
- Koordinator dapat mengelola periode berdasarkan track dan jalur.
- Koordinator dapat assign pembimbing manual.
- Assign pembimbing proyek hanya satu dosen.
- Assign pembimbing internship dua dosen.
- Koordinator dapat menjadwalkan sidang hanya untuk mahasiswa/kelompok eligible.
- Sistem menolak jadwal sidang yang bentrok.

### 15.4 Kaprodi

- Kaprodi dapat assign koordinator ke lebih dari dua track.
- Kaprodi dapat memilih scope periode regular, RPL, atau keduanya.
- Kaprodi dapat monitoring mahasiswa lintas track.
- Kaprodi dapat mengakses fungsi koordinator sesuai kebutuhan.

### 15.5 Admin

- Admin dapat melihat semua user.
- Admin dapat update profile user.
- Admin dapat reset password user.
- Admin dapat mengaktifkan atau menonaktifkan user.
- Admin dapat melihat audit log dan recent activity.
- Admin dapat mengelola dosen.

## 16. Metrics Keberhasilan

- Persentase mahasiswa yang berhasil menyelesaikan flow proposal sampai sidang melalui sistem.
- Jumlah proposal yang divalidasi tepat waktu.
- Jumlah bimbingan approved per mahasiswa/kelompok.
- Penurunan kasus data ganda proposal/laporan.
- Penurunan bentrok jadwal sidang.
- Waktu rata-rata dari proposal submitted ke approved.
- Waktu rata-rata dari laporan submitted ke approved.
- Jumlah notifikasi yang berhasil terkirim.
- Jumlah error API production per endpoint.
- Cache hit rate Redis untuk endpoint yang dicache.

## 17. Risiko dan Mitigasi

| Risiko | Dampak | Mitigasi |
| --- | --- | --- |
| Business rule akademik berubah | Flow sistem tidak sesuai kebijakan baru | Pusatkan rule di service dan constant |
| Redis salah konfigurasi | Cache/rate limit tidak aktif | Validasi env dan fallback dengan warning |
| Turnstile tidak muncul di frontend | Login production gagal | Validasi site key frontend dan secret backend |
| Jadwal sidang bentrok | Dosen/ruangan konflik | Validasi backend wajib sebelum insert/update |
| Proposal kelompok duplikat | Data akademik tidak konsisten | Gunakan group id sebagai basis proposal proyek |
| Periode completed masih bisa input | Data periode lama berubah | Backend harus menolak semua create/update relevan |
| Email gagal terkirim | Pengguna tidak menerima notifikasi | Simpan app notification dan email log |
| Secret bocor | Risiko keamanan tinggi | Gunakan GitHub Secret/GCP env, rotasi secret |
| Swagger tidak sesuai API | Testing membingungkan | Update Swagger setiap perubahan endpoint |

## 18. Roadmap

### Phase 1 - Stabilitas Core Flow

- Finalisasi flow proposal kelompok.
- Finalisasi assign pembimbing proyek/internship.
- Finalisasi bimbingan online dan approval.
- Finalisasi upload laporan dan luaran satu link.
- Finalisasi jadwal sidang anti bentrok.

### Phase 2 - Monitoring dan Realtime

- Event-driven realtime refresh pada semua halaman penting.
- Monitoring dashboard yang lebih akurat.
- Audit log lengkap.
- Notifikasi aplikasi konsisten.

### Phase 3 - Notifikasi dan Dokumen

- Lengkapi email notification semua event akademik.
- Finalisasi template PDF bimbingan.
- Tambahkan export/report admin bila dibutuhkan.

### Phase 4 - Security Hardening

- Validasi CORS production.
- Rate limit endpoint sensitif.
- Turnstile production.
- Review endpoint public sementara seperti schema fix.
- Secret rotation dan deployment hygiene.

### Phase 5 - Developer Role dan Operasional Lanjutan

- Implementasi developer role bila benar-benar dibutuhkan.
- Device binding untuk akses developer.
- Advanced system monitoring.
- Admin/developer tools untuk maintenance aman.

## 19. Dependensi Teknis

Frontend:

- Next.js 16.
- React 19.
- Tailwind CSS.
- TanStack React Query.
- Zustand.
- Sonner.
- Framer Motion.
- Lucide React.

Backend:

- Node.js 22.
- Express.js 5.
- MySQL2.
- JWT dan bcrypt.
- Better Auth.
- Upstash Redis.
- Cloudflare Turnstile.
- Mailtrap/Nodemailer.
- Swagger UI.
- PDF-lib.
- Google Cloud Functions Framework.
- Helmet, CORS, compression, rate limit.

Infrastructure:

- Google Cloud Functions/Cloud Run.
- MySQL database.
- Upstash Redis.
- GitHub Actions.
- GitHub Secrets.
- Custom domain frontend.

## 20. Open Questions

- Apakah role developer akan benar-benar masuk production atau cukup untuk staging/internal maintenance?
- Apakah tanda tangan pembimbing pada PDF cukup QR/signature internal atau perlu standar resmi kampus?
- Apakah semua track memiliki minimal delapan bimbingan yang sama, atau ada pengecualian untuk internship?
- Apakah RPL memiliki batas parallel repeat yang berbeda dari regular?
- Apakah penguji sidang selalu satu penguji tambahan atau dapat lebih dari satu?
- Apakah nilai akhir dihitung otomatis berdasarkan formula tertentu atau hanya disimpan manual?
- Apakah email notification wajib untuk semua event atau hanya event kritis?

## 21. Kesimpulan

Kavana adalah sistem operasional akademik yang harus menjaga konsistensi data, transparansi status, dan validasi aturan akademik lintas role. Fokus utama produk bukan hanya tampilan dashboard, tetapi ketepatan flow bisnis: proposal kelompok, assign pembimbing manual, bimbingan yang tervalidasi, laporan yang disetujui, sidang tanpa bentrok, revisi yang terlacak, dan monitoring yang dapat dipercaya.

PRD ini menjadi acuan implementasi dan evaluasi sistem agar setiap perubahan fitur tetap selaras dengan kebutuhan akademik D4 Teknik Informatika ULBI.


---

## 2. AGENTS & REPOSITORY GUIDELINES (AGENTS.md)

> **Sumber Berkas Asli:** `AGENTS.md`

# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## Repository Layout

`D:\Kerjaan\Kavana` is a **workspace of independent git repositories**, not a monorepo. The workspace root is not itself a git repo — each subdirectory has its own `origin` on the `Kavana-Organization` GitHub org. Commit inside the relevant subdirectory, never at the root.

| Directory | Repo | Role |
| --- | --- | --- |
| `nggk-tau-ini-apa/` | Backend-Kavana | Express.js 5 API (Node 24, ESM) — GCF/Cloud Run + Vercel |
| `New-Frontend/` | Frontend-Kavana | Next.js 16 App Router (React 19, JS not TS) |
| `Test-Kavana/` | Test-Kavana | Selenium WebDriver E2E/UI/API/security test suite |
| `Infra-Kavana/` | Infra-Kavana | Docker Compose + Makefile for local full-stack |
| `Docs-Kavana/` | Docs-Kavana | Central documentation (per-repo docs moved here) |

`prd.md` (root) is the authoritative product spec (in Indonesian). The `*_PLAN.md` files at root are feature design docs. Business rules and role semantics come from `prd.md` — consult it before changing academic flow logic.

Domain language is **Indonesian**: `mahasiswa` (student), `dosen` (lecturer), `koordinator`, `kaprodi` (head of program), `bimbingan` (supervision session), `sidang` (thesis defense), `jadwal` (schedule), `laporan` (report), `proposal`, `revisi`, `kelompok` (group), `periode`/`jadwal_proyek` (academic period), `penguji` (examiner), `jalur` (track path: regular/RPL), `luaran` (deliverables).

## Common Commands

**Backend** (`cd nggk-tau-ini-apa`):
```bash
npm run dev      # local server via local.js on PORT (default 3000)
npm start        # functions-framework --target=kavana (GCF-style)
```
No test/lint scripts in the backend itself — it is exercised by Test-Kavana.

**Frontend** (`cd New-Frontend`):
```bash
npm run dev              # next dev
npm run build            # next build
npm run lint             # eslint
npm run test:e2e         # playwright test
npm run test:e2e:ui      # playwright --ui
```

**Test-Kavana** (`cd Test-Kavana`) — Selenium suite against a running backend + frontend:
```bash
npm test                       # full runner (tests/runner.js)
npm run test:api:auth          # single API test file
npm run test:ui:login          # single UI test file
npm run test:academic:bimbingan  # single academic-flow test
npm run test:security          # rbac + auth + validation
npm run test:headless          # HEADLESS=true
npm run test:chrome|firefox|edge # BROWSER=<x>
npm run install:drivers        # install chromedriver/geckodriver/edgedriver
```
Each `test:*` script maps to one file — run the specific script to iterate on a single test. Config/target URLs come from `Test-Kavana/.env` (see `.env.example`) and `selenium.config.js`.

**Infra** (`cd Infra-Kavana`): `make up` / `make dev` (hot reload) / `make down` / `make logs` / `make health`. See `make help`.

## Backend Architecture (`nggk-tau-ini-apa`)

Standard layered Express app, ESM throughout (`"type": "module"`). Entry composition lives in `index.js`, which exports the app three ways: `functions.http('kavana', app)` for GCF Gen2, `export default app` for Vercel (`api/index.js` wraps it), and `export const kavana` for `local.js`. **OpenTelemetry `initTelemetry()` must run before any other import** — keep it first in `index.js`.

Request flow: `routes/*Routes.js` → `middleware` (`auth` → `requireRole` → optional `cacheGet`) → `controllers/*Controller.js` → `services/*` and direct `pool.query`. Routes are mounted under `/api/<domain>` in `index.js`.

- **`config/`** — `env.js` validates all env vars with Zod and **fail-fasts (`process.exit(1)`) in production** if invalid; in dev it warns and falls back to defaults. `db.js` is the shared mysql2 pool (import as `pool`). `redis.js` initializes Upstash Redis (non-blocking, in-memory fallback). `auth.js` is the Better Auth instance. `telemetry.js` is OTel setup.
- **`middleware/auth.js`** — legacy JWT bearer verification; sets `req.user = { id, role }`.
- **`middleware/role.js`** — `requireRole(...roles)` RBAC with a **role hierarchy baked in** (see below). `kaprodiOnly()` is stricter (excludes admin).
- **`services/`** — centralized business logic (PDF/DOCX generation, notifications, enrollment, sidang eligibility). Per PRD, academic business rules belong here/in helpers, not in UI or controllers.
- **`utils/`** — `constants.js` (track/semester/role mappings — the source of truth for `VALID_TRACKS`, `TRACK_SEMESTER_MAP`, etc.), `roleHelper.js`, `customId.js`, integrations (`gcs`, `gdrive`, `cloudinary`, `mailer`, `turnstile`, `qstash`), `responseCache.js`.
- **`migrations/`** — numbered SQL files (`009_...` onward are ordered; run in sequence). Applied manually against MySQL. Baseline schema is `../nggk-tau-ini-apa/bimbingan_online.sql`.

### RBAC role hierarchy (`middleware/role.js`)
This is the enforced authorization model — respect it when adding endpoints:
- `developer` — superuser, passes every `requireRole` check.
- `admin` — passes all checks **except** those requiring `developer`.
- `koordinator`, `kaprodi`, `penguji` — may access `dosen` routes (they are all dosen with extra roles).
- `kaprodi` — may also access `koordinator` routes.
- Use `kaprodiOnly()` for kaprodi-exclusive routes that admin must NOT reach.

Dosen roles are stored in the `dosen_role` pivot table (a dosen can hold multiple roles); `roleHelper.js` and Better Auth's `customSession` resolve them. `mahasiswa`/`admin` roles come directly from the user record.

### IDs & schema conventions
Business tables use **string primary keys with per-table prefixes** generated by `utils/customId.js` (e.g. `MHS`, `DSN`, `BIM`, `JDW`, `SDG`, `KLP`). Auto-increment is disabled on these (migrations 025/026). When inserting into a registered table, use `insertWithCustomId(table, cols, vals)` rather than raw `INSERT` so the prefixed ID is assigned. New business tables must be added to `CUSTOM_ID_CONFIG`.

### Auth model (hybrid)
Both legacy JWT (`middleware/auth.js`, `JWT_SECRET`) and Better Auth (`config/auth.js`, session cookies + bearer plugin, mounted at `/api/auth/better/*`) coexist. The Better Auth handler is registered **before** `express.json()` in `index.js` — preserve that ordering. `env.js` marks JWT as "legacy — will sunset."

### API docs
Swagger spec is built from JSDoc (`swagger.js`). UI at `/docs/ui`, JSON at `/openapi.json` and `/docs.json`; `/docs` is a lightweight index kept fast for serverless cold starts.

## Frontend Architecture (`New-Frontend`)

Next.js 16 **App Router**, plain JavaScript (`.jsx`/`.js`, no TypeScript; `jsconfig.json` sets `@/*` → `src/*`). React 19, Tailwind v4, shadcn/Radix UI (`components.json`), TanStack Query + Table, Zustand, Framer Motion, Sonner toasts.

- **`src/app/`** — routes. Public pages at top level (`login`, `register`, `forgot-password`, `verify`, legal/guide pages). **`src/app/dashboard/<role>/`** is the core: one subtree per role (`mahasiswa`, `dosen`, `koordinator`, `kaprodi`, `admin`, `developer`, `penguji`), each with feature pages matching the PRD flows (e.g. `mahasiswa/proposal`, `koordinator/validasi-proposal`, `koordinator/jadwal-sidang`).
- **`src/lib/`** — `api.js` is the central fetch wrapper (token in `sessionStorage`, per-user GET caching, in-flight dedup; base URL from `NEXT_PUBLIC_API_BASE_URL`). `realtime.js` drives **event-driven refresh** (targeted refetch on data-change events, NOT interval polling — per PRD §10.8). `constants.js` mirrors backend track/role constants. `alert.js` wraps toasts (no native `alert()`). `validators.js`/`validation.js` for forms.
- **`src/store/auth-store.js`** — Zustand auth store; `hydrate()` from sessionStorage on mount, holds `token`/`role`/`roles`/`userId`. Multi-role users (e.g. kaprodi+koordinator) carry a `roles` array.
- **`src/components/`** — `ui/` (shadcn primitives), plus `auth/`, `layout/`, `public/`, `shared/`.

Keep track/role/status **constants in sync between `New-Frontend/src/lib/constants.js` and `nggk-tau-ini-apa/utils/constants.js`** — the PRD requires consistency across FE/BE.

## Key External Integrations

MySQL (primary DB) · Upstash Redis (rate limit, OTP throttle, token revocation, response cache — in-memory fallback in dev) · Upstash QStash (background jobs, opt-in via `QSTASH_ENABLED`) · Cloudflare Turnstile (login bot protection; `TURNSTILE_ENABLED=true` in prod) · Mailtrap/Nodemailer (OTP + academic notification emails, `EMAIL_NOTIFICATIONS_ENABLED`) · Cloudinary (profile photos) · Google Drive (proposal/report/luaran stored as links, not uploaded) · GCS · OpenTelemetry.

## Critical Business Rules (see `prd.md` §14 for the full list)

When touching academic flow, these invariants must hold:
- Proyek groups (`kelompok`) = max 2 members; **one proposal per group** (either member uploads once, visible to both). Internship = individual, requires **2** supervisors; proyek requires **1**.
- Koordinator only sees/validates proposals for their **assigned track** (`koordinator` assignment by semester/track).
- Supervisor assignment is **manual** by koordinator, never automatic.
- Bimbingan: rejected sessions don't count; target is **8 approved**; date must be ≥ proposal-approved/supervisor-assigned and ≤ period end.
- A **completed `periode` locks all new input** (bimbingan/proposal/laporan/jadwal) — backend must reject, not just hide in UI.
- Sidang scheduling requires report approved + enough approved bimbingan, and must **reject conflicts** on same date/time for the same dosen or same room.
- Enforce RBAC on **every** endpoint; never leak student data across roles.


---

## 3. TUGAS DAN ANALISIS ACCESS CONTROL (Tugas_Access_Control_Kavana.md)

> **Sumber Berkas Asli:** `Tugas_Access_Control_Kavana.md`

**UNIVERSITAS LOGISTIK DAN BISNIS INTERNASIONAL**
**PROGRAM STUDI D4 TEKNOLOGI INFORMASI**
**MATA KULIAH KEAMANAN SISTEM INFORMASI**

**TUGAS ACCESS CONTROL**

**Analisis Pengguna dan Perancangan Hak Akses pada Aplikasi Kavana**

# A. Identitas Kelompok

| **Komponen**       | **Deskripsi** |
|--------------------|---------------|
| **Nama Kelompok**  | .............................................................. |
| **Nama Aplikasi**  | Kavana (KavanaHub) — Sistem Bimbingan Online Prodi D4 Teknik Informatika |
| **Ketua Kelompok** | .............................................................. |
| **Anggota 1**      | .............................................................. |
| **Anggota 2**      | .............................................................. |
| **Anggota 3**      | .............................................................. |
| **Kelas**          | .............................................................. |

> Catatan: kolom identitas anggota sengaja dikosongkan untuk diisi manual. Seluruh isi teknis di bawah ini diambil dari kode dan dokumen proyek yang sebenarnya, bukan asumsi.

# 1. PROFIL APLIKASI

## 1.1 Identitas Aplikasi

| **Komponen**                    | **Deskripsi** |
|---------------------------------|---------------|
| **Nama Aplikasi**               | Kavana (KavanaHub) |
| **Jenis Aplikasi**              | Aplikasi web multi-user (client-server) dengan arsitektur API terpisah: frontend Next.js dan backend REST API Express |
| **Bidang/Kategori**             | Pendidikan tinggi — sistem informasi akademik (manajemen bimbingan dan sidang proyek) |
| **Target Pengguna**             | Mahasiswa D4 Teknik Informatika (jalur regular dan RPL), dosen pembimbing, dosen penguji, koordinator proyek, Ketua Program Studi (kaprodi), admin prodi, dan developer teknis |
| **Teknologi yang Direncanakan** | Frontend: Next.js 16 (App Router) + React 19 + Tailwind CSS 4. Backend: Express 5 (ESM) yang di-deploy ke Google Cloud Functions Gen2 / Cloud Run / Vercel. Basis data: MySQL 8. Autentikasi: Better Auth (session cookie) + JWT. Pendukung: Upstash Redis (cache & rate limit), Cloudflare Turnstile (CAPTCHA), Cloudinary & Google Cloud Storage (berkas), Mailtrap (email OTP), Docker Compose untuk lingkungan lokal |

## 1.2 Latar Belakang

Proses bimbingan akademik pada track Proyek 1, Proyek 2, Proyek 3, Internship 1, dan Internship 2 memiliki banyak tahapan yang saling bergantung: mahasiswa memilih track, membentuk kelompok, mengajukan proposal, menunggu validasi koordinator, menunggu penugasan dosen pembimbing, mencatat bimbingan, mengunggah laporan sidang, menunggu approval, lalu menjalani sidang dan revisi.

Sebelum sistem ini ada, tahapan tersebut tersebar di chat, spreadsheet, dan file Google Drive pribadi. Akibatnya status setiap mahasiswa sulit dipantau secara real time, riwayat approval tidak terdokumentasi, dan potensi bentrok jadwal sidang (tanggal, jam, ruangan, pembimbing, penguji) sulit dicegah.

Masalah lain yang bersifat keamanan informasi: dokumen akademik dan catatan bimbingan tersebar di drive pribadi sehingga tidak ada kendali siapa yang boleh membaca, mengubah, atau menghapusnya. Kaprodi juga tidak memiliki kontrol terpusat atas penugasan koordinator dan periode akademik. Inilah yang membuat perancangan hak akses menjadi bagian penting, bukan pelengkap.

## 1.3 Tujuan Aplikasi

1. Menyediakan satu tempat terpusat untuk seluruh proses bimbingan dan sidang, sehingga setiap tahapan punya status dan riwayat yang jelas.
2. Menjamin setiap proses akademik memiliki jejak audit (siapa melakukan apa, kapan, dari alamat IP mana).
3. Membatasi akses data akademik sesuai peran, sehingga mahasiswa hanya melihat datanya sendiri, dosen hanya melihat mahasiswa bimbingannya, dan koordinator hanya melihat lingkup track yang ditugaskan.
4. Mendukung perbedaan aturan jalur regular dan RPL, termasuk flow mahasiswa yang mengulang proyek.
5. Menyediakan mekanisme notifikasi dan ekspor dokumen resmi (PDF formulir bimbingan) yang konsisten.

## 1.4 Deskripsi Sistem

Pengguna membuka aplikasi web, melakukan login multi-role (dengan CAPTCHA Cloudflare Turnstile), lalu diarahkan ke dashboard sesuai perannya. Setelah terautentikasi, permintaan data dilakukan lewat REST API dengan menyertakan token; setiap endpoint memeriksa identitas pengguna lebih dulu, kemudian memeriksa kewenangan peran, dan pada kasus tertentu memeriksa kepemilikan data (misalnya mahasiswa hanya boleh mengunggah lampiran untuk dirinya sendiri).

Alur utama: mahasiswa mengajukan proposal → koordinator memvalidasi proposal dan menugaskan pembimbing → mahasiswa mencatat bimbingan → dosen menyetujui atau menolak catatan bimbingan → mahasiswa mengunggah laporan sidang → dosen menyetujui laporan → koordinator menjadwalkan sidang dengan validasi bentrok → penguji dan dosen menetapkan hasil sidang → mahasiswa mengerjakan revisi → sistem menutup periode. Admin mengelola akun pengguna, sedangkan developer menangani konfigurasi dan pemeliharaan teknis.

## 1.5 Data yang Akan Dikelola

| **No.** | **Jenis Data** | **Deskripsi** | **Tingkat Sensitivitas** |
|---------|----------------|---------------|--------------------------|
| 1 | Kredensial akun | Kata sandi (hash bcrypt), token sesi, kode OTP sekali pakai, status akun dan alasan pemblokiran | Sangat Sensitif |
| 2 | Log audit dan log autentikasi | Catatan aksi pengguna (aksi, target, hasil, detail), IP address, user agent, riwayat percobaan login | Sangat Sensitif |
| 3 | Konfigurasi sistem | Variabel lingkungan berisi secret, kredensial integrasi (database, penyimpanan, email), daftar perangkat developer yang diizinkan | Sangat Sensitif |
| 4 | Data akademik mahasiswa | Nama, NPM, email, nomor WhatsApp, angkatan, jalur, semester, track, status mengulang | Sensitif |
| 5 | Dokumen akademik | File proposal, laporan sidang, luaran proyek, berkas revisi, catatan hasil validasi | Sensitif |
| 6 | Data bimbingan | Topik, catatan bimbingan, lampiran, status persetujuan, tanda tangan dosen | Sensitif |
| 7 | Data sidang dan hasil | Jadwal, ruangan, pembimbing, penguji, hasil sidang (lulus / lulus revisi / tidak lulus), catatan sidang | Sensitif |
| 8 | Pengelompokan dan penugasan | Kelompok proyek, penugasan pembimbing, penugasan koordinator per semester | Internal |
| 9 | Data dosen | Nama, NIDN, email, nomor WhatsApp, daftar peran yang dipegang | Internal |
| 10 | Periode akademik | Nama periode, tipe, tanggal mulai dan selesai, cakupan jalur | Internal |
| 11 | Konten publik | Landing page, panduan pengguna, FAQ, syarat layanan, kebijakan privasi | Publik |

# 2. IDENTIFIKASI PENGGUNA

## 2.1 Profil User/Role

| **No.** | **User/Role** | **Deskripsi Pengguna** | **Tanggung Jawab** | **Kebutuhan Akses** |
|---------|---------------|------------------------|--------------------|---------------------|
| 1 | Mahasiswa | Pengguna utama yang sedang menempuh track proyek atau internship | Memilih track, membentuk atau bergabung kelompok, mengajukan proposal, mencatat bimbingan, mengunggah laporan dan revisi, melihat jadwal serta hasil sidang | Membuat dan membaca data miliknya sendiri; mengubah profil dan track miliknya; tidak boleh melihat data mahasiswa lain |
| 2 | Dosen | Dosen yang ditugaskan sebagai pembimbing | Meninjau dan menyetujui atau menolak catatan bimbingan, laporan sidang, dan revisi; memberikan catatan akademik | Membaca data mahasiswa bimbingannya, mengubah status persetujuan; tidak boleh membuat jadwal atau mengubah data pokok |
| 3 | Penguji | Dosen yang ditugaskan menguji sidang | Menguji sidang dan menetapkan hasil sidang | Membaca jadwal sidang yang ia uji, mengubah hasil sidang yang menjadi kewenangannya |
| 4 | Koordinator | Dosen yang ditugaskan mengelola satu atau beberapa track pada semester tertentu | Mengelola periode, memvalidasi proposal, menugaskan pembimbing, menjadwalkan sidang, memonitor revisi | Membaca data mahasiswa pada track yang ditangani, membuat jadwal, mengubah status proposal dan penugasan |
| 5 | Kaprodi | Ketua Program Studi | Mengelola koordinator dan periode, memonitor seluruh mahasiswa dan dosen | Membaca seluruh data akademik, menetapkan koordinator per semester, mengubah status proposal dan status mengulang, mengonversi track |
| 6 | Admin | Staf administrasi prodi yang mengelola akun | Mengelola akun pengguna dan data dosen, mereset kata sandi, mengaktifkan atau menonaktifkan akun, memantau log | Membaca seluruh akun dan log audit, membuat data dosen, mengubah status dan profil pengguna, menghapus akun |
| 7 | Developer | Superuser teknis yang dikunci pada perangkat terdaftar | Pemeliharaan sistem, penanganan insiden, audit teknis, pemulihan | Membaca konfigurasi sistem dan log teknis, mencabut perangkat, membersihkan cache, menjalankan uji sintetis |

## 2.2 Justifikasi Role

**Mengapa tujuh role diperlukan.** Aplikasi ini memisahkan dua jenis kewenangan yang sering tercampur: kewenangan **akademik** dan kewenangan **teknis**. Kewenangan akademik melekat pada jabatan struktural (kaprodi dan koordinator) serta pada penugasan (pembimbing dan penguji). Kewenangan teknis melekat pada pengelolaan sistem (admin dan developer). Pemisahan ini yang membuat role tidak bisa digabung begitu saja.

**Mahasiswa** hanya berinteraksi dengan datanya sendiri. Ia tidak diberi kemampuan membaca data mahasiswa lain karena tidak ada kebutuhan bisnis untuk itu, dan data mahasiswa memuat informasi pribadi (email, nomor WhatsApp, status akademik).

**Dosen** dan **Penguji** memiliki kebutuhan akses yang mirip namun berbeda ruang lingkup: dosen bekerja pada mahasiswa bimbingannya, penguji bekerja pada sidang yang ia uji. Keduanya sengaja tidak diberi hak membuat jadwal atau mengubah data pokok, karena penetapan jadwal adalah kewenangan koordinator yang memegang aturan bentrok jadwal.

**Koordinator** diperlukan karena prodi memiliki beberapa track yang dikelola orang berbeda. Ia dibatasi pada semester dan track yang ditugaskan, sehingga koordinator Proyek 1 tidak otomatis bisa mengubah proposal Proyek 3.

**Kaprodi** berada di atas koordinator karena ia yang menetapkan siapa koordinator pada semester tertentu, mengubah status mengulang mahasiswa, dan mengonversi track. Kewenangan ini tidak boleh dipegang koordinator, karena berpotensi dipakai untuk menguntungkan mahasiswa tertentu.

**Admin** adalah role administratif. Ia mengelola akun, tetapi sengaja **tidak** diberi kewenangan penetapan koordinator. Pada implementasi, rute khusus kaprodi memakai pemeriksaan yang lebih ketat yang mengecualikan admin, sehingga keputusan akademik tidak jatuh ke tangan staf administrasi.

**Developer** adalah role dengan kewenangan tertinggi secara teknis. Ia diperlukan untuk pemulihan sistem dan audit, namun karena dampaknya besar, role ini dirancang terikat pada identitas yang terdaftar di konfigurasi secret dan divalidasi per perangkat (device-locked), sehingga tidak cukup hanya dengan menebak email dan kata sandi.

**Pengguna dengan akses terbatas.** Mahasiswa adalah pihak dengan akses paling terbatas: hanya create dan read pada datanya sendiri, tanpa hak hapus pada data akademik mana pun. Selain itu ada pengguna publik (belum login) yang hanya dapat mengakses halaman informasi dan endpoint autentikasi.

# 3. IDENTIFIKASI FITUR DAN RESOURCE

| **No.** | **Nama Fitur** | **Deskripsi Fungsi** | **Resource/Data** | **Tingkat Sensitivitas** |
|---------|----------------|----------------------|-------------------|--------------------------|
| 1 | Autentikasi dan Registrasi | Login multi-role, registrasi mahasiswa, OTP email, reset kata sandi, CAPTCHA, pembatasan percobaan | Kredensial, sesi, kode OTP | Sangat Sensitif |
| 2 | Manajemen Akun Pengguna | Melihat daftar pengguna, mengubah status aktif, mengubah profil, mereset kata sandi, menghapus akun | Tabel pengguna dan data dosen/mahasiswa | Sangat Sensitif |
| 3 | Pemilihan Track dan Kelompok | Mahasiswa memilih track, membuat atau bergabung kelompok proyek | Data mahasiswa (track, jalur, status mengulang), kelompok | Sensitif |
| 4 | Pengajuan dan Validasi Proposal | Mahasiswa mengunggah proposal; koordinator dan kaprodi memvalidasi | Berkas proposal, status proposal, catatan validasi | Sensitif |
| 5 | Penugasan Pembimbing | Koordinator dan kaprodi menetapkan dosen pembimbing | Relasi mahasiswa–dosen pembimbing | Sensitif |
| 6 | Bimbingan Online | Mahasiswa mencatat bimbingan dan mengunggah lampiran; dosen menyetujui atau menolak; ekspor PDF | Catatan bimbingan, lampiran, status, tanda tangan | Sensitif |
| 7 | Laporan Sidang dan Luaran | Mahasiswa mengunggah laporan dan luaran; dosen menyetujui | Berkas laporan, tautan luaran, status | Sensitif |
| 8 | Penjadwalan Sidang | Koordinator membuat jadwal dengan validasi bentrok tanggal, jam, ruangan, pembimbing, dan penguji | Jadwal sidang, penguji, ruangan | Sensitif |
| 9 | Penetapan Hasil Sidang | Penguji, koordinator, dan kaprodi menetapkan hasil sidang | Hasil sidang, catatan sidang | Sensitif |
| 10 | Revisi Sidang | Mahasiswa mengunggah revisi; dosen dan koordinator memverifikasi | Berkas revisi, status verifikasi | Sensitif |
| 11 | Pengelolaan Periode | Koordinator dan kaprodi mengelola periode per track dan jalur | Periode akademik | Internal |
| 12 | Notifikasi | Notifikasi dalam aplikasi dan email atas perubahan status | Notifikasi pengguna, log email | Internal |
| 13 | Monitoring dan Audit | Statistik sistem, log aktivitas, log audit, log autentikasi | Log audit, log autentikasi, statistik | Sangat Sensitif |
| 14 | Konfigurasi Teknis | Melihat konfigurasi sistem, mengelola perangkat developer, membersihkan cache | Variabel lingkungan, perangkat, cache | Sangat Sensitif |
| 15 | Halaman Publik | Landing page, panduan, FAQ, syarat layanan, kebijakan privasi | Konten statis | Publik |

# 4. ACCESS CONTROL MATRIX

C = Create | R = Read | U = Update | D = Delete | ✓ = Diizinkan | — = Tidak Diizinkan

| **Role** | **Resource/Fitur** | **C** | **R** | **U** | **D** |
|----------|--------------------|-------|-------|-------|-------|
| Mahasiswa | Profil akademik milik sendiri | — | ✓ | ✓ | — |
| Mahasiswa | Track dan jalur milik sendiri | — | ✓ | ✓ | — |
| Mahasiswa | Kelompok proyek | ✓ | ✓ | — | — |
| Mahasiswa | Proposal (berkas dan status) | ✓ | ✓ | — | — |
| Mahasiswa | Bimbingan milik sendiri | ✓ | ✓ | — | — |
| Mahasiswa | Laporan sidang dan luaran milik sendiri | ✓ | ✓ | — | — |
| Mahasiswa | Revisi sidang milik sendiri | ✓ | ✓ | — | — |
| Mahasiswa | Jadwal dan hasil sidang milik sendiri | — | ✓ | — | — |
| Mahasiswa | Data mahasiswa lain | — | — | — | — |
| Dosen | Profil dan statistik pribadi | — | ✓ | — | — |
| Dosen | Data mahasiswa bimbingan | — | ✓ | — | — |
| Dosen | Catatan bimbingan (persetujuan) | — | ✓ | ✓ | — |
| Dosen | Laporan sidang (persetujuan) | — | ✓ | ✓ | — |
| Dosen | Revisi sidang (verifikasi) | — | ✓ | ✓ | — |
| Dosen | Tanda tangan bimbingan | — | ✓ | ✓ | — |
| Dosen | Jadwal sidang | — | ✓ | — | — |
| Penguji | Profil pribadi | — | ✓ | — | — |
| Penguji | Jadwal sidang yang diuji | — | ✓ | — | — |
| Penguji | Hasil sidang | — | ✓ | ✓ | — |
| Koordinator | Profil, statistik, semester penugasan | — | ✓ | — | — |
| Koordinator | Daftar mahasiswa pada track yang ditangani | — | ✓ | — | — |
| Koordinator | Daftar dosen dan penguji | — | ✓ | — | — |
| Koordinator | Validasi proposal | — | ✓ | ✓ | — |
| Koordinator | Penugasan pembimbing | ✓ | ✓ | — | — |
| Koordinator | Periode akademik | ✓ | ✓ | ✓ | — |
| Koordinator | Jadwal sidang | ✓ | ✓ | ✓ | — |
| Koordinator | Monitoring revisi | — | ✓ | — | — |
| Koordinator | Catatan bimbingan dan laporan (sebagai pembimbing) | — | ✓ | ✓ | — |
| Kaprodi | Profil, statistik, aktivitas | — | ✓ | — | — |
| Kaprodi | Seluruh data mahasiswa | — | ✓ | — | — |
| Kaprodi | Daftar dosen, koordinator, penguji | — | ✓ | — | — |
| Kaprodi | Penugasan koordinator per semester | ✓ | ✓ | ✓ | ✓ |
| Kaprodi | Validasi proposal | — | ✓ | ✓ | — |
| Kaprodi | Status mengulang mahasiswa | — | ✓ | ✓ | — |
| Kaprodi | Konversi track | ✓ | ✓ | — | — |
| Kaprodi | Jadwal sidang | ✓ | ✓ | ✓ | — |
| Kaprodi | Fitur koordinator dan dosen (warisan kewenangan) | ✓ | ✓ | ✓ | — |
| Admin | Profil dan statistik sistem | — | ✓ | — | — |
| Admin | Daftar dan data pengguna | — | ✓ | ✓ | ✓ |
| Admin | Status aktif akun | — | ✓ | ✓ | — |
| Admin | Kata sandi pengguna (reset) | — | — | ✓ | — |
| Admin | Data dosen | ✓ | ✓ | — | — |
| Admin | Log audit dan aktivitas | — | ✓ | — | — |
| Admin | Laporan sistem | — | ✓ | — | — |
| Developer | Konfigurasi sistem | — | ✓ | — | — |
| Developer | Log audit, log autentikasi, daftar perangkat | — | ✓ | — | — |
| Developer | Pencabutan perangkat | — | ✓ | ✓ | ✓ |
| Developer | Cache sistem | — | ✓ | — | ✓ |
| Developer | Uji sintetis dan matriks izin | ✓ | ✓ | — | — |
| Developer | Seluruh resource akademik (bypass teknis) | ✓ | ✓ | ✓ | ✓ |
| Publik (belum login) | Halaman informasi (landing, panduan, FAQ) | — | ✓ | — | — |
| Publik (belum login) | Registrasi, login, OTP, reset kata sandi | ✓ | — | — | — |

Keterangan atau kondisi khusus:

1. **Kepemilikan data (ownership).** Tanda ✓ pada mahasiswa selalu berarti "data miliknya sendiri", bukan seluruh tabel. Sistem membandingkan identitas pengguna dengan `mahasiswa_id` pada setiap operasi baca dan unggah. Mahasiswa yang mencoba mengakses data mahasiswa lain ditolak walaupun perannya benar.
2. **Lingkup penugasan (scoping).** Koordinator dibatasi pada semester dan track yang ditugaskan, sehingga tanda ✓ pada "daftar mahasiswa" berarti mahasiswa dalam lingkup penugasannya, bukan seluruh prodi.
3. **Relasi pembimbing.** Tanda ✓ pada dosen untuk catatan bimbingan dan laporan berarti hanya untuk mahasiswa yang menjadi bimbingannya, diperiksa lewat relasi penugasan pembimbing.
4. **Tidak ada hak hapus pada data akademik.** Hampir seluruh role tidak memiliki hak Delete. Penghapusan hanya dimiliki admin (akun pengguna), kaprodi (pencabutan penugasan koordinator), dan developer (cache serta perangkat). Data akademik seperti bimbingan, proposal, laporan, dan sidang bersifat *append-only* dengan perubahan status, bukan dihapus. Ini keputusan desain yang disengaja untuk menjaga integritas bukti akademik.
5. **Pemisahan kewenangan akademik dan teknis.** Admin tidak memperoleh hak atas penetapan koordinator, status mengulang, maupun konversi track. Sebaliknya kaprodi tidak memperoleh akses konfigurasi sistem.
6. **Catatan implementasi saat ini.** Matriks di atas adalah rancangan yang seharusnya berlaku. Pada kode saat ini terdapat tiga penyimpangan yang perlu diperbaiki agar matriks ini benar-benar ditegakkan: (a) fungsi pemeriksaan peran memberi admin akses lolos ke hampir seluruh rute akademik; (b) terdapat satu endpoint pemeliharaan basis data yang masih dapat diakses tanpa autentikasi; (c) daftar dosen beserta nomor WhatsApp dan daftar perannya dapat dibaca tanpa autentikasi. Ketiganya dirinci pada Lampiran.

# 5. ANALISIS HAK AKSES

## 5.1 Role dengan Kewenangan Tertinggi

Kewenangan tertinggi dipegang oleh **developer**, disusul **admin** pada sisi administratif, dan **kaprodi** pada sisi akademik.

**Developer** berada di puncak karena ia melewati seluruh pemeriksaan peran di dalam sistem, termasuk rute yang khusus disediakan untuk kaprodi. Alasannya bersifat operasional: developer harus mampu menangani insiden, memulihkan data, dan mengaudit sistem ketika jalur normal gagal. Karena kewenangannya menyeluruh, role ini diberi kontrol tambahan berupa pengikatan identitas pada konfigurasi secret dan validasi perangkat, sehingga akun developer tidak dapat dipakai dari perangkat sembarangan.

**Kaprodi** adalah otoritas tertinggi dalam ranah akademik. Ia menetapkan koordinator per semester, mengubah status mengulang, dan mengonversi track. Kewenangan ini sengaja **tidak** diberikan kepada admin, karena keputusan akademik harus tetap berada di tangan pemegang jabatan akademik, bukan staf administrasi.

**Admin** memiliki kewenangan luas tetapi bersifat administratif: mengelola akun, mereset kata sandi, dan membaca log. Admin tidak memutuskan hal akademik.

## 5.2 Resource Paling Sensitif

Empat kelompok resource paling sensitif, berurutan:

1. **Kredensial dan sesi** (kata sandi ter-hash, token sesi, kode OTP). Jika bocor, penyerang dapat mengambil alih akun tanpa perlu menembus lapisan lain. Dampaknya berantai: akun kaprodi yang diambil alih dapat mengubah status akademik mahasiswa.
2. **Log audit dan log autentikasi.** Data ini memuat jejak perilaku seluruh pengguna beserta alamat IP. Jika dimodifikasi atau dihapus, sistem kehilangan kemampuan membuktikan siapa melakukan apa — justru pada saat bukti paling dibutuhkan.
3. **Hasil sidang dan catatan sidang.** Ini menentukan kelulusan. Perubahan tidak sah berdampak langsung pada status akademik mahasiswa dan sulit dibatalkan secara sosial walaupun secara teknis bisa diperbaiki.
4. **Dokumen akademik dan catatan bimbingan.** Memuat karya intelektual mahasiswa dan penilaian dosen. Kebocoran berisiko plagiarisme, sedangkan perubahan tanpa izin merusak integritas penilaian.

Dampak bila diakses pihak tidak berwenang: pengambilalihan akun, manipulasi nilai akademik, kebocoran data pribadi (nama, NPM, email, nomor WhatsApp), hilangnya bukti audit, dan hilangnya kepercayaan pada sistem sebagai satu-satunya sumber kebenaran proses akademik.

## 5.3 Pembatasan Hak Akses

| **Resource** | **Role** | **Hak yang Dibatasi** | **Alasan** |
|--------------|----------|-----------------------|------------|
| Proposal (berkas dan status) | Mahasiswa | U dan D | Proposal yang sudah diajukan bersifat sebagai bukti pengajuan. Jika mahasiswa bebas mengubah atau menghapusnya setelah divalidasi, koordinator kehilangan objek yang divalidasi. Perubahan harus melalui pengajuan ulang yang tercatat, bukan penimpaan diam-diam |
| Proposal (berkas dan status) | Dosen dan Penguji | U dan D | Dosen dan penguji tidak memiliki kewenangan menilai proposal; kewenangan validasi ada pada koordinator dan kaprodi. Membuka hak ini akan menciptakan jalur persetujuan kedua yang tidak tercatat dalam alur resmi |
| Jadwal sidang | Dosen dan Penguji | C, U, D | Penetapan jadwal harus mempertimbangkan bentrok tanggal, jam, ruangan, pembimbing, dan penguji lintas mahasiswa. Kewenangan ini dipusatkan pada koordinator agar aturan bentrok hanya dijalankan di satu tempat |
| Jadwal sidang | Mahasiswa | C, U, D | Mahasiswa adalah pihak yang dijadwalkan, bukan yang menjadwalkan. Memberi hak ini membuka peluang mengatur jadwal yang menguntungkan diri sendiri |
| Hasil sidang | Mahasiswa | C dan U | Mahasiswa tidak boleh menetapkan hasil sidangnya sendiri. Akses dibatasi hanya membaca, dan itu pun setelah hasil ditetapkan |
| Hasil sidang | Dosen pembimbing | U | Untuk menjaga independensi penilaian, hasil sidang ditetapkan oleh penguji bersama koordinator dan kaprodi, bukan oleh pembimbing yang mendampingi mahasiswa sehari-hari |
| Penetapan koordinator | Admin | C, U, D | Penetapan koordinator adalah keputusan akademik berbasis jabatan struktural. Jika admin dapat melakukannya, pemisahan kewenangan akademik dan administratif tidak lagi bermakna |
| Data mahasiswa | Mahasiswa dan Dosen | C, R (menyeluruh), D | Tidak ada kebutuhan bisnis bagi mahasiswa untuk membaca data mahasiswa lain, dan dosen hanya memerlukan mahasiswa bimbingannya. Membuka akses menyeluruh akan membocorkan data pribadi tanpa manfaat fungsional |
| Akun pengguna | Admin | C | Pembuatan akun pengguna dilakukan melalui registrasi mandiri (mahasiswa) atau penambahan data dosen oleh admin. Admin tidak diberi hak membuat akun bertipe developer, karena pembuatan superuser harus berada di luar kendali administrator aplikasi |
| Log audit | Admin dan Developer | U dan D | Log audit bersifat *append-only*. Jika dapat diubah atau dihapus, fungsi pembuktian hilang dan pelaku penyalahgunaan dapat menutup jejaknya sendiri |

## 5.4 Prinsip Least Privilege

Penerapan least privilege pada rancangan ini terlihat dari lima hal berikut.

**Pertama, default deny.** Fungsi pemeriksaan peran bekerja dengan cara menolak lebih dulu: pengguna hanya diloloskan bila perannya secara eksplisit terdaftar pada rute tersebut. Tidak ada rute akademik yang "terbuka secara default lalu difilter belakangan".

**Kedua, pemisahan berdasarkan prefix rute.** Setiap kelompok peran memiliki ruang rute sendiri (mahasiswa, dosen, penguji, koordinator, kaprodi, admin, developer). Artinya kewenangan melekat pada jalur akses, sehingga kesalahan memberi izin pada satu rute tidak otomatis membuka rute lain.

**Ketiga, pemeriksaan berlapis.** Urutan pemeriksaannya adalah autentikasi → peran → kepemilikan atau lingkup. Contohnya pada unggah lampiran bimbingan: pengguna harus login, lalu sistem memeriksa apakah ia mahasiswa pemilik data atau memiliki peran istimewa. Jadi memiliki peran yang benar saja belum cukup.

**Keempat, pembatasan horizontal dan vertikal.** Pembatasan vertikal terlihat dari mahasiswa yang tidak bisa mengakses rute koordinator. Pembatasan horizontal terlihat dari koordinator yang hanya melihat track yang ditugaskan dan dosen yang hanya melihat mahasiswa bimbingannya — keduanya memiliki peran yang sah, tetapi ruang lingkupnya dipersempit.

**Kelima, pengecualian yang dibuat sempit dan eksplisit.** Ada pemeriksaan khusus yang hanya meloloskan kaprodi dan secara eksplisit mengecualikan admin. Ini kebalikan dari pola "semua yang berwenang boleh", dan merupakan bentuk least privilege yang paling ketat: kewenangan sekecil mungkin, diberikan hanya kepada pihak yang benar-benar membutuhkannya.

Selain itu, untuk role dengan kewenangan tertinggi diterapkan penguncian perangkat. Developer tidak hanya diverifikasi lewat kata sandi, tetapi juga lewat perangkat terdaftar yang tokennya disimpan dalam bentuk hash. Ini menurunkan dampak bila kredensial developer bocor.

## 5.5 Risiko Kesalahan Hak Akses

| **No.** | **Risiko** | **Dampak terhadap Sistem** |
|---------|------------|----------------------------|
| 1 | Pemberian hak baca menyeluruh kepada peran yang seharusnya terbatas (misalnya mahasiswa dapat membaca data mahasiswa lain) | Kebocoran data pribadi (nama, NPM, email, nomor WhatsApp) dalam skala massal; pelanggaran privasi; data dapat dipakai untuk penipuan atau doxing; reputasi prodi dan kampus terdampak |
| 2 | Pemberian hak Update pada data akademik yang seharusnya hanya ditambah (proposal, laporan, hasil sidang) | Manipulasi status akademik: proposal yang ditolak diubah menjadi disetujui, atau hasil sidang diubah menjadi lulus. Bukti asli hilang karena data ditimpa, sehingga sengketa akademik tidak dapat diselesaikan berbasis rekam digital |
| 3 | Akumulasi kewenangan pada satu akun (gabungan admin dan keputusan akademik) | Menghilangkan pemisahan tugas (*separation of duties*). Satu akun yang bocor atau disalahgunakan dapat menyetujui proposal, menetapkan pembimbing, dan mengubah hasil sidang tanpa ada pihak lain yang memverifikasi |
| 4 | Kewenangan teknis (developer) yang tidak dikunci pada perangkat atau tidak diaudit | Pengambilalihan penuh sistem: penyerang dapat membaca konfigurasi, mengakses basis data langsung, dan menghapus jejak audit sehingga insiden tidak dapat direkonstruksi |
| 5 | Tidak adanya hak hapus yang terkontrol disertai hak hapus yang terlalu luas pada akun | Sebaliknya dari risiko sebelumnya: penghapusan akun tanpa prosedur dapat menghilangkan riwayat akademik yang terhubung, memutus relasi data bimbingan dan sidang, serta merusak integritas laporan |
| 6 | Pemeriksaan hanya di antarmuka pengguna, tidak di sisi API | Pembatasan dapat dilewati dengan memanggil API secara langsung. Antarmuka hanya menyembunyikan tombol; tanpa pemeriksaan di server, seluruh rancangan akses menjadi ilusi |

## 5.6 Respons terhadap Akses Tidak Sah

Rancangan respons berlapis sebagai berikut.

**Lapisan 1 — Penolakan di server.** Permintaan tanpa identitas yang sah dijawab dengan kode 401 dan pesan umum, tanpa menjelaskan data apa yang sebenarnya ada. Permintaan yang identitasnya sah tetapi perannya tidak berwenang dijawab dengan kode 403. Pesan kesalahan sengaja tidak menyebut detail internal agar tidak membantu penyerang memetakan sistem.

**Lapisan 2 — Penolakan tetap dicatat.** Setiap percobaan akses yang ditolak idealnya dicatat ke log audit bersama identitas pengguna, aksi, target, hasil, alamat IP, dan user agent. Saat ini pencatatan audit sudah tersedia untuk aksi sensitif (autentikasi, pengelolaan akun, aksi developer), namun penolakan otorisasi belum tercatat otomatis — ini termasuk perbaikan yang diusulkan pada Lampiran.

**Lapisan 3 — Pembatasan laju.** Endpoint autentikasi dilindungi pembatasan laju yang lebih ketat dibanding endpoint umum, sehingga percobaan menebak kredensial atau membanjiri sistem akan dijawab dengan kode 429 sebelum mencapai pemeriksaan bisnis.

**Lapisan 4 — Respons di sisi klien.** Ketika API menjawab 401 dengan kode kesalahan sesi (tidak terautentikasi, sesi kedaluwarsa, token tidak valid), antarmuka menghapus sesi lokal dan mengarahkan pengguna kembali ke halaman login. Bila pengguna mencoba membuka halaman dashboard yang bukan haknya, ia diarahkan ke dashboard yang sesuai dengan perannya, bukan dibiarkan melihat halaman kosong.

**Lapisan 5 — Prinsip tidak membocorkan keberadaan data.** Penolakan tidak membedakan antara "data tidak ada" dan "data ada tetapi Anda tidak berhak". Perbedaan pesan semacam itu akan berfungsi sebagai alat enumerasi bagi penyerang.

# 6. VALIDASI RANCANGAN

| **No.** | **Pernyataan**                                            | **Ya** | **Tidak** |
|---------|-----------------------------------------------------------|--------|-----------|
| 1       | Profil aplikasi telah dijelaskan dengan jelas             | ✓      |           |
| 2       | Seluruh role memiliki fungsi yang jelas                   | ✓      |           |
| 3       | Fitur utama aplikasi telah diidentifikasi                 | ✓      |           |
| 4       | Resource penting telah ditentukan tingkat sensitivitasnya | ✓      |           |
| 5       | Setiap resource memiliki aturan hak akses                 | ✓      |           |
| 6       | Hak akses sesuai dengan tanggung jawab masing-masing role | ✓      |           |
| 7       | Tidak terdapat hak akses berlebihan tanpa alasan          | ✓*     |           |
| 8       | Prinsip least privilege telah diterapkan                  | ✓      |           |

\* Dinyatakan "Ya" pada tingkat rancangan. Pada tingkat implementasi masih terdapat tiga penyimpangan yang dirinci pada Lampiran dan perlu diperbaiki agar pernyataan ini benar sepenuhnya.

# 7. KESIMPULAN

Rancangan Access Control pada aplikasi Kavana dibangun di atas tujuh peran yang memisahkan dua jenis kewenangan: kewenangan akademik (kaprodi, koordinator, dosen pembimbing, penguji) dan kewenangan teknis (admin, developer), dengan mahasiswa sebagai pengguna berhak paling terbatas. Pemisahan ini penting karena keputusan akademik tidak boleh berada di tangan pengelola teknis, dan sebaliknya konfigurasi sistem tidak boleh berada di tangan pemegang jabatan akademik.

Matriks akses menunjukkan pola yang konsisten dengan prinsip least privilege: hak baca dibatasi pada kepemilikan data dan lingkup penugasan, hak ubah dibatasi pada pihak yang memang memiliki kewenangan menetapkan status, dan hak hapus hampir tidak diberikan sama sekali pada data akademik karena data tersebut berfungsi sebagai bukti. Data akademik dirancang bersifat *append-only* dengan perubahan status, bukan penghapusan.

Tiga mekanisme teknis memperkuat rancangan ini: pemeriksaan berlapis (autentikasi, peran, kepemilikan), penguncian perangkat untuk role berkewenangan tertinggi, dan pencatatan audit untuk aksi sensitif. Ketiganya menurunkan dampak bila satu lapisan gagal.

Namun rancangan yang baik tidak otomatis menjadi sistem yang aman. Analisis ini menemukan tiga titik di mana implementasi belum sejalan dengan rancangan: adanya endpoint pemeliharaan basis data yang masih terbuka tanpa autentikasi, daftar dosen yang dapat dibaca tanpa autentikasi, dan fungsi pemeriksaan peran yang meloloskan admin ke hampir seluruh rute akademik. Ketiganya bukan kelemahan rancangan, melainkan selisih antara rancangan dan implementasi yang harus ditutup. Selama selisih itu ada, matriks pada bagian 4 belum sepenuhnya ditegakkan oleh sistem, dan pernyataan pada bagian 6 poin 7 baru berlaku pada tingkat rancangan.

---

# LAMPIRAN — Bukti Teknis dan Selisih Rancangan terhadap Implementasi

Lampiran ini bukan bagian dari template tugas, tetapi disertakan agar setiap keputusan pada matriks dapat ditelusuri ke kode yang sebenarnya.

## A.1 Sumber Data Analisis

| **Aspek** | **Jumlah / Keterangan** |
|-----------|--------------------------|
| Endpoint API | 125 endpoint pada 19 berkas rute |
| Tabel basis data | 19 tabel, 19 foreign key, 46 index |
| Migrasi skema | 28 berkas migrasi SQL |
| Peran tersimpan di tabel `role` | 4 (dosen, koordinator, kaprodi, penguji) — peran ini melekat pada dosen melalui tabel pivot `dosen_role`, sehingga satu dosen dapat memegang beberapa peran sekaligus |
| Peran tersimpan pada akun | mahasiswa, admin, developer (kolom `role` pada tabel `user`) |
| Halaman antarmuka | 81 halaman |
| Berkas pengujian | 38 berkas uji otomatis (Selenium) |

## A.2 Penerapan Hierarki Peran

Hierarki yang berlaku pada kode (`middleware/role.js`):

- developer — lolos seluruh pemeriksaan peran.
- admin — lolos seluruh pemeriksaan peran kecuali rute yang mensyaratkan developer.
- koordinator — dapat mengakses rute dosen.
- kaprodi — dapat mengakses rute dosen dan rute koordinator.
- penguji — dapat mengakses rute dosen.
- Pemeriksaan khusus kaprodi secara eksplisit mengecualikan admin.

## A.3 Selisih yang Ditemukan

| **No.** | **Temuan** | **Lokasi** | **Dampak** | **Usulan Perbaikan** |
|---------|------------|------------|------------|----------------------|
| 1 | Endpoint pemeliharaan basis data dapat dipanggil tanpa autentikasi dan menjalankan perintah pembuatan serta perubahan tabel | `routes/authRoutes.js` (rute `fix-schema`), `controllers/authController.js` (fungsi `runSchemaFix`) | Siapa pun yang mengetahui URL-nya dapat memicu perubahan skema pada basis data produksi, memicu penguncian tabel, dan membuat sistem tidak stabil. Tidak ada pencatatan audit | Hapus rute tersebut; perubahan skema dijalankan melalui berkas migrasi |
| 2 | Daftar dosen dapat dibaca tanpa autentikasi, dan keluarannya memuat nama, NIDN, nomor WhatsApp, serta daftar peran dosen | `routes/dosenRoutes.js` (rute `list`), `controllers/sharedController.js` (fungsi `getActiveDosen`) | Nomor WhatsApp dosen dan informasi peran (termasuk siapa koordinator/kaprodi) terbuka untuk publik dan dapat dipanen untuk penipuan atau rekayasa sosial | Tambahkan autentikasi, atau batasi keluaran hanya pada kolom yang memang diperlukan untuk pemilihan pembimbing (id dan nama) |
| 3 | Fungsi pemeriksaan peran meloloskan admin ke hampir seluruh rute akademik | `middleware/role.js` | Menghilangkan pemisahan tugas antara admin dan kaprodi/koordinator | Ganti pola "admin lolos semua kecuali developer" menjadi daftar peran eksplisit per rute akademik |
| 4 | Penolakan otorisasi tidak tercatat otomatis ke log audit | `middleware/role.js`, `middleware/audit.js` | Percobaan akses tidak sah tidak dapat direkonstruksi | Panggil pencatatan audit dari dalam pemeriksaan peran saat terjadi penolakan |
| 5 | Terdapat berkas kebijakan peran yang tidak pernah digunakan | `middleware/policyRole.js` | Rancangan kebijakan peran yang lebih rinci tidak pernah aktif, sehingga kewenangan sepenuhnya bergantung pada pemeriksaan peran sederhana | Aktifkan dan uji, atau hapus agar tidak menyesatkan pembaca kode |
| 6 | Token akses disimpan di penyimpanan sesi peramban dan dikirim melalui header, sementara mekanisme cookie httpOnly sudah tersedia namun belum dipakai antarmuka | `src/lib/api.js`, `src/store/auth-store.js` pada frontend; `config/auth.js` pada backend | Token rentan dicuri melalui skrip lintas situs (XSS), dan keunggulan cookie httpOnly tidak terpakai | Migrasikan antarmuka ke mekanisme sesi berbasis cookie yang sudah dikonfigurasi |
| 7 | Kredensial kata sandi tersimpan pada tabel bisnis (mahasiswa dan dosen) sekaligus pada tabel akun autentikasi | `controllers/authController.js`, `controllers/adminController.js`, tabel `mahasiswa` dan `dosen` | Dua sumber kredensial yang harus dijaga konsisten; menambah permukaan serangan | Satukan pada satu sumber kredensial (tabel akun autentikasi) |
| 8 | Nomor berkas migrasi ganda | `migrations/010_better_auth_audit.sql` dan `migrations/010_support_multi_koordinator_assignment.sql` | Urutan penerapan skema menjadi ambigu saat membangun basis data baru | Beri nomor unik dan jalankan berurutan |

## A.4 Kontrol Keamanan yang Sudah Berjalan

Agar penilaian berimbang, berikut kontrol yang sudah diterapkan dan relevan dengan Access Control:

1. Pembatasan tipe dan ukuran berkas unggahan (whitelist MIME, maksimum 10 MB) serta sanitasi ekstensi berkas.
2. Pemeriksaan kepemilikan pada unggahan lampiran bimbingan.
3. Pembatasan laju tiga tingkat: autentikasi, area admin/developer, dan umum.
4. CAPTCHA Cloudflare Turnstile pada login.
5. OTP email untuk registrasi dan reset kata sandi, dengan tabel dan masa berlaku tersendiri.
6. Penyaringan domain email sementara (disposable email) dan validasi format email.
7. Penguncian perangkat untuk akun developer berbasis hash token perangkat.
8. Verifikasi tanda tangan pada endpoint pekerjaan latar (QStash).
9. Header keamanan (Helmet + Content Security Policy) dan pembatasan origin CORS.
10. Pencatatan audit untuk aksi sensitif pada autentikasi, pengelolaan akun, dan aksi developer.
11. Pesan kesalahan umum pada kegagalan unggah, tanpa membocorkan detail internal.

*— Akhir Dokumen —*


---

## 4. DEVELOPER ROLE & DEVICE LOCKING PLAN (DEVELOPER_ROLE_ACCESS_PLAN.md)

> **Sumber Berkas Asli:** `DEVELOPER_ROLE_ACCESS_PLAN.md`

# Plan Role Developer - Full Access & Device Lock

## Tujuan
- Menambahkan role `developer` sebagai role tertinggi di sistem Kavana.
- Role `developer` memiliki akses penuh ke seluruh modul website, termasuk fitur yang dimiliki admin, kaprodi, koordinator, dosen, dan mahasiswa.
- Role `developer` berada di atas `admin` dan `kaprodi` dalam hierarki otorisasi.
- Akun developer dibuat dari environment variable/GitHub Secret seperti akun admin.
- Login developer hanya boleh dari device yang sudah terdaftar.

## Catatan Penting Tentang MAC Address
- Website biasa tidak bisa membaca MAC address asli client dari browser.
- Backend juga tidak menerima MAC address melalui HTTP request.
- Extension, DevTools, atau JavaScript frontend tidak boleh dijadikan sumber MAC address karena mudah dipalsukan dan dibatasi browser.
- Implementasi yang realistis adalah memakai `registered device` berbasis `device_id`, fingerprint terbatas, signed device token, passkey/MFA, dan optional MAC hash hanya jika ada agent/internal tool yang memang bisa membaca MAC secara sah.

## Hierarki Role
- `developer`: akses penuh seluruh sistem, termasuk konfigurasi teknis dan audit.
- `admin`: manajemen operasional umum.
- `kaprodi`: manajemen akademik tingkat program studi.
- `koordinator`: validasi, periode, pembimbing, jadwal, dan monitoring sesuai track yang diassign.
- `dosen`: bimbingan, approval, laporan, nilai, jadwal terkait.
- `mahasiswa`: proyek/internship, proposal, bimbingan, laporan, hasil akhir.

## Hak Akses Developer
- Bisa melihat seluruh dashboard tanpa filter role.
- Bisa impersonate user untuk debugging dengan audit log wajib.
- Bisa reset password semua role.
- Bisa mengaktifkan, menonaktifkan, dan mengunci akun.
- Bisa mengubah role user.
- Bisa melihat dan mengelola semua data mahasiswa, dosen, koordinator, kaprodi, admin.
- Bisa melihat semua proposal, bimbingan, laporan sidang, revisi, nilai, jadwal sidang, periode, track, dan kelompok.
- Bisa mengakses audit log, auth log, error log, email log, realtime event log, dan cache log.
- Bisa clear cache Redis per key/module atau seluruh cache.
- Bisa trigger refresh event realtime secara manual.
- Bisa melihat health check backend, database, Redis, email provider, dan Turnstile.
- Bisa menjalankan maintenance mode.
- Bisa mengatur konfigurasi sistem non-secret dari dashboard.
- Bisa export data audit dan laporan teknis.
- Bisa membuka Developer Control Center sebagai pusat kendali teknis seluruh aplikasi.
- Bisa melihat semua request API yang gagal, status code, payload aman, trace id, dan latency.
- Bisa melihat dependency health untuk database, Redis, Cloudflare Turnstile, email provider, storage, dan external API.
- Bisa mengatur feature flag untuk mengaktifkan atau menonaktifkan fitur tertentu tanpa deploy ulang.
- Bisa mengatur kill switch untuk mematikan sementara fitur bermasalah seperti register, login, upload proposal, export dokumen, realtime, atau email notification.
- Bisa melakukan force logout semua user atau role tertentu.
- Bisa revoke seluruh session user tertentu.
- Bisa mengunci sistem menjadi read-only mode saat maintenance.
- Bisa menjalankan data repair tool yang sudah disediakan backend.
- Bisa melakukan dry-run aksi sensitif sebelum benar-benar dieksekusi.
- Bisa melihat preview efek perubahan konfigurasi sebelum disimpan.
- Bisa mengakses panel debug frontend untuk melihat state user, role, permission, dan endpoint yang dipakai.
- Bisa melihat daftar versi build frontend/backend yang sedang aktif.
- Bisa trigger cache warmup setelah deploy atau setelah data besar berubah.
- Bisa trigger ulang email notification yang gagal.
- Bisa trigger ulang realtime event yang gagal terkirim.
- Bisa export snapshot konfigurasi sistem.
- Bisa membandingkan konfigurasi production dengan konfigurasi terakhir yang valid.

## Fitur Developer Tingkat Lanjut
- `Developer Control Center`: halaman utama untuk melihat status sistem, error terbaru, traffic, cache, event realtime, dan health check.
- `Permission Matrix Inspector`: melihat izin efektif setiap role dan alasan kenapa user boleh atau tidak boleh mengakses fitur tertentu.
- `Impersonation Mode`: masuk sebagai user lain untuk debugging, dengan banner jelas dan audit log wajib.
- `User Session Manager`: melihat session aktif, revoke session, force logout, dan cek device login.
- `Feature Flag Manager`: mengaktifkan atau mematikan fitur per role, per user, atau global.
- `Maintenance Mode Manager`: mengaktifkan maintenance mode penuh, partial maintenance, atau read-only mode.
- `API Request Log Viewer`: melihat endpoint mana yang sering error, lambat, atau ditolak CORS/auth.
- `Error Trace Viewer`: melihat stack trace aman, request id, execution id, dan korelasi ke log Cloud Run.
- `Realtime Event Monitor`: melihat event yang dikirim, subscriber aktif, event gagal, dan retry event.
- `Redis Cache Explorer`: melihat key cache, TTL, hit/miss, clear per module, clear per user, dan warmup cache.
- `Email Delivery Monitor`: melihat email terkirim, gagal, pending, retry, dan alasan gagal.
- `Database Inspector Read-Only`: melihat tabel penting, jumlah row, relasi data, dan data broken tanpa bisa menjalankan SQL bebas.
- `Safe Data Repair Tools`: tombol perbaikan data yang sudah ditentukan, misalnya sinkronisasi kelompok, proposal, pembimbing, bimbingan, jadwal, dan status mahasiswa.
- `Orphan Data Scanner`: mendeteksi data yatim seperti proposal tanpa mahasiswa, bimbingan tanpa pembimbing, jadwal tanpa peserta, atau user tanpa profile.
- `Duplicate Data Scanner`: mendeteksi duplikasi NPM, email, proposal, kelompok, jadwal, dan assignment.
- `Config Snapshot & Rollback`: menyimpan snapshot konfigurasi dan rollback ke konfigurasi sebelumnya.
- `Build Info Panel`: melihat commit hash, branch, waktu deploy, environment, dan versi package penting.
- `Deployment Guard`: menandai deploy bermasalah dan memberi opsi rollback manual lewat prosedur aman.
- `Webhook/Event Simulator`: mengirim event simulasi untuk mengetes notifikasi, realtime refresh, email, dan dashboard.
- `Document Export Debugger`: mengetes export PDF/Word dengan sample data, melihat error template, dan preview metadata.
- `Turnstile Debugger`: cek status site key, secret key validation, token verification result, dan alasan challenge gagal.
- `CORS Debugger`: cek origin yang diizinkan, preflight result, header response, dan endpoint yang ditolak.
- `Security Audit Panel`: melihat percobaan login gagal, device tidak dikenal, token invalid, akses role ditolak, dan request mencurigakan.
- `Rate Limit Monitor`: melihat IP/user yang terkena limit, reset limit, atau block sementara.
- `Notification Debugger`: melihat notifikasi in-app/email/realtime untuk setiap user dan mengirim test notification.
- `File/Upload Inspector`: melihat metadata upload proposal/laporan, link rusak, dan status validasi file.
- `Academic Flow Inspector`: melihat status flow mahasiswa dari register, pilih track, kelompok, proposal, pembimbing, bimbingan, laporan, sidang, revisi, sampai nilai akhir.
- `Rule Engine Preview`: preview hasil aturan sistem, misalnya apakah mahasiswa boleh upload proposal, boleh bimbingan, boleh sidang, atau harus ngulang.
- `Manual Override Dengan Alasan`: developer bisa override status tertentu, tetapi wajib mengisi alasan dan semua perubahan masuk audit log.
- `Emergency Admin Recovery`: membuat ulang akun admin/kaprodi jika terkunci, dengan validasi device developer dan audit log.
- `System Announcement`: membuat banner pengumuman global di semua dashboard.
- `Data Export Center`: export data teknis dan akademik untuk backup atau audit.
- `Synthetic Test Runner`: menjalankan test ringan dari dashboard, misalnya login test, DB ping, Redis ping, email ping, dan export document test.
- `Background Job Runner`: menjalankan job yang aman seperti clear expired sessions, cleanup cache, resend failed email, rebuild derived status, dan sync realtime counters.
- `Danger Zone`: kumpulan aksi sangat sensitif yang butuh re-auth, MFA, alasan, dan konfirmasi ganda.

## Batas Aman Developer Console
- Developer tidak diberi fitur menjalankan SQL mentah langsung dari browser production.
- Developer tidak diberi fitur menjalankan shell command bebas dari dashboard.
- Developer tidak diberi fitur melihat secret mentah seperti JWT secret, DB password, Redis token, Turnstile secret, atau Mailtrap token.
- Developer hanya boleh melihat status `configured/not configured`, hash pendek, atau masked value untuk secret.
- Aksi destructive harus disediakan sebagai command aman yang sudah dibuat backend, bukan input bebas.
- Semua fitur seperti data repair, cache clear, force logout, dan override status harus memakai audit log.
- Fitur `Danger Zone` wajib MFA ulang, device aktif, reason wajib, dan confirmation phrase.

## Fitur Keamanan Developer
- Login developer wajib melewati validasi credential, Turnstile, device allowlist, dan MFA/passkey.
- Session developer lebih pendek dari role lain, misalnya 30-60 menit.
- Refresh token developer wajib rotate dan bisa dicabut manual.
- Semua aktivitas developer wajib masuk audit log.
- Aksi sensitif wajib re-auth password/MFA, misalnya reset password, ubah role, clear cache, impersonate, hapus data.
- Developer tidak boleh memakai endpoint auth biasa tanpa device check.
- Endpoint developer diberi rate limit lebih ketat.
- Token developer diberi claim khusus `role=developer`, `device_id`, `session_level=privileged`.

## Device Lock
- Buat tabel `developer_devices`.
- Kolom minimal:
  - `id`
  - `developer_email`
  - `device_id`
  - `device_name`
  - `mac_hash` optional
  - `fingerprint_hash`
  - `public_key` optional untuk passkey/device key
  - `status`: `pending`, `active`, `revoked`
  - `last_ip`
  - `last_user_agent`
  - `last_login_at`
  - `created_at`
  - `revoked_at`
- Saat login developer, backend cek apakah `device_id` aktif dan terdaftar.
- Jika device belum terdaftar, login ditolak dengan pesan umum.
- Enrollment device hanya bisa lewat GitHub Secret/bootstrap/manual DB insert oleh owner.

## Strategi Device ID
- Frontend membuat atau membaca device token dari secure storage/browser storage.
- Token device bukan sekadar UUID biasa, tapi signed token dari backend saat enrollment.
- Backend menyimpan hash token, bukan token mentah.
- Saat login, frontend kirim `device_id` dan `device_token`.
- Backend validasi hash token, status device, user agent risk, dan optional IP risk.
- Untuk keamanan lebih kuat, gunakan WebAuthn/passkey agar device benar-benar terikat ke perangkat.

## GitHub Secret Yang Dibutuhkan
- `DEVELOPER_EMAIL`: email akun developer utama.
- `DEVELOPER_PASSWORD`: password awal developer.
- `DEVELOPER_NAME`: nama tampilan developer.
- `DEVELOPER_DEVICE_IDS`: daftar device ID yang diizinkan, dipisah koma.
- `DEVELOPER_DEVICE_TOKEN_HASHES`: daftar hash token device, dipisah koma.
- `DEVELOPER_MAC_HASHES`: optional, hanya jika ada metode enrollment MAC yang valid.
- `DEVELOPER_MFA_REQUIRED`: `true`.
- `DEVELOPER_SESSION_TTL_MINUTES`: contoh `60`.
- `DEVELOPER_IMPERSONATION_ENABLED`: `true` atau `false`.

## Perubahan Backend
- Tambah role enum/constant `developer`.
- Tambah middleware `requireDeveloper`.
- Tambah middleware `requirePrivilegedDevice`.
- Update middleware role hierarchy agar `developer` bisa melewati semua role check.
- Tambah bootstrap developer dari environment variable saat server start atau command seed.
- Tambah endpoint internal:
  - `GET /api/developer/health`
  - `GET /api/developer/audit-logs`
  - `GET /api/developer/auth-logs`
  - `GET /api/developer/cache/keys`
  - `DELETE /api/developer/cache`
  - `POST /api/developer/users/:id/reset-password`
  - `POST /api/developer/users/:id/impersonate`
  - `POST /api/developer/devices/enroll`
  - `POST /api/developer/devices/:id/revoke`
  - `GET /api/developer/permissions/matrix`
  - `GET /api/developer/sessions`
  - `POST /api/developer/sessions/revoke`
  - `POST /api/developer/sessions/force-logout`
  - `GET /api/developer/feature-flags`
  - `PUT /api/developer/feature-flags/:key`
  - `GET /api/developer/realtime/events`
  - `POST /api/developer/realtime/events/replay`
  - `GET /api/developer/email/logs`
  - `POST /api/developer/email/retry`
  - `GET /api/developer/redis/keys`
  - `DELETE /api/developer/redis/keys/:key`
  - `POST /api/developer/redis/warmup`
  - `GET /api/developer/system/config`
  - `POST /api/developer/system/config/snapshot`
  - `POST /api/developer/system/config/rollback`
  - `POST /api/developer/system/maintenance`
  - `POST /api/developer/system/read-only`
  - `GET /api/developer/data/orphans`
  - `GET /api/developer/data/duplicates`
  - `POST /api/developer/data/repair/:repairKey`
  - `POST /api/developer/rules/preview`
  - `POST /api/developer/override`
  - `POST /api/developer/tests/synthetic`
- Semua endpoint developer wajib audit log.

## Perubahan Frontend
- Tambah dashboard developer.
- Tambah sidebar khusus developer dengan menu:
  - Overview System
  - User & Role Management
  - Device Management
  - Audit Logs
  - Auth Logs
  - Error Logs
  - Redis Cache
  - Realtime Events
  - Email Logs
  - System Health
  - Maintenance Mode
  - Impersonation Console
  - Developer Control Center
  - Permission Matrix
  - Feature Flags
  - Session Manager
  - CORS Debugger
  - Turnstile Debugger
  - API Request Logs
  - Error Trace Viewer
  - Database Inspector
  - Data Repair Tools
  - Orphan/Duplicate Scanner
  - Job Runner
  - Rule Preview
  - Document Export Debugger
  - Danger Zone
- Login page perlu mengirim device token saat role developer terdeteksi.
- Jika device tidak terdaftar, tampilkan error aman: `Device tidak diizinkan untuk akun ini`.

## Database & Audit
- Tambah tabel `developer_devices`.
- Tambah tabel/kolom audit untuk:
  - `actor_user_id`
  - `actor_role`
  - `target_user_id`
  - `action`
  - `module`
  - `ip_address`
  - `user_agent`
  - `device_id`
  - `metadata`
  - `created_at`
- Semua aksi developer harus mencatat sebelum dan sesudah perubahan jika data sensitif.

## Risiko & Mitigasi
- Risiko: MAC address tidak bisa diverifikasi langsung dari browser.
- Mitigasi: gunakan device token + WebAuthn/passkey + MFA.
- Risiko: akun developer terlalu kuat.
- Mitigasi: session pendek, audit lengkap, re-auth untuk aksi sensitif, device allowlist, rate limit ketat.
- Risiko: GitHub Secret bocor.
- Mitigasi: jangan log secret, rotate berkala, simpan hash device token, gunakan password awal hanya untuk bootstrap.
- Risiko: impersonation disalahgunakan.
- Mitigasi: wajib alasan impersonation, audit log, durasi pendek, banner aktif saat impersonate.

## Tahapan Implementasi
1. Tambah role `developer` di backend dan database.
2. Tambah bootstrap akun developer dari env/GitHub Secret.
3. Tambah tabel `developer_devices`.
4. Tambah middleware role hierarchy dan device allowlist.
5. Tambah audit log untuk semua aksi developer.
6. Tambah endpoint developer untuk user management, logs, cache, health, dan device.
7. Tambah dashboard developer di frontend.
8. Tambah validasi login developer berbasis device token.
9. Tambah MFA/passkey untuk developer.
10. Uji login developer dari device terdaftar dan device tidak terdaftar.

## Acceptance Criteria
- Developer bisa login hanya jika credential benar dan device terdaftar aktif.
- Device tidak terdaftar tidak bisa masuk meskipun email dan password benar.
- Developer bisa mengakses semua menu dan semua data lintas role.
- Admin dan kaprodi tidak bisa mengakses menu developer.
- Semua aksi developer masuk audit log.
- Secret developer tidak pernah muncul di response API, log aplikasi, atau console frontend.
- Cache Redis, auth log, error log, dan health check bisa dilihat dari dashboard developer.


---

## 5. SIDANG CONFLICT VALIDATION PLAN (SIDANG_CONFLICT_VALIDATION_PLAN.md)

> **Sumber Berkas Asli:** `SIDANG_CONFLICT_VALIDATION_PLAN.md`

# Plan Fix Validasi Bentrok Jadwal Sidang Koordinator

## Ringkasan
- Tambahkan validasi agar jadwal sidang **tidak boleh bentrok** pada kombinasi `tanggal`, `waktu`, dan dosen yang terlibat.
- Bentrok harus dicek untuk **dosen pembimbing** (`dosen_id`) dan **penguji** (`penguji_id`).
- Jika pada slot yang sama salah satu dosen sudah dipakai di sidang lain, sistem harus menolak pembuatan jadwal baru.

## Kondisi Saat Ini
- Backend endpoint `scheduleSidang` di `nggk-tau-ini-apa/controllers/sidangController.js` saat ini hanya:
  - validasi field wajib
  - cek mahasiswa ada
  - cek mahasiswa punya pembimbing
  - cek penguji ada
  - lalu langsung `INSERT INTO sidang`
- Tidak ada query pengecekan bentrok dosen.
- Frontend koordinator pada `New-Frontend/src/app/dashboard/koordinator/jadwal-sidang/page.jsx` juga belum melakukan pre-check bentrok sebelum submit.
- Artinya kasus berikut **masih bisa terjadi**:
  - Sidang A: `5 Mei 2026`, `10:00`, dosen `1` dan penguji `2`
  - Sidang B: `5 Mei 2026`, `10:00`, dosen `2` dan penguji `3`
  - Dosen `2` bentrok, tetapi sistem saat ini tetap bisa menyimpan.

## Aturan Bentrok yang Harus Diterapkan
- Untuk satu `tanggal` dan `waktu` yang sama, seorang dosen **tidak boleh muncul lebih dari satu kali** pada tabel sidang.
- Dosen dianggap bentrok jika ia muncul sebagai:
  - `dosen_id` di sidang lain
  - `penguji_id` di sidang lain
- Bentrok juga berlaku silang:
  - calon `dosen_id` baru bentrok jika sudah ada sebagai `penguji_id`
  - calon `penguji_id` baru bentrok jika sudah ada sebagai `dosen_id`
- Jika `dosen_id` dan `penguji_id` yang dipilih sama orangnya, request harus ditolak.
- Jika mahasiswa yang sama sudah punya sidang aktif/terjadwal, request juga harus ditolak agar tidak ada jadwal ganda untuk mahasiswa yang sama.

## Perubahan Implementasi
### Backend
- Tambahkan validasi konflik di `scheduleSidang` sebelum proses `INSERT`.
- Buat query conflict check berbasis:
  - `tanggal = ?`
  - `waktu = ?`
  - dan salah satu dari dosen yang akan dipakai muncul pada `dosen_id` atau `penguji_id`
- Logic validasi minimal:
  - tolak jika `dosen_id === penguji_id`
  - tolak jika mahasiswa sudah punya sidang existing yang belum dibatalkan/selesai
  - tolak jika ada sidang lain pada slot yang sama dengan salah satu dosen yang sama
- Response error harus eksplisit, misalnya:
  - `Dosen pembimbing bentrok pada tanggal dan jam tersebut`
  - `Penguji bentrok pada tanggal dan jam tersebut`
  - `Mahasiswa sudah memiliki jadwal sidang`

### Database Integrity
- Tambahkan proteksi database agar race condition tidak membuat bentrok lolos saat dua request masuk bersamaan.
- Opsi yang disarankan:
  - jalankan validasi dan insert dalam transaction, lalu lock candidate rows yang relevan
  - atau buat constraint/index pendukung jika model tabel memungkinkan
- Karena bentrok melibatkan dua kolom role (`dosen_id` dan `penguji_id`), validasi aplikasi tetap wajib walaupun ada index tambahan.

### Frontend
- Frontend tetap submit seperti sekarang, tetapi error bentrok dari backend harus ditampilkan jelas ke koordinator.
- Opsional: tampilkan hint sebelum submit jika dosen/penguji yang dipilih terlihat bentrok dari data jadwal yang sudah dimuat.
- Sumber kebenaran tetap backend; frontend pre-check hanya tambahan UX, bukan validasi utama.

### Dokumentasi / Swagger
- Update deskripsi endpoint jadwal sidang agar menyebutkan bahwa slot akan ditolak jika dosen pembimbing atau penguji bentrok.
- Tambahkan contoh error response bentrok pada endpoint `/api/koordinator/sidang/schedule`.

## Perubahan API / Perilaku
- Endpoint tetap sama:
  - `POST /api/koordinator/sidang/schedule`
- Payload tetap sama:
  - `mahasiswa_id`
  - `tanggal`
  - `waktu`
  - `ruangan`
  - `penguji_id`
- Perubahan hanya pada rule validasi dan error response ketika slot bentrok.

## Tahapan Implementasi
1. Tambahkan validasi `dosen_id !== penguji_id`.
2. Tambahkan query pengecekan sidang existing untuk mahasiswa yang sama.
3. Tambahkan query conflict check dosen/penguji pada slot `tanggal + waktu`.
4. Bungkus validasi dan insert dalam transaction agar aman dari race condition.
5. Perbarui frontend agar error bentrok tampil jelas.
6. Perbarui Swagger/documentation untuk kasus bentrok.

## Test Plan
- Sidang baru berhasil dibuat jika slot kosong dan semua dosen tidak bentrok.
- Sidang ditolak jika `dosen_id` sudah dipakai sebagai pembimbing di slot yang sama.
- Sidang ditolak jika `dosen_id` sudah dipakai sebagai penguji di slot yang sama.
- Sidang ditolak jika `penguji_id` sudah dipakai sebagai pembimbing di slot yang sama.
- Sidang ditolak jika `penguji_id` sudah dipakai sebagai penguji di slot yang sama.
- Sidang ditolak jika `dosen_id` dan `penguji_id` adalah orang yang sama.
- Sidang ditolak jika mahasiswa yang sama sudah punya jadwal sidang.
- Error bentrok tampil jelas di halaman koordinator.

## Asumsi
- Bentrok dihitung pada kombinasi slot exact `tanggal + waktu`, bukan durasi rentang waktu.
- Scope fix ini fokus pada bentrok dosen dan mahasiswa pada jadwal sidang koordinator.
- Jika nanti ada status sidang seperti `cancelled`, rule bentrok bisa dikecualikan hanya untuk status yang tidak aktif lagi.


---

## 6. SIDANG PREREQUISITE PLAN (SIDANG_PREREQUISITE_PLAN.md)

> **Sumber Berkas Asli:** `SIDANG_PREREQUISITE_PLAN.md`

# Plan Validasi Prasyarat Jadwal Sidang

## Tujuan
- Mencegah koordinator/kaprodi menjadwalkan sidang jika syarat akademik belum terpenuhi.
- Memastikan aturan berlaku untuk mahasiswa individual dan kelompok proyek.
- Menyamakan perilaku backend dan frontend agar kandidat sidang yang muncul memang benar-benar layak dijadwalkan.

## Aturan Bisnis
- Mahasiswa tidak boleh dijadwalkan sidang jika jumlah bimbingan yang sudah `approved` masih kurang dari `8`.
- Mahasiswa tidak boleh dijadwalkan sidang jika laporan sidang belum `approved`.
- Untuk track proyek berkelompok, validasi harus berlaku ke seluruh anggota kelompok, bukan hanya mahasiswa yang dipilih di UI.
- Jika satu anggota kelompok belum memenuhi syarat, maka seluruh kelompok tidak boleh dijadwalkan sidang.
- Untuk mahasiswa individual, validasi cukup berlaku pada mahasiswa tersebut saja.

## Scope Implementasi
- Backend validasi penjadwalan sidang.
- Backend penyediaan data eligibility sidang.
- Frontend filter kandidat sidang.
- Frontend pesan alasan jika mahasiswa/kelompok belum layak sidang.

## Perubahan Backend

### 1. Validasi Keras di Endpoint Jadwal Sidang
- Lokasi utama: `D:\Kerjaan\Kavana\nggk-tau-ini-apa\controllers\sidangController.js`
- Tambahkan pengecekan sebelum insert jadwal sidang:
  - Ambil target mahasiswa.
  - Jika track proyek dan punya `kelompok_id`, ambil seluruh anggota kelompok.
  - Hitung jumlah bimbingan `approved` tiap anggota.
  - Ambil status laporan sidang terbaru/aktif tiap anggota.
  - Tolak request jika ada anggota yang:
    - bimbingan approved `< 8`
    - laporan sidang belum `approved`
- Error response harus jelas, misalnya:
  - `Kelompok belum memenuhi minimal 8 bimbingan approved`
  - `Laporan sidang salah satu anggota kelompok belum approved`

### 2. Standarisasi Helper Eligibility
- Buat helper internal agar logika eligibility sidang tidak duplikat.
- Helper menerima daftar `mahasiswa_id`, lalu mengembalikan:
  - `bimbingan_count`
  - `laporan_status`
  - `sidang_eligible`
  - `reason`
- Helper ini nantinya bisa dipakai ulang oleh:
  - endpoint jadwal sidang
  - endpoint daftar mahasiswa
  - endpoint kandidat sidang

### 3. Perluasan Data Mahasiswa untuk Kebutuhan Sidang
- Lokasi kemungkinan: `D:\Kerjaan\Kavana\nggk-tau-ini-apa\controllers\sharedController.js`
- Tambahkan field turunan untuk kebutuhan UI:
  - `approved_bimbingan_count`
  - `laporan_sidang_status`
  - `sidang_eligible`
  - `sidang_ineligible_reason`
- Untuk proyek berkelompok, field agregat harus dihitung berdasarkan seluruh anggota kelompok:
  - gunakan `min approved bimbingan`
  - gunakan `all laporan approved`

### 4. Pertimbangan Konsistensi Data
- Pastikan pengecekan sidang tetap authoritative di backend walaupun frontend sudah memfilter.
- Jika frontend dibypass, backend tetap wajib menolak data yang tidak memenuhi syarat.

## Perubahan Frontend

### 1. Filter Kandidat Jadwal Sidang
- Lokasi utama: `D:\Kerjaan\Kavana\New-Frontend\src\app\dashboard\koordinator\jadwal-sidang\page.jsx`
- Kandidat yang bisa dipilih harus memenuhi:
  - proposal sudah approved
  - pembimbing sudah ada
  - belum punya jadwal sidang
  - `sidang_eligible = true`
- Untuk kelompok proyek, opsi yang tampil harus mewakili kelompok, bukan anggota terpisah.

### 2. Informasi Alasan Belum Layak
- Jika dipilih pendekatan show-disabled:
  - tampilkan mahasiswa/kelompok yang belum layak dalam keadaan disabled
  - tampilkan alasan, misalnya:
    - `Bimbingan approved baru 6/8`
    - `Laporan sidang belum approved`
- Jika dipilih pendekatan hide-only:
  - hanya tampilkan yang sudah layak
  - alasan tetap tersedia di halaman lain seperti daftar mahasiswa

### 3. Realtime Refresh
- Setelah approval bimbingan atau approval laporan sidang, halaman jadwal sidang harus ikut refresh.
- Cache endpoint kandidat sidang harus ikut di-invalidasi saat:
  - approve/reject bimbingan
  - approve/reject laporan sidang

## Perubahan Data yang Perlu Dicek
- Tabel `bimbingan`
  - pastikan status `approved` bisa dihitung konsisten
- Tabel `laporan_sidang`
  - pastikan status laporan terbaru bisa diambil dengan jelas
- Tabel `mahasiswa`
  - pastikan `track`, `kelompok_id`, dan pembimbing tersedia

## Flow yang Diinginkan

### Kasus Mahasiswa Individual
1. Mahasiswa punya minimal 8 bimbingan approved.
2. Laporan sidang sudah approved.
3. Mahasiswa muncul di kandidat jadwal sidang.
4. Koordinator bisa menjadwalkan sidang.

### Kasus Kelompok Proyek
1. Salah satu anggota submit bimbingan/laporan, tetapi status akademik berlaku ke seluruh kelompok sesuai flow proyek.
2. Sistem mengecek semua anggota kelompok.
3. Jika seluruh anggota memenuhi:
   - minimal 8 bimbingan approved
   - laporan sidang approved
   maka kelompok muncul sebagai kandidat sidang.
4. Jika ada satu anggota gagal syarat, kelompok tidak boleh dijadwalkan.

## Acceptance Criteria
- Koordinator tidak bisa menjadwalkan sidang jika bimbingan approved kurang dari 8.
- Koordinator tidak bisa menjadwalkan sidang jika laporan sidang belum approved.
- Untuk proyek berkelompok, satu anggota yang belum memenuhi syarat memblokir seluruh kelompok.
- Daftar kandidat sidang di frontend hanya menampilkan mahasiswa/kelompok yang layak.
- Backend tetap menolak request sidang yang tidak layak walaupun request dikirim manual.
- Setelah bimbingan/laporan di-approve, data eligibility sidang ikut ter-refresh.

## Urutan Implementasi
1. Tambah helper eligibility sidang di backend.
2. Pasang validasi keras di `scheduleSidang`.
3. Tambah field eligibility di endpoint data mahasiswa/kandidat sidang.
4. Update frontend `jadwal-sidang` agar hanya memakai kandidat yang layak.
5. Tambah invalidasi cache/realtime refresh untuk approval bimbingan dan laporan.
6. Uji kasus individual dan kelompok proyek.

## Risiko
- Jika query agregasi kelompok tidak hati-hati, data proyek bisa terduplikasi.
- Jika status laporan sidang tidak diambil dari record yang tepat, eligibility bisa salah.
- Jika frontend hanya memfilter tanpa validasi backend, aturan masih bisa dibypass.

## Hasil Akhir yang Diharapkan
- Menu `Jadwal Sidang` hanya bisa dipakai untuk mahasiswa/kelompok yang memang sudah siap sidang.
- Tidak ada lagi kasus sidang dijadwalkan sebelum 8 bimbingan selesai dan laporan sidang disetujui.


---

## 7. SIDANG REVISION FLOW PLAN (SIDANG_REVISION_FLOW_PLAN.md)

> **Sumber Berkas Asli:** `SIDANG_REVISION_FLOW_PLAN.md`

# Plan Fitur Revisi Pasca Sidang

## Tujuan
- Menambahkan flow revisi setelah sidang yang terpisah dari flow `laporan_sidang` sebelum sidang.
- Memastikan mahasiswa atau kelompok bisa submit revisi pasca sidang dengan status yang jelas.
- Memastikan dosen/penguji bisa mereview revisi sampai final approved.

## Latar Belakang
- Saat ini sistem hanya punya flow:
  - bimbingan
  - upload laporan sidang
  - approve/reject laporan sidang
  - penjadwalan sidang
- Belum ada flow khusus untuk:
  - hasil sidang membutuhkan revisi
  - mahasiswa submit revisi setelah sidang
  - dosen memeriksa revisi pasca sidang

## Prinsip Utama
- `laporan_sidang` tetap dipakai untuk syarat maju sidang.
- `revisi_sidang` harus menjadi entitas terpisah.
- Hasil sidang harus menentukan apakah mahasiswa:
  - lulus tanpa revisi
  - lulus dengan revisi
  - tidak lulus
- Untuk proyek berkelompok:
  - cukup satu submit revisi untuk kelompok
  - status revisi berlaku ke seluruh anggota kelompok
- Untuk mahasiswa individual:
  - flow revisi berlaku per mahasiswa

## Scope
- Backend status hasil sidang.
- Backend submit revisi sidang.
- Backend approve/reject revisi sidang.
- Frontend mahasiswa untuk upload revisi sidang.
- Frontend dosen/koordinator untuk review revisi sidang.
- Monitoring status revisi.

## Aturan Bisnis

### 1. Hasil Sidang
- Setelah sidang selesai, status hasil sidang harus bisa dipilih:
  - `lulus`
  - `lulus_revisi`
  - `tidak_lulus`

### 2. Trigger Revisi
- Jika hasil sidang = `lulus_revisi`, maka mahasiswa/kelompok masuk ke flow revisi.
- Jika hasil sidang = `lulus`, maka tidak perlu submit revisi.
- Jika hasil sidang = `tidak_lulus`, maka flow lanjutannya bukan revisi, tetapi mengikuti aturan akademik lain.

### 3. Submit Revisi
- Mahasiswa hanya boleh submit revisi jika status hasil sidang = `lulus_revisi`.
- Mahasiswa submit:
  - file revisi
  - catatan revisi / ringkasan perubahan
  - opsional link dokumen final

### 4. Proyek Berkelompok
- Jika track proyek dan punya `kelompok_id`:
  - cukup satu anggota submit revisi
  - revisi disinkronkan ke seluruh anggota kelompok
  - semua anggota melihat status revisi yang sama

### 5. Review Revisi
- Dosen/penguji dapat memberi status:
  - `approved`
  - `rejected`
  - `needs_revision`
- Jika `needs_revision`, mahasiswa dapat submit ulang revisi versi baru.

## Desain Data yang Disarankan

### Opsi yang Disarankan
Buat tabel baru `revisi_sidang`.

Contoh field minimal:
- `id`
- `mahasiswa_id`
- `sidang_id`
- `file_url`
- `catatan_mahasiswa`
- `catatan_dosen`
- `status`
- `submitted_at`
- `reviewed_at`
- `reviewed_by`
- `revision_round`

### Status yang Disarankan
- `pending`
- `approved`
- `rejected`
- `needs_revision`

### Tambahan pada Tabel Sidang
Tambahkan field hasil sidang:
- `hasil_sidang`

Nilai:
- `lulus`
- `lulus_revisi`
- `tidak_lulus`

Opsional:
- `catatan_sidang`
- `completed_at`

## Perubahan Backend

### 1. Update Flow Sidang
- Lokasi utama:
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\controllers\sidangController.js`
- Tambahkan endpoint atau update endpoint untuk menyimpan hasil sidang.
- Setelah sidang selesai, sistem menyimpan:
  - hasil sidang
  - catatan sidang

### 2. Endpoint Submit Revisi Mahasiswa
- Tambahkan endpoint mahasiswa:
  - submit revisi sidang
  - lihat status revisi sidang
- Validasi:
  - hanya boleh jika hasil sidang = `lulus_revisi`
  - untuk proyek kelompok, sinkron ke semua anggota

### 3. Endpoint Review Revisi Dosen
- Tambahkan endpoint dosen/koordinator:
  - list revisi yang menunggu review
  - approve / needs_revision / reject

### 4. Sync Kelompok Proyek
- Jika proyek + kelompok:
  - update seluruh anggota kelompok
  - retrieval juga harus grouped/shared

### 5. Cache & Realtime
- Invalidate cache saat:
  - hasil sidang diubah
  - revisi disubmit
  - revisi direview
- Resource yang terdampak:
  - mahasiswa hasil akhir
  - mahasiswa revisi sidang
  - dosen review revisi
  - koordinator monitoring sidang

## Perubahan Frontend

### 1. Halaman Mahasiswa
- Tambahkan menu baru:
  - `Revisi Sidang`
- Halaman menampilkan:
  - hasil sidang
  - status revisi
  - catatan revisi dari dosen/penguji
  - form upload revisi jika diizinkan

### 2. Halaman Dosen
- Tambahkan halaman:
  - `Approve Revisi Sidang`
- Menampilkan:
  - mahasiswa / kelompok
  - file revisi
  - catatan mahasiswa
  - aksi review

### 3. Halaman Koordinator / Kaprodi
- Monitoring revisi sidang:
  - siapa yang belum submit
  - siapa yang menunggu review
  - siapa yang sudah approved

## Flow yang Diinginkan

### Kasus Lulus Tanpa Revisi
1. Sidang selesai.
2. Hasil sidang = `lulus`.
3. Tidak ada flow revisi dibuka.
4. Mahasiswa langsung ke hasil akhir/finalisasi.

### Kasus Lulus Dengan Revisi
1. Sidang selesai.
2. Hasil sidang = `lulus_revisi`.
3. Sistem membuka menu revisi sidang untuk mahasiswa/kelompok.
4. Mahasiswa submit revisi.
5. Dosen/penguji review revisi.
6. Jika approved, revisi selesai.
7. Jika needs_revision, mahasiswa submit ulang.

### Kasus Proyek Berkelompok
1. Sidang proyek dilakukan untuk kelompok.
2. Hasil sidang = `lulus_revisi`.
3. Salah satu anggota submit revisi.
4. Revisi otomatis tercatat untuk seluruh anggota kelompok.
5. Semua anggota melihat status revisi yang sama.

## Acceptance Criteria
- Sistem dapat menyimpan hasil sidang `lulus`, `lulus_revisi`, atau `tidak_lulus`.
- Jika hasil sidang `lulus_revisi`, mahasiswa bisa submit revisi.
- Jika hasil sidang bukan `lulus_revisi`, menu revisi tidak bisa dipakai.
- Untuk proyek berkelompok, satu submit revisi berlaku ke seluruh kelompok.
- Dosen dapat review revisi dan memberi status final.
- Status revisi tampil konsisten di mahasiswa, dosen, dan koordinator.

## Urutan Implementasi
1. Tambah field hasil sidang pada tabel `sidang`.
2. Buat tabel `revisi_sidang`.
3. Tambah endpoint hasil sidang.
4. Tambah endpoint submit revisi mahasiswa.
5. Tambah endpoint review revisi dosen.
6. Tambah halaman frontend mahasiswa dan dosen.
7. Tambah monitoring koordinator/kaprodi.
8. Tambah invalidasi cache dan realtime refresh.

## Risiko
- Jika revisi dicampur dengan `laporan_sidang`, status sebelum sidang dan sesudah sidang akan rancu.
- Jika proyek kelompok tidak disinkronkan, anggota bisa melihat status revisi berbeda.
- Jika hasil sidang tidak disimpan eksplisit, sistem sulit menentukan kapan flow revisi harus dibuka.

## Hasil Akhir yang Diharapkan
- Setelah sidang, sistem bisa membedakan mahasiswa yang langsung lulus dan mahasiswa yang wajib revisi.
- Mahasiswa/kelompok dapat submit revisi dengan flow yang jelas.
- Dosen/koordinator dapat memonitor dan menyetujui revisi sampai selesai.


---

## 8. REPEAT & PARALLEL ACADEMIC FLOW PLAN (REPEAT_PARALLEL_FLOW_PLAN.md)

> **Sumber Berkas Asli:** `REPEAT_PARALLEL_FLOW_PLAN.md`

# Plan Implementasi Flow Mahasiswa Mengulang dengan Jalur Regular, RPL, dan Paralel

## Ringkasan
- Ubah flow repeat agar mahasiswa `regular` yang mengulang tetap harus mengikuti periode aktif, tetapi boleh mengambil track repeat dan track semester berjalan secara paralel.
- Mahasiswa `rpl` yang mengulang tetap bebas mengambil repeat pada semester berapa pun selama ada periode aktif yang sesuai.
- Mahasiswa `regular` yang mengulang boleh ikut periode `rpl` untuk track repeat yang sama jika periode repeat di `regular` belum ada, selama periode `rpl` tersebut memang dibuka untuk repeat track itu.
- Karena sistem saat ini hanya memiliki satu `track` aktif di tabel `mahasiswa`, implementasi flow paralel membutuhkan pemindahan state aktif track ke tabel enrollment terpisah.

## Masalah pada Implementasi Saat Ini
- Logic repeat sekarang di backend memakai `repeat_required`, `repeat_track`, dan `next_allowed_track`, lalu hanya menghasilkan satu `allowedTracks`.
- Tabel `mahasiswa` masih menyimpan `track`, `kelompok_id`, `judul_proyek`, `status_proposal`, `dosen_id`, dan `dosen_id_2` langsung di level mahasiswa.
- Struktur itu tidak bisa mendukung kondisi satu mahasiswa memiliki dua track aktif sekaligus, misalnya `proyek1` repeat dan `proyek2` regular di semester yang sama.
- Filtering periode saat ini hanya melihat `jalur` mahasiswa dan `jalur_scope` jadwal, belum mengenal konsep “regular ikut periode rpl khusus repeat”.

## Perubahan Desain
### Model Data
- Tambahkan tabel baru `mahasiswa_track_enrollments` sebagai sumber kebenaran untuk semua pengambilan track aktif maupun histori.
- Setiap baris enrollment minimal menyimpan:
  - `id`
  - `mahasiswa_id`
  - `track`
  - `jalur_pengambilan` dengan nilai `regular` atau `rpl`
  - `enrollment_type` dengan nilai `regular`, `repeat`, atau `parallel_repeat`
  - `status` dengan nilai `active`, `completed`, `failed`, `cancelled`
  - `jadwal_id`
  - `kelompok_id`
  - `judul_proyek`
  - `file_proposal`
  - `status_proposal`
  - `usulan_dosen_id`
  - `dosen_id`
  - `dosen_id_2`
  - `created_at`, `updated_at`, `completed_at`
- Tabel `mahasiswa` tetap menyimpan identitas utama seperti `jalur`, `angkatan`, dan profil, tetapi tidak lagi menjadi satu-satunya sumber state track aktif.
- Kolom legacy seperti `track`, `kelompok_id`, `judul_proyek`, `status_proposal`, `dosen_id`, dan `dosen_id_2` perlu diperlakukan sebagai backward compatibility sementara atau dimigrasikan bertahap ke enrollment aktif.

### Aturan Akademik
- Mahasiswa `regular` normal:
  - Tetap mengikuti track berdasarkan semester berjalan.
- Mahasiswa `regular` yang repeat:
  - Track repeat hanya boleh diambil jika ada periode aktif untuk track tersebut.
  - Jika periode repeat `regular` belum ada, mahasiswa boleh ikut periode `rpl` untuk track repeat yang sama.
  - Mahasiswa tetap boleh mengambil track semester berjalan secara paralel, selama periode track semester berjalan aktif.
- Mahasiswa `rpl` normal:
  - Boleh mengambil track sesuai aturan `rpl` yang sudah berlaku.
- Mahasiswa `rpl` yang repeat:
  - Boleh mengambil repeat pada semester mana pun selama ada periode aktif yang sesuai.
- Track repeat dan track reguler semester berjalan harus dicatat sebagai dua enrollment aktif yang berbeda, bukan dua status di satu record mahasiswa.

### Aturan Periode
- Jadwal/periode tetap memakai `jadwal_proyek` yang ada sekarang.
- Untuk repeat, perlu tambahan penanda bahwa suatu periode bisa menerima peserta repeat lintas jalur.
- Tambahkan field baru di `jadwal_proyek`, misalnya `repeat_access_scope` dengan nilai:
  - `none`
  - `same_jalur_only`
  - `cross_jalur_allowed`
- Jika `repeat_access_scope = cross_jalur_allowed`, maka mahasiswa `regular` yang repeat boleh masuk ke periode `rpl` untuk track yang sama.
- Validasi tetap berbasis `track`, `status = active`, dan rentang tanggal aktif.

## Perubahan Backend
### Resolver Eligibility
- Ganti resolver track tunggal saat ini menjadi resolver enrollment eligibility.
- Resolver harus menghasilkan daftar peluang enrollment aktif, bukan satu `allowedTrack`.
- Contoh hasil untuk mahasiswa regular semester 3 yang repeat `proyek1`:
  - `proyek1` sebagai `repeat`
  - `proyek2` sebagai `regular`
- Resolver juga harus menentukan jalur pengambilan untuk repeat:
  - pakai periode `regular` jika tersedia
  - fallback ke periode `rpl` jika tidak tersedia dan jadwal mengizinkan `cross_jalur_allowed`

### Endpoint Pemilihan Track
- Endpoint pemilihan track mahasiswa tidak lagi sekadar update `mahasiswa.track`.
- Endpoint harus membuat enrollment aktif baru pada `mahasiswa_track_enrollments`.
- Validasi wajib mencegah duplicate active enrollment untuk track yang sama.
- Validasi wajib mengizinkan dua enrollment aktif berbeda jika salah satunya repeat dan satunya track semester berjalan yang sah.

### Proposal, Kelompok, dan Pembimbing
- Semua data proposal, kelompok, dan pembimbing harus diarahkan ke enrollment aktif yang dipilih mahasiswa.
- Endpoint upload proposal harus menerima atau menentukan `enrollment_id`.
- Assign pembimbing, validasi proposal, dan data kelompok harus bekerja per enrollment, bukan per mahasiswa global.
- Listing koordinator, kaprodi, dan dosen pembimbing harus menampilkan data berdasarkan enrollment aktif pada track yang relevan.

## Perubahan Frontend
- Halaman mahasiswa pilih track harus menampilkan beberapa kartu eligibility jika mahasiswa punya lebih dari satu kesempatan ambil track.
- Untuk kasus paralel, UI harus membedakan:
  - `Track Semester Berjalan`
  - `Track Ulang`
- Jika repeat regular memakai periode `rpl`, UI harus menampilkan badge yang jelas, misalnya `Repeat via RPL`.
- Halaman proposal, kelompok, dan progress mahasiswa harus berbasis enrollment aktif yang dipilih user, bukan satu track global.
- Dashboard koordinator dan kaprodi harus menampilkan proposal dan assignment pembimbing per enrollment agar tidak bentrok ketika satu mahasiswa punya dua track aktif.

## Tahapan Implementasi
1. Tambah tabel `mahasiswa_track_enrollments` dan migration data dasar.
2. Tambah field periode untuk kontrol repeat lintas jalur.
3. Buat service/backend resolver eligibility baru untuk regular, rpl, repeat, dan paralel.
4. Refactor endpoint set track menjadi create enrollment.
5. Refactor proposal, kelompok, pembimbing, dan monitoring agar mengacu ke `enrollment_id`.
6. Refactor frontend mahasiswa untuk memilih dan mengelola beberapa enrollment aktif.
7. Refactor dashboard koordinator/kaprodi/dosen agar semua operasi bekerja per enrollment.
8. Sediakan fallback compatibility sementara untuk data lama selama migrasi belum selesai penuh.

## Test Plan
- Mahasiswa regular semester 3 tanpa repeat hanya bisa mengambil `proyek2`.
- Mahasiswa regular semester 3 yang repeat `proyek1` bisa mengambil `proyek1` dan `proyek2` secara paralel jika kedua periodenya aktif.
- Mahasiswa regular semester 3 yang repeat `proyek1` tidak bisa mengambil repeat jika tidak ada periode aktif regular maupun rpl yang mengizinkan repeat.
- Mahasiswa regular semester 3 yang repeat `proyek1` bisa ikut periode `rpl` untuk `proyek1` jika periode itu aktif dan `cross_jalur_allowed`.
- Mahasiswa `rpl` yang repeat `proyek2` bisa mengambil `proyek2` pada semester mana pun selama periode aktif ada.
- Mahasiswa tidak bisa membuat dua enrollment aktif untuk track yang sama.
- Proposal untuk `proyek1` repeat tidak boleh menimpa proposal untuk `proyek2` regular milik mahasiswa yang sama.
- Koordinator `proyek1` hanya melihat enrollment `proyek1`, dan koordinator `proyek2` hanya melihat enrollment `proyek2`, walaupun mahasiswanya sama.

## Asumsi
- Paralel hanya berlaku untuk kombinasi `repeat + track semester berjalan`, bukan untuk sembarang dua track bebas.
- Track repeat dan track reguler tetap mengikuti aturan periode aktif.
- Repeat lintas jalur hanya berlaku untuk `regular` yang ikut periode `rpl`, bukan sebaliknya, kecuali nanti ada perubahan kebijakan.
- Implementasi ini adalah refactor besar dan tidak aman jika dipaksakan tetap bergantung pada satu kolom `mahasiswa.track`.


---

## 9. KOORDINATOR MULTI-ASSIGNMENT PLAN (KOORDINATOR_MULTI_ASSIGN_PLAN.md)

> **Sumber Berkas Asli:** `KOORDINATOR_MULTI_ASSIGN_PLAN.md`

# Plan Implementasi Koordinator Bisa Menangani Lebih dari 2 Track/Semester

## Ringkasan
- Hilangkan batas maksimal 2 assignment track/semester untuk koordinator.
- Kaprodi harus bisa assign satu koordinator ke lebih dari dua semester/track sekaligus.
- Model data yang sekarang sudah mendukung multi-assignment melalui banyak baris `dosen_role` dengan `assigned_semester`, jadi perubahan utama ada di validasi backend, UX frontend, dan penyesuaian copy/feedback.

## Kondisi Saat Ini
- Backend saat ini menolak request jika jumlah semester yang dipilih lebih dari 2 pada `assignKoordinatorSemester` di `nggk-tau-ini-apa/controllers/kaprodiController.js`.
- Frontend halaman kelola koordinator di `New-Frontend/src/app/dashboard/kaprodi/kelola-koordinator/page.jsx` memakai konstanta `MAX_ASSIGNMENTS = 2`.
- UI juga menampilkan pesan bahwa koordinator maksimal hanya boleh menangani 2 track/semester.
- Route dan payload saat ini sudah cukup karena assignment dikirim sebagai array semester; tidak perlu ubah kontrak API besar.

## Perubahan Implementasi
### Backend
- Hapus validasi hard limit `requestedSemesters.length > 2` pada endpoint assign koordinator.
- Pertahankan validasi semester hanya boleh dari daftar yang sah: `2, 3, 5, 7, 8`.
- Pertahankan mekanisme dedup semester agar semester yang sama tidak disimpan ganda untuk koordinator yang sama.
- Pastikan logic update/replace assignment tetap konsisten:
  - semester yang dipilih sekarang menjadi assignment aktif koordinator
  - semester yang dicabut dari pilihan akan ikut di-unassign
- Update pesan response agar tidak lagi menyebut limit 2 track.

### Frontend
- Hapus konstanta dan guard `MAX_ASSIGNMENTS = 2`.
- Izinkan user memilih sebanyak mungkin semester yang tersedia pada daftar track.
- Ubah helper text dan toast agar tidak lagi menyebut batas maksimal 2 assignment.
- Pastikan state checkbox/toggle semester tetap sinkron saat koordinator sudah punya banyak assignment.

### Dokumentasi dan API
- Update dokumentasi internal dan Swagger bila ada deskripsi yang masih menyiratkan batas 2 track.
- Tidak perlu ubah shape payload; tetap gunakan:
  - `koordinator_id`
  - `semesters: number[]`

## Test Plan
- Kaprodi bisa assign 1 koordinator ke 1 semester.
- Kaprodi bisa assign 1 koordinator ke 3 semester sekaligus.
- Kaprodi bisa assign 1 koordinator ke semua semester valid sekaligus: `2, 3, 5, 7, 8`.
- Request dengan semester duplikat tetap tersimpan unik.
- Request dengan semester invalid tetap ditolak.
- Update assignment dari banyak semester ke subset semester yang lebih kecil tetap berhasil.
- UI tetap menampilkan seluruh badge assignment koordinator dengan benar setelah reload.

## Asumsi
- Satu koordinator tetap hanya bisa diassign ke semester/track yang ada di daftar semester valid saat ini.
- Tidak ada batas baru pengganti limit 2; artinya seorang koordinator boleh menangani semua track bila Kaprodi memilih demikian.
- Model penyimpanan existing di `dosen_role` sudah cukup, jadi tidak perlu migration database khusus untuk perubahan ini.


---

## 10. BIMBINGAN EXPORT PDF & WORD PLAN (BIMBINGAN_EXPORT_PDF_WORD_PLAN.md)

> **Sumber Berkas Asli:** `BIMBINGAN_EXPORT_PDF_WORD_PLAN.md`

# Plan Implementasi Export Hasil Bimbingan Mahasiswa ke PDF dan Word dengan Template Presisi

## Ringkasan
- Tambahkan fitur agar mahasiswa bisa meng-export hasil bimbingannya ke **PDF** atau **Word (.docx)**.
- User memilih format export saat mengunduh: `PDF` atau `Word`.
- Semua field formulir diisi otomatis dari database, seperti nama mahasiswa, NPM, pembimbing, judul, kegiatan/track, dan daftar bimbingan.
- Template harus dibuat sepresisi mungkin mengikuti dokumen contoh, tanpa perubahan layout, jarak, label, garis, atau struktur tabel.

## Keputusan Teknis
- Gunakan **dua generator dokumen terpisah**, bukan satu generator universal:
  - **PDF**: gunakan `pdf-lib`
  - **Word**: gunakan `docx`
- Jangan gunakan HTML-to-PDF atau HTML-to-DOCX untuk kebutuhan ini, karena pendekatan itu tidak cukup stabil untuk hasil yang harus sangat mirip dengan template resmi.
- Gunakan **template tetap** untuk masing-masing format:
  - satu template visual untuk PDF
  - satu template struktur Word yang dikunci sedetail mungkin
- Hasil PDF dan Word harus mengacu ke template yang sama, tetapi tetap dibuat dengan engine native masing-masing format agar output tidak berubah saat dibuka di environment berbeda.

## Kondisi Data Saat Ini
- Data bimbingan mahasiswa tersedia dari tabel `bimbingan`.
- Data identitas mahasiswa tersedia dari tabel `mahasiswa`.
- Data pembimbing tersedia melalui relasi `mahasiswa.dosen_id` dan `mahasiswa.dosen_id_2`.
- Judul proyek tersedia di `mahasiswa.judul_proyek`.
- Track kegiatan tersedia di `mahasiswa.track`.
- Dengan struktur yang ada sekarang, backend sudah dapat mengambil seluruh data inti yang diperlukan untuk mengisi formulir secara otomatis.

## Perubahan Implementasi
### Backend
- Tambahkan endpoint export formulir bimbingan mahasiswa, misalnya:
  - `GET /api/mahasiswa/bimbingan/export?format=pdf`
  - `GET /api/mahasiswa/bimbingan/export?format=docx`
- Endpoint akan mengambil:
  - data mahasiswa login
  - data pembimbing utama
  - judul proyek
  - track/kegiatan
  - daftar bimbingan mahasiswa
- Endpoint akan memilih generator berdasarkan query `format`.
- Validasi `format` hanya menerima:
  - `pdf`
  - `docx`

### Template PDF
- Sediakan template dasar PDF yang identik dengan contoh.
- Template disimpan sebagai aset tetap di backend.
- Elemen statis seperti logo, judul, label, garis, tabel, dan footer dibuat fixed.
- Data dinamis hanya ditulis ke koordinat yang sudah dipetakan dengan presisi.

### Template Word
- Sediakan template Word `.docx` dengan layout, margin, font, ukuran, tabel, alignment, dan spacing yang mengikuti dokumen contoh seketat mungkin.
- Template Word harus dibangun dengan library `docx` secara eksplisit, bukan hasil konversi dari HTML.
- Gunakan pengaturan page size, margin, tab stop, border table, paragraph spacing, dan font run yang tetap agar tampilan tidak berubah saat file dibuka.
- Bila perlu, sisipkan logo ULBI sebagai asset tetap di dokumen Word.

### Mapping Data ke Template
- `Nama Mahasiswa` diambil dari `mahasiswa.nama`
- `NPM` diambil dari `mahasiswa.npm`
- `Pembimbing` diambil dari `dosen.nama`
- `Judul` diambil dari `mahasiswa.judul_proyek`
- `Kegiatan` diambil dari mapping `track`:
  - `proyek1` -> `Proyek 1`
  - `proyek2` -> `Proyek 2`
  - `proyek3` -> `Proyek 3`
  - `internship1` -> `Internship 1`
  - `internship2` -> `Internship 2`
- Tabel bimbingan diambil dari list `bimbingan`:
  - `Tanggal/Jam Bimbingan` dari `tanggal`
  - `Catatan Kemajuan/Materi` dari `topik` dan/atau `catatan`
  - `Paraf Pembimbing` dibiarkan kosong pada v1 kecuali nanti ada fitur paraf digital

### Aturan Isi Tabel
- Form utama mengikuti template 8 baris.
- Jika jumlah bimbingan kurang dari 8, sisa baris tetap kosong.
- Jika jumlah bimbingan lebih dari 8, implementasi v1 harus memilih salah satu pendekatan yang konsisten:
  - batasi hanya 8 baris pertama, atau
  - buat halaman tambahan dengan template lanjutan
- Untuk implementasi awal, gunakan:
  - **v1: hanya 8 baris pertama**
- Jika data lebih dari 8, backend mengembalikan pesan yang jelas atau hanya mengambil 8 entri pertama sesuai kebijakan yang nanti dipilih saat implementasi.

### Frontend
- Tambahkan tombol export di dashboard mahasiswa pada halaman bimbingan atau halaman hasil bimbingan.
- Saat tombol export diklik, tampilkan pilihan format:
  - `Export PDF`
  - `Export Word`
- Setelah user memilih format, frontend memanggil endpoint export dengan parameter `format`.
- File diunduh langsung sesuai format yang dipilih.
- Jika data inti belum lengkap, frontend menampilkan pesan validasi yang jelas.

## Validasi dan Aturan Bisnis
- Hanya mahasiswa yang login boleh meng-export formulir miliknya sendiri.
- Export hanya boleh dilakukan jika:
  - mahasiswa punya dosen pembimbing
  - mahasiswa punya judul proyek
  - mahasiswa punya minimal 1 data bimbingan
- Jika mahasiswa track proyek dan bimbingannya kelompok, data yang diexport tetap mengikuti data bimbingan milik mahasiswa login sesuai record yang ada di database.
- Kolom paraf pembimbing tidak diisi otomatis pada v1 kecuali sudah ada sumber data tanda tangan/paraf yang valid.

## Perubahan API / Interface
- Endpoint baru:
  - `GET /api/mahasiswa/bimbingan/export?format=pdf|docx`
- Response untuk PDF:
  - `Content-Type: application/pdf`
  - `Content-Disposition: attachment; filename="formulir-bimbingan-{npm}.pdf"`
- Response untuk Word:
  - `Content-Type: application/vnd.openxmlformats-officedocument.wordprocessingml.document`
  - `Content-Disposition: attachment; filename="formulir-bimbingan-{npm}.docx"`

## Library dan Dependensi
- Tambahkan dependency backend:
  - `pdf-lib`
  - `docx`
- Opsional:
  - `fontkit` untuk PDF jika butuh embed font custom secara presisi
- Font yang dipakai harus dikunci dan disertakan agar hasil render konsisten di server dan local.
- Jangan bergantung pada font default OS untuk template resmi.

## Tahapan Implementasi
1. Siapkan spesifikasi layout final berdasarkan template gambar.
2. Buat template generator PDF dengan `pdf-lib`.
3. Buat template generator Word dengan `docx`.
4. Buat service agregasi data mahasiswa + pembimbing + judul + bimbingan.
5. Tambahkan endpoint export dengan parameter format.
6. Tambahkan pilihan export format di frontend mahasiswa.
7. Tambahkan validasi data wajib sebelum export.
8. Uji hasil PDF dan Word terhadap template referensi sampai layout stabil.

## Test Plan
- Mahasiswa dengan data lengkap bisa export ke PDF.
- Mahasiswa dengan data lengkap bisa export ke Word.
- PDF dan Word memuat nama, NPM, pembimbing, judul, kegiatan, dan daftar bimbingan sesuai database.
- Format PDF terunduh dengan nama file dan MIME type yang benar.
- Format Word terunduh dengan nama file dan MIME type yang benar.
- Mahasiswa tanpa pembimbing tidak bisa export.
- Mahasiswa tanpa judul proyek tidak bisa export.
- Mahasiswa tanpa data bimbingan tidak bisa export.
- Hasil PDF tidak bergeser saat dibuka di browser dan PDF reader.
- Hasil Word tidak berubah struktur utamanya saat dibuka di Microsoft Word.

## Asumsi
- User memilih format export saat proses unduh.
- V1 tetap memakai kolom paraf pembimbing kosong.
- Target “sama persis” diterjemahkan sebagai penggunaan template tetap dan styling eksplisit, bukan render bebas.
- Word akan dibuat sedekat mungkin dengan template resmi menggunakan struktur `docx`, tetapi PDF tetap menjadi format yang paling stabil untuk kesamaan visual absolut.


---

## 11. CLOUDFLARE TURNSTILE LOGIN PLAN (CLOUDFLARE_TURNSTILE_LOGIN_PLAN.md)

> **Sumber Berkas Asli:** `CLOUDFLARE_TURNSTILE_LOGIN_PLAN.md`

# Plan Implementasi Cloudflare Turnstile pada Login

## Ringkasan
Tambahkan Cloudflare Turnstile ke flow login agar setiap percobaan login wajib menyertakan token verifikasi manusia sebelum backend memproses kredensial. Implementasi akan memakai widget Turnstile yang terlihat di halaman login, validasi token di backend melalui endpoint verifikasi Cloudflare, dan konfigurasi berbasis environment agar local/staging tetap bisa diatur terpisah dari production.

## Perubahan Implementasi
### Frontend
- Tambahkan widget Cloudflare Turnstile ke halaman login di `New-Frontend/src/app/login/page.jsx`.
- Simpan `turnstileToken` di state form dan blok submit selama token belum tersedia atau token sudah expired.
- Reset widget jika login gagal karena verifikasi Turnstile gagal atau token tidak valid.
- Ubah pemanggilan `authAPI.login(...)` agar ikut mengirim `turnstile_token`.
- Tambahkan env frontend untuk `NEXT_PUBLIC_TURNSTILE_SITE_KEY`.
- Tampilkan pesan error yang jelas untuk kasus token kosong, expired, atau verifikasi gagal.

### Backend
- Tambahkan helper/service verifikasi Turnstile yang memanggil `https://challenges.cloudflare.com/turnstile/v0/siteverify`.
- Validasi token Turnstile di awal flow login pada `nggk-tau-ini-apa/controllers/authController.js` sebelum cek user/password.
- Tolak request login jika `turnstile_token` kosong, verifikasi gagal, atau respons Cloudflare tidak sukses.
- Tambahkan env backend untuk `TURNSTILE_SECRET_KEY`.
- Tambahkan opsi env boolean untuk enable/disable enforcement per environment, dengan default implementasi: aktif penuh pada production login flow.

### Kontrak API / Interface
- Request body login berubah dari:
  - `{ email, password }`
- Menjadi:
  - `{ email, password, turnstile_token }`
- Tambahan env:
  - Frontend: `NEXT_PUBLIC_TURNSTILE_SITE_KEY`
  - Backend: `TURNSTILE_SECRET_KEY`
  - Opsional backend: `TURNSTILE_ENABLED=true|false`

## Validasi dan Perilaku
- Login tidak boleh diproses jika widget belum menghasilkan token.
- Token Turnstile harus diverifikasi server-side; frontend tidak dianggap sumber kebenaran.
- Error Turnstile harus dibedakan dari error kredensial agar debugging mudah, tetapi pesan ke user tetap aman dan ringkas.
- Jika token expired sebelum submit, user harus menyelesaikan challenge lagi.
- Flow login dengan email maupun NPM tetap dipertahankan; Turnstile hanya menjadi lapisan validasi tambahan.

## Test Plan
- Login sukses dengan email + password valid + token Turnstile valid.
- Login sukses dengan NPM + password valid + token Turnstile valid.
- Login ditolak jika `turnstile_token` tidak dikirim.
- Login ditolak jika token invalid/expired.
- Login ditolak jika verifikasi Cloudflare gagal atau secret key salah.
- Widget reset dengan benar setelah kegagalan verifikasi.
- Local/staging tetap bisa dijalankan sesuai env toggle tanpa memblokir developer workflow.
- Swagger atau client non-browser tidak bisa login tanpa token jika endpoint login memakai enforcement yang sama.

## Asumsi
- Scope hanya untuk login, tidak mencakup register, forgot password, OTP, atau endpoint lain.
- Widget yang dipakai adalah mode terlihat, bukan invisible.
- Enforcement default adalah selalu wajib pada login.


---

## 12. EMAIL NOTIFICATION GAP PLAN (EMAIL_NOTIFICATION_GAP_PLAN.md)

> **Sumber Berkas Asli:** `EMAIL_NOTIFICATION_GAP_PLAN.md`

# Plan Implementasi Notifikasi Email Kavana

## Kondisi Saat Ini

### Sudah Ada
- Pengiriman email via `Mailtrap + Nodemailer`.
- Utility mailer sudah tersedia di:
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\utils\mailer.js`
- Email yang benar-benar sudah dipakai:
  - OTP login
  - OTP reset password
  - OTP verifikasi registrasi
- Flow pengiriman OTP dipakai di:
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\controllers\otpController.js`
- Konfigurasi environment email sudah disiapkan di:
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\.env.example`
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\config\env.js`

### Belum Ada
- Belum ada email notifikasi untuk event akademik selain OTP.
- Belum ada service email transaksional umum selain `sendOTPEmail`.
- Belum ada template email per event akademik.
- Belum ada antrian/background job email.
- Belum ada preference user untuk opt-in/opt-out notifikasi email.
- Belum ada audit log khusus status kirim email.

### Yang Sudah Ada Tapi Bukan Email
- Sistem notifikasi aplikasi saat ini masih berupa statistik/badge count di:
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\controllers\notificationController.js`
- Ini hanya untuk in-app indicator, bukan pengiriman email ke user.

## Gap Utama
- Mahasiswa belum menerima email saat proposal di-approve / di-reject.
- Mahasiswa belum menerima email saat dosen pembimbing di-assign.
- Mahasiswa belum menerima email saat bimbingan di-approve / di-reject.
- Mahasiswa belum menerima email saat laporan sidang di-approve / di-reject.
- Mahasiswa belum menerima email saat jadwal sidang dibuat / diubah.
- Dosen belum menerima email saat ada mahasiswa baru untuk dibimbing.
- Dosen belum menerima email saat ada bimbingan/laporan yang perlu direview.
- Admin/Kaprodi/Koordinator belum punya digest/ringkasan email operasional.

## Prioritas Event Email yang Disarankan

### Prioritas 1 - Wajib
- OTP login
- OTP reset password
- OTP registrasi
- Proposal approved / rejected
- Pembimbing assigned
- Jadwal sidang dibuat
- Nilai akhir tersedia

### Prioritas 2 - Penting
- Bimbingan approved / rejected
- Laporan sidang approved / rejected
- Jadwal sidang diubah
- Reminder sidang H-3 dan H-1

### Prioritas 3 - Operasional
- Dosen mendapat mahasiswa bimbingan baru
- Dosen ada pending review bimbingan
- Dosen ada pending review laporan
- Koordinator/Kaprodi digest harian
- Admin alert untuk event penting akun/sistem

## Bentuk Notifikasi yang Disarankan

### Untuk Mahasiswa
- Email transaksional per status penting.
- Subjek singkat dan langsung ke aksi.
- Isi memuat:
  - nama mahasiswa
  - track
  - judul
  - status terbaru
  - dosen/penguji bila relevan
  - CTA ke dashboard

### Untuk Dosen
- Email event-driven untuk tugas yang butuh tindakan.
- Fokus ke:
  - mahasiswa baru
  - bimbingan perlu review
  - laporan perlu review
  - sidang terjadwal

### Untuk Koordinator / Kaprodi / Admin
- Jangan spam per event kecil.
- Lebih cocok:
  - digest harian
  - alert penting tertentu

## Desain Teknis yang Disarankan

### 1. Pisahkan Mailer OTP dan Mailer Transaksional
- `sendOTPEmail` tetap dipertahankan khusus OTP.
- Tambahkan service baru, misalnya:
  - `sendTransactionalEmail`
  - `sendAcademicNotificationEmail`

### 2. Tambahkan Template per Event
- Lokasi yang disarankan:
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\utils\emailTemplates\`
- Minimal template:
  - proposal-status
  - pembimbing-assigned
  - bimbingan-status
  - laporan-status
  - sidang-scheduled
  - nilai-akhir

### 3. Tambahkan Notification Service Terpusat
- Lokasi yang disarankan:
  - `D:\Kerjaan\Kavana\nggk-tau-ini-apa\services\notificationService.js`
- Tugas service:
  - mapping event ke recipient
  - mapping event ke template
  - kirim email
  - fallback logging jika gagal

### 4. Hindari Kirim Email Langsung dari Banyak Controller
- Controller cukup memicu event/fungsi notifikasi.
- Contoh:
  - `proposalApproved`
  - `pembimbingAssigned`
  - `bimbinganApproved`
  - `laporanApproved`
  - `sidangScheduled`

### 5. Tambahkan Audit Log Pengiriman
- Simpan minimal:
  - recipient
  - event_type
  - subject
  - status (`queued`, `sent`, `failed`)
  - error_message
  - created_at

### 6. Pertimbangkan Queue / Async Delivery
- Untuk tahap awal bisa synchronous dulu.
- Untuk production yang lebih aman, email sebaiknya async:
  - queue table DB, atau
  - background worker, atau
  - provider email API async

## Provider yang Cocok

### Tahap Development
- Tetap pakai `Mailtrap`

### Tahap Production
- Disarankan pindah ke provider transactional email:
  - Resend
  - SendGrid
  - Mailgun
  - Postmark

Alasan:
- deliverability lebih baik
- analytics lebih baik
- webhook bounce/spam complaint lebih lengkap

## Urutan Implementasi

### Tahap 1
- Rapikan mailer menjadi mailer OTP + mailer transaksional.
- Buat helper template email umum.

### Tahap 2
- Implementasikan email event prioritas 1:
  - proposal status
  - pembimbing assigned
  - jadwal sidang dibuat

### Tahap 3
- Implementasikan email event prioritas 2:
  - bimbingan status
  - laporan status
  - reminder sidang

### Tahap 4
- Tambahkan audit log email.
- Tambahkan preference notifikasi user.

### Tahap 5
- Tambahkan digest untuk dosen/koordinator/admin.

## Acceptance Criteria
- OTP tetap berjalan seperti sekarang.
- Event akademik prioritas 1 mengirim email ke recipient yang benar.
- Subject dan isi email sesuai konteks event.
- Gagal kirim email tidak membuat transaksi akademik utama rusak.
- Ada pencatatan status kirim email.
- Sistem siap dipindah dari Mailtrap ke provider production tanpa refactor besar.

## Hasil Akhir yang Diharapkan
- Mahasiswa, dosen, dan admin hanya menerima email yang relevan.
- Email fokus pada event penting, bukan spam semua aktivitas.
- Arsitektur notifikasi email rapi, reusable, dan mudah ditambah event baru.


---

## 13. EMAIL REGISTER & VALIDATION PLAN (EMAIL_REGISTER_VALIDATION_PLAN.md)

> **Sumber Berkas Asli:** `EMAIL_REGISTER_VALIDATION_PLAN.md`

# Rencana Validasi Email Register

## Tujuan

Membatasi proses registrasi agar hanya menerima email yang valid, wajar, dan bukan email disposable atau temporary mail.

## Scope

Fokus perubahan ada pada flow registrasi mahasiswa, terutama:

- validasi email di frontend
- validasi email di backend
- flow OTP register
- blacklist domain temporary mail

## Masalah Saat Ini

- Validasi email di frontend masih terlalu longgar dan hanya mengecek format umum.
- Backend register belum memvalidasi email dengan aturan yang lebih ketat.
- Flow register aktif berjalan lewat OTP, sehingga validasi harus dipasang di endpoint OTP, bukan hanya di endpoint register biasa.
- Email dengan pola aneh seperti `bagas++agung.12@...` masih berpotensi lolos.
- Email dari layanan temporary mail belum diblokir.

## Aturan Email Yang Diinginkan

Email register harus memenuhi aturan berikut:

- format email harus valid
- local-part hanya boleh memakai karakter yang wajar:
  - huruf
  - angka
  - titik
  - underscore
  - dash
- tidak boleh memakai karakter simbol berlebihan seperti `+`, `++`, atau pola aneh lain
- tidak boleh mengandung double dot
- tidak boleh diawali atau diakhiri titik atau simbol
- domain harus valid dan memiliki TLD yang wajar
- domain temporary/disposable mail harus ditolak

## Contoh

Email yang diterima:

- `bagas.agung12@gmail.com`
- `bagas_agung12@ulbi.ac.id`
- `bagas-agung@outlook.com`

Email yang ditolak:

- `bagas++agung.12@gmail.com`
- `.bagas@gmail.com`
- `bagas..agung@gmail.com`
- `bagas.@gmail.com`
- `bagas@tempmail.com`

## Titik Implementasi

### Backend

Validator utama harus dipasang di:

- [otpController.js](D:/Kerjaan/Kavana/nggk-tau-ini-apa/controllers/otpController.js)
  - `requestRegisterOTP`
  - `verifyRegisterOTP`
- [authController.js](D:/Kerjaan/Kavana/nggk-tau-ini-apa/controllers/authController.js)
  - `registerMahasiswa`

Backend harus menjadi sumber kebenaran utama agar validasi tidak bisa dibypass dari client.

### Frontend

Frontend perlu diperbarui di:

- [page.jsx](D:/Kerjaan/Kavana/New-Frontend/src/app/register/page.jsx)
- [validators.js](D:/Kerjaan/Kavana/New-Frontend/src/lib/validators.js)

Tujuannya agar user langsung mendapat feedback sebelum submit, tetapi keputusan akhir tetap di backend.

## Strategi Validasi

### 1. Validator Khusus Register

Buat validator email khusus untuk registrasi, terpisah dari validator email umum, agar:

- login tidak terganggu
- profile lama tidak langsung rusak
- aturan register bisa lebih ketat

### 2. Normalisasi Email

Sebelum validasi:

- trim spasi
- lowercase domain
- batasi panjang local-part dan domain
- cegah karakter unicode atau pola yang belum ingin didukung

### 3. Blacklist Temporary Mail

Siapkan daftar domain disposable populer dalam file konfigurasi terpisah, misalnya:

- `mailinator.com`
- `tempmail.com`
- `10minutemail.com`
- `guerrillamail.com`

Daftar ini harus mudah diperbarui tanpa mengubah banyak kode.

## Tahapan Implementasi

1. Buat validator backend khusus email register.
2. Buat blacklist domain temporary mail.
3. Pasang validasi di `requestRegisterOTP`.
4. Pasang validasi ulang di `verifyRegisterOTP`.
5. Pasang fallback validasi di `registerMahasiswa`.
6. Sinkronkan validasi ringan di frontend register.
7. Tambahkan pesan error yang spesifik dan mudah dipahami user.

## Pesan Error Yang Disarankan

- `Format email tidak valid`
- `Email mengandung karakter yang tidak diperbolehkan`
- `Email temporary/disposable tidak diperbolehkan`
- `Domain email tidak valid`

## Verifikasi

Minimal pengujian yang harus dilakukan:

- email normal diterima
- email dengan karakter aneh ditolak
- email dengan plus alias ditolak
- email temp mail ditolak
- email kampus dan provider umum tetap diterima
- flow OTP register tidak bisa bypass validasi

## Catatan

Perubahan ini sebaiknya hanya diterapkan ketat pada proses registrasi akun baru terlebih dahulu. Untuk email user lama, validasi lebih ketat jangan langsung dipaksakan ke semua endpoint agar tidak menimbulkan regresi yang tidak perlu.


---

## 14. EVENT-DRIVEN REALTIME REFRESH PLAN (EVENT_DRIVEN_REALTIME_REFRESH_PLAN.md)

> **Sumber Berkas Asli:** `EVENT_DRIVEN_REALTIME_REFRESH_PLAN.md`

# Plan Realtime Refresh Berbasis Event

## Tujuan
- Mengubah mekanisme refresh dashboard agar hanya berjalan saat ada perubahan data.
- Menghapus ketergantungan pada polling berkala atau refresh berbasis interval waktu.
- Membuat seluruh halaman sinkron saat data berubah, baik dari tab yang sama maupun tab lain.

## Masalah Saat Ini
- Beberapa halaman masih terasa tidak realtime karena bergantung pada cache GET dan remount halaman.
- Ada mekanisme polling berkala yang tetap memicu refresh walaupun tidak ada perubahan data.
- Refresh global berbasis waktu tidak efisien dan bisa mengganggu form atau interaksi user.

## Prinsip Solusi
- Refresh hanya dipicu oleh **event perubahan data**.
- Setiap mutasi sukses harus memancarkan event dengan payload yang jelas.
- Halaman yang mendengar event tersebut hanya me-refresh resource yang relevan.
- Tidak ada lagi polling `setInterval` untuk refresh data dashboard.

## Scope
- Semua halaman di bawah `D:\Kerjaan\Kavana\New-Frontend\src\app\dashboard`
- Jika diperlukan, bisa diperluas ke login/register/non-dashboard yang juga butuh sinkronisasi state
- Endpoint backend tidak perlu diubah menjadi websocket dulu jika event frontend sudah cukup

## Arsitektur yang Diinginkan

### 1. Event Bus Frontend Global
- Gunakan satu pusat event realtime di:
  - `D:\Kerjaan\Kavana\New-Frontend\src\lib\realtime.js`
- Event ini menjadi sumber tunggal untuk:
  - broadcast perubahan data
  - subscribe halaman
  - sinkronisasi antar tab

### 2. Event Harus Spesifik per Resource
- Jangan broadcast hanya `refresh-all`.
- Gunakan nama event yang jelas, misalnya:
  - `profile.updated`
  - `proposal.submitted`
  - `proposal.validated`
  - `bimbingan.created`
  - `bimbingan.approved`
  - `laporan.approved`
  - `sidang.scheduled`
  - `periode.updated`
  - `koordinator.assigned`
- Payload minimal berisi:
  - `type`
  - `resourcePrefixes`
  - `entityId` atau `groupId` jika ada
  - `scope` jika ingin dibatasi per role/halaman

### 3. Invalidasi Cache yang Terarah
- Saat event diterima:
  - invalidasi cache hanya untuk endpoint yang terdampak
  - jangan bersihkan semua cache jika tidak perlu
- Contoh:
  - `proposal.submitted` invalidasi:
    - `/api/mahasiswa/proposal`
    - `/api/koordinator/proposal`
    - `/api/koordinator/assign-pembimbing`
  - `bimbingan.approved` invalidasi:
    - `/api/mahasiswa/bimbingan`
    - `/api/dosen/bimbingan`
    - `/api/koordinator/jadwal-sidang`

### 4. Listener per Halaman atau per Resource
- Halaman tidak perlu refresh total jika hanya sebagian data berubah.
- Setiap page harus subscribe ke event yang relevan saja.
- Saat menerima event:
  - panggil `loadData()` atau `refetch()` lokal
  - jangan remount seluruh dashboard jika cukup refetch data halaman

### 5. Sinkronisasi Antar Tab
- Tetap gunakan `BroadcastChannel` atau `storage event` agar perubahan di satu tab langsung diterima tab lain.
- Event yang dikirim antar tab tetap event spesifik, bukan force reload global.

## Perubahan yang Dibutuhkan

### 1. Rework Utility Realtime
- Lokasi:
  - `D:\Kerjaan\Kavana\New-Frontend\src\lib\realtime.js`
- Tambahkan API yang lebih eksplisit:
  - `emitRealtimeEvent(type, payload)`
  - `subscribeRealtimeEvent(type, handler)`
  - `subscribeManyRealtimeEvents(types, handler)`
- Pisahkan event per jenis resource.

### 2. Integrasi ke Layer API
- Lokasi:
  - `D:\Kerjaan\Kavana\New-Frontend\src\lib\api.js`
- Setelah mutasi sukses (`POST`, `PUT`, `PATCH`, `DELETE`):
  - emit event sesuai aksi
  - sertakan prefix cache yang harus di-invalidasi
- Mapping event harus terpusat agar tidak tercecer di semua halaman.

### 3. Hapus Polling Berkala
- Lokasi kemungkinan:
  - `D:\Kerjaan\Kavana\New-Frontend\src\components\layout\dashboard-layout.jsx`
- Hapus `setInterval` atau mekanisme polling yang sekarang memicu refresh periodik.
- Ganti dengan listener event-driven murni.

### 4. Ubah Strategi Refresh Halaman
- Halaman tidak lagi bergantung pada remount global.
- Setiap halaman utama punya:
  - `loadData`
  - subscription ke event relevan
  - cleanup listener saat unmount

### 5. Buat Peta Event per Modul
- Modul mahasiswa:
  - track
  - proposal
  - kelompok
  - bimbingan
  - laporan sidang
- Modul dosen:
  - approval bimbingan
  - approval laporan
- Modul koordinator/kaprodi:
  - periode
  - proposal
  - assign pembimbing
  - jadwal sidang
  - data mahasiswa
- Modul admin:
  - user management
  - audit log

## Strategi Implementasi

### Tahap 1. Standarisasi Event
- Definisikan daftar event dan cache prefix yang terdampak.
- Simpan di satu tempat agar mudah dipelihara.

### Tahap 2. Refactor API Mutations
- Semua mutation API emit event yang sesuai.
- Hilangkan pattern refresh global yang terlalu kasar.

### Tahap 3. Refactor Dashboard Layout
- Hapus polling interval.
- Pertahankan hanya mekanisme listener event lintas tab.

### Tahap 4. Refactor Page-by-Page
- Pasang listener di setiap page yang memang punya data dinamis.
- Pastikan hanya refetch saat menerima event yang relevan.

### Tahap 5. Pengujian Lintas Role
- Uji perubahan dari:
  - mahasiswa -> terlihat di dosen/koordinator
  - dosen -> terlihat di mahasiswa/kaprodi
  - koordinator -> terlihat di mahasiswa/dosen
  - admin -> terlihat di page admin lain

## Contoh Flow

### Kasus Submit Proposal
1. Mahasiswa submit proposal.
2. Mutation sukses.
3. Frontend emit `proposal.submitted`.
4. Event dibroadcast ke tab lain.
5. Halaman yang subscribe:
   - proposal mahasiswa
   - validasi proposal koordinator
   - assign pembimbing
   melakukan refetch data masing-masing.
6. Tidak ada polling dan tidak ada full reload page.

### Kasus Approve Bimbingan
1. Dosen approve bimbingan.
2. Mutation sukses.
3. Emit `bimbingan.approved`.
4. Halaman mahasiswa bimbingan, daftar approve bimbingan, dan jadwal sidang menerima event.
5. Masing-masing invalidate cache terkait lalu refetch data.

## Acceptance Criteria
- Tidak ada lagi refresh periodik berbasis waktu.
- Tidak ada `setInterval` untuk polling refresh dashboard.
- Setiap mutasi sukses memicu event realtime yang sesuai.
- Halaman lain yang relevan langsung sinkron tanpa manual refresh.
- Halaman yang tidak relevan tidak ikut refetch.
- Cache hanya di-invalidasi untuk resource terkait.
- Sinkronisasi antar tab tetap berjalan.

## Risiko
- Jika event naming tidak konsisten, sebagian halaman bisa gagal update.
- Jika satu aksi mempengaruhi banyak modul tetapi mapping event tidak lengkap, UI bisa stale.
- Jika listener dipasang berlebihan, bisa muncul refetch ganda.
- Jika cleanup listener buruk, bisa terjadi memory leak atau duplicate subscription.

## Rekomendasi Teknis
- Pertahankan model event-driven frontend dulu.
- Jangan langsung lompat ke websocket kalau kebutuhan utamanya hanya sinkronisasi perubahan hasil mutasi aplikasi sendiri.
- Jika nanti butuh perubahan dari backend eksternal atau user lain secara real realtime lintas device, baru pertimbangkan:
  - WebSocket
  - SSE
  - Pub/Sub backend

## Hasil Akhir yang Diharapkan
- Semua page hanya refresh ketika ada perubahan data yang benar-benar relevan.
- Tidak ada lagi refresh berkala yang sia-sia.
- Pengalaman pengguna lebih stabil, cepat, dan tidak mengganggu form/interaksi aktif.


---

## 15. PROSES BISNIS BACKEND (Docs-Kavana/Backend-Kavana/PROSES_BISNIS.md)

> **Sumber Berkas Asli:** `Docs-Kavana/Backend-Kavana/PROSES_BISNIS.md`

# PROSES BISNIS BIMBINGAN ONLINE

Sistem bimbingan online ini dirancang untuk mengelola alur akademik mahasiswa yang sedang menjalani Proyek 1, 2, dan 3 serta Internship 1 dan 2. Proses dimulai dari tahap registrasi, di mana mahasiswa membuat akun dengan mengisi data pribadi seperti nama, email, password, NPM, program studi (D3/D4 Teknik Informatika), angkatan, dan nomor WhatsApp. Setelah registrasi, mahasiswa dapat melakukan login dan mengakses sistem. Berbeda dengan mahasiswa, data dosen tidak dibuat melalui registrasi melainkan ditambahkan secara manual oleh developer atau admin, lengkap dengan role akademik seperti Kepala Prodi, Koordinator Proyek/Internship, Pembimbing, ataupun Dosen Biasa.

Setelah login, mahasiswa wajib memilih jenis kegiatan akademik yang sedang dijalani, apakah Proyek (1, 2, atau 3) atau Internship (1 atau 2). Pada kegiatan Proyek, kelompok dapat terdiri dari satu hingga dua mahasiswa, sementara Internship hanya dapat dijalani oleh satu mahasiswa. Mahasiswa kemudian membuat kelompok (group) sesuai dengan track yang dipilih. Untuk proyek, sistem memperbolehkan satu atau dua anggota, sedangkan untuk internship, sistem memastikan grup hanya berisi satu mahasiswa. Setiap kelompok mendapatkan satu *group ID* yang digunakan dalam seluruh proses berikutnya.

Tahap berikutnya adalah pengunggahan proposal. Mahasiswa perlu mengunggah file proposal, menuliskan judul dan catatan tambahan bila perlu, lalu memilih dosen pembimbing yang ingin diajukan. Pada proyek, hanya diperlukan satu pembimbing, sedangkan internship mengharuskan dua pembimbing yaitu Pembimbing Utama dan Pembimbing 2. Semua usulan pembimbing disimpan dalam sistem sebagai *proposal supervisor requests* dengan status awal **SUBMITTED**.

Proposal yang telah diajukan akan masuk ke proses validasi oleh Koordinator, yang sebelumnya telah ditetapkan oleh Kaprodi sesuai dengan prodi mahasiswa dan jenis track yang dipilih. Koordinator kemudian meninjau proposal tersebut dan menentukan apakah proposal layak atau perlu revisi. Jika proposal ditolak, mahasiswa harus memperbaiki dan mengunggah ulang; jika diterima, proses berlanjut ke tahap persetujuan pembimbing. Status proposal setelah lolos validasi menjadi **APPROVED_BY_KOORDINATOR**.

Setelah proposal disetujui, Koordinator melakukan ACC terhadap dosen pembimbing berdasarkan usulan mahasiswa. Untuk proyek, koordinator memilih satu dosen pembimbing, sedangkan untuk internship koordinator memilih dua pembimbing sesuai perannya masing-masing. Pembimbing yang telah disetujui akan disimpan dalam sistem sebagai *supervisor assignments* dan mahasiswa dapat melihat daftar pembimbing lengkap dengan nama, role, dan nomor WhatsApp. Status mahasiswa pada tahap ini berubah menjadi **SUPERVISOR_ASSIGNED**.

Mahasiswa kemudian memasuki tahap bimbingan, di mana mereka diwajibkan melakukan maksimal delapan kali sesi bimbingan. Pada setiap sesi, mahasiswa mengisi minggu ke-, tanggal, topik, dan catatan bimbingan. Sesi tersebut kemudian ditinjau oleh dosen pembimbing yang dapat menyetujuinya atau menolaknya. Proses bimbingan berlanjut hingga mahasiswa mencapai delapan bimbingan yang disetujui.

Setelah menyelesaikan bimbingan, mahasiswa dapat mengunggah laporan akhir sebagai syarat pengajuan sidang. Pembimbing kemudian memeriksa laporan tersebut dan memberikan persetujuan atau meminta revisi. Jika laporan telah disetujui, mahasiswa dinyatakan **READY_FOR_DEFENSE** dan dapat mengikuti sidang.

Pada tahap penjadwalan sidang, koordinator menetapkan tanggal sidang serta menunjuk penguji utama (dari tabel penguji). Dosen pembimbing mahasiswa secara otomatis menjadi Penguji 1 (pembimbing yang juga menguji), sedangkan penguji yang ditunjuk koordinator menjadi Penguji 2 (penguji utama). Pada hari pelaksanaan, mahasiswa melakukan presentasi dan sesi tanya jawab. Setelah sidang selesai, kedua penguji (Dosen Pembimbing dan Penguji Utama) masing-masing memberikan nilai dan catatan kepada mahasiswa melalui endpoint yang berbeda. Berdasarkan penilaian tersebut, sistem menentukan hasil akhir apakah mahasiswa dinyatakan Lulus, Revisi, atau Tidak Lulus. Jika membutuhkan revisi, mahasiswa diminta mengunggah laporan revisi sebelum dinyatakan lulus sepenuhnya.

Ketika satu periode proyek/internship selesai, koordinator dapat menandai jadwal sebagai **completed**. Aksi ini sekaligus melakukan pembersihan data terkait (nilai sidang, sidang, laporan sidang, bimbingan, serta reset proposal/assignment pada mahasiswa) agar periode berikutnya mulai bersih. Koordinator yang menyelesaikan periode juga dinonaktifkan otomatis sehingga perlu diaktifkan atau ditetapkan ulang untuk periode baru.

Secara keseluruhan, alur sistem ini mencakup langkah-langkah: registrasi dan login mahasiswa, pemilihan track, pembuatan kelompok, pengunggahan proposal, pengajuan pembimbing, validasi koordinator, persetujuan pembimbing, proses bimbingan, pengunggahan laporan akhir, persetujuan pembimbing, penjadwalan sidang, pelaksanaan sidang, penilaian, dan penentuan kelulusan.


---

## 16. SETUP BACKEND & DATABASE (Docs-Kavana/Backend-Kavana/SETUP.md)

> **Sumber Berkas Asli:** `Docs-Kavana/Backend-Kavana/SETUP.md`

# Panduan Setup

## Prasyarat
- Node.js terinstal (versi 14+)
- MySQL server berjalan
- npm terinstal

## Setup Database

1. Buka MySQL client atau workbench
2. Jalankan script SQL di `database.sql`:
   ```
   source database.sql;
   ```
   Atau copy-paste isi database.sql ke MySQL

3. Verifikasi database sudah dibuat:
   ```
   USE Bimbingan_Online;
   SHOW TABLES;
   ```

## Konfigurasi Environment

1. Edit file `.env` dengan kredensial MySQL Anda:
   ```
   DB_HOST=localhost
   DB_USER=root
   DB_PASSWORD=your_password
   DB_NAME=Bimbingan_Online
   DB_PORT=3306
   JWT_SECRET=your_jwt_secret_key_here_change_in_production
   PORT=3000
   NODE_ENV=development
   ```

2. Copy dari template jika diperlukan:
   ```bash
   cp .env.example .env
   ```

## Menjalankan Server

```bash
npm install          # Install dependencies
npm start           # Start production server
# atau
npm run dev         # Start development with nodemon (jika tersedia)
```

Server akan berjalan di `http://localhost:3000`

---

## Dokumentasi API

### 1. Endpoint Autentikasi
Untuk registrasi dan login pengguna.

**POST /api/auth/register** - Daftar pengguna baru
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "role": "mahasiswa"
}
```
Response: User ID dan pesan sukses

**POST /api/auth/login** - Login dan dapatkan token JWT
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```
Response: JWT token dan data user

### 2. Endpoint Proposal (Mahasiswa)
- `POST /api/proposals/upload` - Upload proposal PDF (dengan file)
- `GET /api/proposals/my-proposals` - Dapatkan proposal milik mahasiswa
- `GET /api/proposals/:id/download` - Download proposal

### 3. Endpoint Pembimbing (Advisor)
- `GET /api/advisors` - Dapatkan daftar dosen pembimbing (Mahasiswa)
- `POST /api/advisors/select` - Pilih pembimbing untuk proposal (Mahasiswa)
- `GET /api/advisors/my-proposals` - Dapatkan proposal yang ditugaskan (Dosen)
- `PATCH /api/advisors/approve` - Setujui/tolak proposal (Dosen)

### 4. Endpoint Bimbingan (Guidance)
- `POST /api/guidance/start` - Mulai sesi bimbingan (Dosen)
- `GET /api/guidance/sessions` - Dapatkan sesi bimbingan untuk proposal
- `PATCH /api/guidance/complete` - Selesaikan sesi bimbingan (Dosen)
- `PATCH /api/guidance/approve` - Setujui sesi bimbingan

### 5. Endpoint Laporan (Report)
- `POST /api/reports/upload` - Upload laporan PDF (Mahasiswa)
- `GET /api/reports` - Dapatkan semua laporan (Dosen)
- `GET /api/reports/:id/download` - Download laporan
- `PATCH /api/reports/approve` - Setujui/tolak laporan (Dosen)

### 6. Endpoint Ujian (Exam)
- `POST /api/exams/submit` - Kirim dokumen ujian (Mahasiswa)
- `GET /api/exams/submissions` - Dapatkan submisi ujian (Dosen)
- `PATCH /api/exams/approve` - Setujui submisi ujian (Dosen)
- `POST /api/exams/assess` - Beri nilai ujian (Dosen)
- `GET /api/exams/assessments` - Dapatkan penilaian ujian (Dosen)

---

## Header Request

Semua endpoint (kecuali auth) memerlukan header:
```
Authorization: Bearer {jwt_token}
```

## Format Response

### Success (Status 200/201)
```json
{
  "success": true,
  "message": "Operasi berhasil",
  "data": {}
}
```

### Error (Status 400/401/403/500)
```json
{
  "success": false,
  "message": "Deskripsi error",
  "error": {}
}
```

---

## Troubleshooting

| Masalah | Solusi |
|---------|--------|
| Port sudah digunakan | Ubah PORT di .env atau kill process pada port tersebut |
| Koneksi database failed | Pastikan MySQL berjalan dan kredensial benar di .env |
| JWT_SECRET error | Set JWT_SECRET yang kuat di .env |
| Permission denied pada uploads | Pastikan folder uploads ada dan writable |

## Dokumentasi Lebih Lanjut

- [API_USAGE.md](./API_USAGE.md) - Contoh penggunaan API
- [WORKFLOW.md](./WORKFLOW.md) - Alur kerja sistem
- [Testing](./TESTING_QUICKSTART.md) - Panduan testing


---

## 17. FRONTEND DESIGN SYSTEM & UI/UX ARCHITECTURE (Docs-Kavana/Frontend-Kavana/DESIGN.md)

> **Sumber Berkas Asli:** `Docs-Kavana/Frontend-Kavana/DESIGN.md`

# Kavana Design System & UI/UX Architecture (`DESIGN.md`)

> **Product:** KAVANA — Sistem Bimbingan Online untuk Proyek dan Internship Mahasiswa (D4 Teknik Informatika, ULBI)  
> **PRD Reference:** `PRD_Rombak_Desain_UI_UX_KAVANA_v1.2.md` (Versi 1.2 · 17 Nov 2026 · Pemilik: Renzip)  
> **Codebase Scope:** `Frontend-Kavana/src/app`, `Frontend-Kavana/src/components`, `Frontend-Kavana/src/lib`  
> **Tech Stack:** Next.js 16 (App Router, React 19), Tailwind CSS v4 (`@theme inline`), Radix UI (`radix-ui`), Class Variance Authority (`cva`), Framer Motion, Recharts, Lucide React, Sonner

---

## 1. Visual Direction & Design Philosophy (`DS-1`, `DS-9`, `DS-10`)

### 1.1 Selected Visual Direction: *Institutional Academic Operations (Warm Slate & ULBI Blue)*
KAVANA serves 7 distinct academic roles (**Mahasiswa**, **Dosen Pembimbing**, **Kepala Prodi**, **Koordinator**, **Dosen Penguji**, **Administrator**, **Developer**) working with dense daily academic workflows: proposal review, 8-session logbook tracking, defense (`sidang`) scheduling, and post-defense revision verification.

To eliminate generic "AI slop" (purple/indigo gradients, glassmorphism blur everywhere, uniform 4-card KPI strips with fabricated trend arrows, and `rounded-2xl` nested cards), KAVANA adopts a **single, cohesive institutional operations direction**:

1. **Surface-First Hierarchy, Not Card-Inside-Card (`DS-5`, `DS-10`):**
   - Primary workspace sits on a warm neutral canvas (`--surface-page`).
   - Containers use a flat raised surface (`--surface-raised`) with a crisp `1px` border (`--line`), while table headers, input wells, and code blocks recess into `--surface-sunken`.
   - Shadows are never used for static card elevation; `--shadow-overlay` is reserved strictly for floating overlays (modals, sheets, popovers, dropdowns, and toasts).
2. **One Design System, Seven Task-Shaped Dashboards (`DS-9`, `DB-1..7`):**
   - Every role shares the exact same typography, semantic tokens, status badges, and primitive controls.
   - Visual differentiation comes from **information priority, layout structure, and density**:
     - *Mahasiswa (`DB-1`)*: Guided & spacious (`52px` rows), led by a "Langkah berikutnya" CTA block, a 7-stage horizontal stepper, and an 8-slot segmented logbook progress bar.
     - *Dosen (`DB-2`)*: High-density work inbox (`44px` compact rows), led by an oldest-first review queue (`Antrean Tinjauan Teratas`) with category filters before the supervised student table.
     - *Kaprodi (`DB-3`)*: Executive analytical summary pairing cohort distribution charts (with accessible companion data tables) and raw-data degradation lists for students needing attention.
     - *Koordinator (`DB-4`)*: Operational control center combining an active academic period calendar, incomplete supervisor assignment alerts, and defense schedule conflict detection.
     - *Penguji (`DB-5`)*: Distraction-free focus layout spotlighting today's/upcoming defense sessions with direct entry into the segmented evaluation form.
     - *Admin (`DB-6`)*: Utilitarian data management workspace with direct user/role filtering, search, and master data actions.
     - *Developer (`DB-7`)*: High-density technical observability console using `JetBrains Mono` strictly for latency, hashes, IPs, Redis keys, and JSON payloads.
3. **Authentic Campus Language & Canonical Status Glossary (`§5`, `§8`):**
   - All UI copy uses natural Indonesian academic terminology (`Bimbingan ke-3`, `Sidang Proyek 2`, `Logbook`, `Luaran Proyek`, `Berita Acara`).
   - Status labels follow the strict 5-term glossary without synonyms: **`Menunggu`**, **`Revisi`**, **`ACC`**, **`Ditolak`**, **`Selesai`** (plus operational state badges `Aktif` and `Terjadwal`), always rendered with **Color + Distinct Icon + Text (`A11Y-4`)**.
   - Dates use the standard Indonesian format **`"16 Nov 2026"`** in **WIB (`Asia/Jakarta`)**.

### 1.2 Architectural Rationale (Why This Direction Fits KAVANA)
- **Linear & Stripe Dashboard Principles:** High contrast between structural hairlines (`--line`) and tabular numeric data (`font-mono tabular-nums`) lets lecturers and coordinators scan 50+ student rows without visual fatigue.
- **Notion & GitHub Review Principles:** Collapsible grouped navigation (`264px` expanded / `72px` icon rail) and side-by-side / drawer review panels keep context visible while approving logbooks or revisions.
- **Zero Purple/Indigo Policy:** Replaced all legacy purple/violet hues (`258°`) with warm academic teal-slate (`195°`) and ULBI royal blue (`221°`), passing WCAG AA (`>= 4.5:1` text, `>= 3:1` UI boundaries).

---

## 2. Design Tokens (`DS-2..8`, `ENF-1..3`)

All tokens live in `src/app/globals.css` and are enforced by `scripts/slop-check.mjs` (which rejects raw `#hex`, arbitrary `rounded-[`, `rounded-2xl`, `rounded-3xl`, `backdrop-blur` outside overlays, `bg-gradient-to-`, `font-black`, and `shadow-[0_`).

### 2.1 Color System (`DS-2`)

| Semantic Token | Light Value (`H S% L%`) | Purpose & Usage |
| :--- | :--- | :--- |
| `--brand` / `--accent` | `hsl(221 83% 45%)` | Primary brand dominant action, active navigation indicator, focus ring |
| `--accent-soft` | `hsl(221 83% 45% / 0.08)` | Active nav fill, selected table row / item highlight |
| `--ctp-teal` | `hsl(201 89% 32%)` | Secondary accent (academic track badges, secondary series) |
| `--surface-page` | `hsl(210 40% 98%)` | Flat document `<body>` canvas (no grid or noise overlays) |
| `--surface-raised` | `hsl(0 0% 100%)` | Primary containers: cards, sticky header, sidebar, modals, sheets |
| `--surface-sunken` | `hsl(210 40% 96%)` | Recessed wells: table headers, form inputs, code blocks, filter bars |
| `--line` | `hsl(214 32% 91%)` | All `1px` borders, table row dividers, and section hairlines |
| `--text-strong` | `hsl(222 47% 11%)` | Headings, primary record names, key metric values (`> 15:1` contrast) |
| `--text-body` | `hsl(215 19% 35%)` | Body copy, table cells, descriptions (`> 7:1` contrast) |
| `--text-muted` | `hsl(215 18% 38%)` | Captions, timestamps, helper text, column headers (`>= 4.5:1` contrast) |
| `--status-success` | `hsl(161 90% 24%)` | Semantic status: `ACC`, `Selesai`, `Aktif` |
| `--status-warning` | `hsl(24 94% 32%)` | Semantic status: `Menunggu`, `Revisi` |
| `--status-danger` | `hsl(0 74% 40%)` | Semantic status: `Ditolak`, destructive actions, error alerts |
| `--status-info` | `hsl(221 83% 45%)` | Semantic status: `Terjadwal`, informational callouts |

### 2.2 Typography Scale (`DS-3`)

Self-hosted via `next/font/google` in `src/app/layout.js`:
- **UI & Heading Font (`--font-sans`):** `Public Sans` (`400`, `500`, `600`, `700`) — neutral, high x-height institutional grotesque optimized for dense data tables and Indonesian multi-syllable words.
- **Monospace Font (`--font-mono`):** `JetBrains Mono` (`400`, `500`, `600`) — used strictly for NPM (`9 digits`), NIDN, session numbers (`3/8`), dates/timestamps, latency (`14 ms`), IP addresses, Redis keys, and JSON payloads. All numeric columns enforce `font-variant-numeric: tabular-nums`.

| Token | Size | Tailwind Class | Line Height | Usage |
| :--- | :--- | :--- | :--- | :--- |
| `--font-size-12` | `12px` (`0.75rem`) | `text-xs` | `1.4` | Badges, timestamps, table metadata, helper hints, group headers |
| `--font-size-14` | `14px` (`0.875rem`) | `text-sm` | `1.5` | Default body text, table cells, form inputs, navigation links |
| `--font-size-16` | `16px` (`1rem`) | `text-base` | `1.5` | Card titles, dialog titles, primary record titles |
| `--font-size-20` | `20px` (`1.25rem`) | `text-xl` | `1.25` | `PageHeader` `h1` title, primary section headings |
| `--font-size-28` | `28px` (`1.75rem`) | `text-2xl` / `text-[28px]` | `1.2` | Metric strip primary numbers, verification document header |
| `--font-size-36` | `36px` (`2.25rem`) | `text-4xl` | `1.15` | Public landing hero display heading |

### 2.3 Spacing Scale (`DS-4` — Strict 4/8px System)

| Token | Value | Tailwind Equivalent | Typical Usage |
| :--- | :--- | :--- | :--- |
| `--space-1` | `4px` (`0.25rem`) | `p-1` / `gap-1` | Tight inline badge gaps, icon-to-label micro spacing |
| `--space-2` | `8px` (`0.5rem`) | `p-2` / `gap-2` | Button groups, compact cell padding, nav item spacing |
| `--space-3` | `12px` (`0.75rem`) | `p-3` / `gap-3` | Input horizontal padding (`12px`), table cell padding, compact cards |
| `--space-4` | `16px` (`1rem`) | `p-4` / `gap-4` | Mobile page padding (`16px`), grid gap between cards |
| `--space-6` | `24px` (`1.5rem`) | `p-6` / `gap-6` | Desktop page padding (`24px`), vertical section rhythm (`space-y-6`) |
| `--space-8` | `32px` (`2rem`) | `p-8` / `gap-8` | Major layout pane separation, empty/error state vertical padding |
| `--space-12` | `48px` (`3rem`) | `py-12` | Public landing section padding |
| `--space-16` | `64px` (`4rem`) | `py-16` | Public hero vertical breathing room |

### 2.4 Border Radius & Elevation (`DS-5`)

- **Control Radius (`--radius-control` = `8px` / `rounded-lg`):** Buttons, Inputs, Selects, Tabs triggers, Sidebar links (`rounded-md` `6px` for compact inline `StatusBadge` pills).
- **Container Radius (`--radius-container` = `12px` / `rounded-xl`):** Cards, Tables, Modals (`Dialog`), Drawers (`Sheet`), Dropdowns, Toasts (`Sonner`), Empty/Error states.
- **Elevation Hierarchy:**
  1. **Level 0 (Canvas):** `bg-[var(--surface-page)]`
  2. **Level 1 (Container / Card):** `bg-[var(--surface-raised)] border border-[var(--line)] shadow-none`
  3. **Level -1 (Recessed Well):** `bg-[var(--surface-sunken)] border border-[var(--line)]`
  4. **Level 2 (Floating Overlay Only):** `bg-[var(--surface-raised)] border border-[var(--line)] shadow-overlay` (`0 8px 24px hsl(222 47% 11% / 0.12)`)

### 2.5 Iconography (`DS-6`)

- **Library:** `lucide-react` exclusively across all 7 roles.
- **Stroke & Sizing:** `strokeWidth={1.75}`; `16px` (`h-4 w-4`) inside buttons/headers, `18px` (`h-[18px] w-[18px]`) in sidebar navigation, `14px` (`h-3.5 w-3.5`) inside `StatusBadge`.
- **Anti-Slop Rule:** Zero emoji icons (`🚀`, `👋`, `✅`) anywhere in UI headings, greetings, or navigation.

---

## 3. Core Component Catalog (`DS-7`, `A11Y-1..6`)

| Component | File | Key Specifications & Accessibility |
| :--- | :--- | :--- |
| **1. `Button`** | `src/components/ui/button.jsx` | Default height `40px` (`h-10`), compact `32px` (`h-8` / `size="sm"` `h-9`), mobile touch target `>= 44px` (`[@media(pointer:coarse)]:min-h-11`). Variants: `default`, `secondary`, `outline`, `ghost`, `destructive`, `link`. Radius `8px` (`rounded-lg`), `150ms` transition, no hover lift (`hover:-translate-y` banned). |
| **2. `Input` & `Field`** | `src/components/ui/input.jsx`, `src/components/ui/field.jsx` | Height `40px` (`h-10`), mobile `44px`, radius `8px` (`rounded-lg`). `Field` automatically binds `<label htmlFor={id}>`, `aria-describedby` (hint + error), and `aria-invalid` via React `useId()`. |
| **3. `Select`** | `src/components/ui/select.jsx` | Default height `40px` (`h-10`), compact `32px` (`size="sm"` `h-8`), mobile `44px`. Popover uses `rounded-xl border-[var(--line)] shadow-overlay`. |
| **4. `Table`** | `src/components/ui/table.jsx` | Supports `density="comfortable"` (`52px` rows) and `density="compact"` (`44px` rows), sticky header (`sticky top-0 z-10 bg-[var(--surface-sunken)]`), right-aligned action columns, `stackOnMobile` card-stacking below `640px`, and `.table-scroll-container` horizontal scroll shadow indicator (`DIM-1`). |
| **5. `StatusBadge`** | `src/components/shared/status-badge.jsx` | Enforces **Color + Distinct Icon + Text (`A11Y-4`)** across the §5 glossary: `Menunggu` (`Clock` + amber), `Revisi` (`RotateCcw` + amber), `ACC` (`CheckCircle2` + emerald), `Ditolak` (`XCircle` + red), `Selesai` (`CheckCircle2` + emerald), `Terjadwal` (`Calendar` + blue). |
| **6. `Tabs`** | `src/components/ui/tabs.jsx` | Recessed list `bg-[var(--surface-sunken)] rounded-lg p-1`, active trigger `bg-[var(--surface-raised)] rounded-md`, full keyboard arrow navigation and `44px` coarse-pointer target. |
| **7. `Dialog` & `Sheet`** | `src/components/ui/dialog.jsx`, `src/components/ui/sheet.jsx` | Focus-trapped modal (`Dialog`) and slide-over drawer (`Sheet`), `Escape` to close, and deterministic focus restoration to trigger (`DialogFocusCapture` / `SheetFocusCapture`, `A11Y-6`). |
| **8. `Sonner` (`Toaster`)** | `src/components/ui/sonner.jsx` | `12px` radius (`rounded-xl`), `border-[var(--line)]`, `shadow-overlay`, and `4px` semantic left border (`border-l-4`). |
| **9. `EmptyState`, `ErrorState`, `ForbiddenState`** | `src/components/shared/empty-state.jsx` | Covers `ST-1` states: dashed empty state with contextual CTA, inline `ErrorState` (`role="alert"`) with retry button, and `ForbiddenState` for unauthorized role access. |
| **10. `LoadingState` & `Skeleton`** | `src/components/shared/loading-state.jsx` | Accessible skeleton loader (`role="status" aria-live="polite"`) with shape-matched variants: `dashboard`, `table`, and `form`. |
| **11. `Breadcrumb` & `PageHeader`** | `src/components/layout/dashboard-layout.jsx`, `src/components/layout/page-header.jsx` | Sticky topbar renders `Role / Page` `<nav aria-label="Breadcrumb">`. `PageHeader` renders a flat `20px` (`text-xl font-semibold`) `h1` with optional action slot and zero duplicate eyebrows. |

---

## 4. App Shell & Navigation (`§6.2`, `§6.6`)

- **Desktop Sidebar (`DesktopSidebar`):**
  - Expanded width: **`264px`** (`w-[264px]`); Collapsed icon-rail width: **`72px`** (`w-[72px]`).
  - Viewport height: `h-screen h-[100dvh]` (`§6.6`).
  - Toggle button (`>= 44px` target) + keyboard shortcut **`Ctrl+B` / `Cmd+B`**, with collapsed state persisted in `localStorage` (`kavana-sidebar-collapsed`).
  - Grouped accordion sections (`src/lib/menu-groups.js`) over `MENU_CONFIG`: parent group header highlights when any child route is active (`NAV-4`), and collapsed rail items expose **keyboard-focusable Radix Tooltips (`A11Y-5`)** plus **Popover flyout submenus (`NAV-3`)**.
- **Mobile & Tablet Drawer (`MobileSidebar`):**
  - Off-canvas Radix `Sheet` (`side="left"`), auto-closes on link navigation, focus-trapped (`NAV-5`).
- **Sticky Header (`56px` / `h-14`):**
  - Left: Mobile drawer trigger + `Role / Page` Breadcrumb.
  - Right: Quick Search (`Ctrl/Cmd+K` dialog jumping to role pages, `NAV-8`), Multi-Role Switcher for accounts with multiple roles (`NAV-7`), Date in WIB (`xl`), Notifications dropdown (`44px` target), Theme toggle, and User profile dropdown.
- **Viewport & Layout Dimensions (`§6.6`):**
  - Content max-width: `1280px` (`max-w-[1280px]`); tables and calendars expand full-width within the container.
  - Page padding: `24px` desktop (`lg:px-6 lg:py-6`), `16px` mobile (`px-4 py-4`).
  - Tested across all 6 required viewports: **`1366x768`**, **`1440x900`**, **`1920x1080`**, **`1024x768`**, **`768x1024`**, and **`390x844`** (plus `320px` narrow mobile guard) with zero body horizontal overflow (`DIM-1`).


---

## 18. LAPORAN IMPLEMENTASI PRD FRONTEND (Docs-Kavana/Frontend-Kavana/LAPORAN_IMPLEMENTASI_PRD_v1.2.md)

> **Sumber Berkas Asli:** `Docs-Kavana/Frontend-Kavana/LAPORAN_IMPLEMENTASI_PRD_v1.2.md`

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


---

## 19. INFRASTRUKTUR DOCKER (Docs-Kavana/Infra-Kavana/DOCKER.md)

> **Sumber Berkas Asli:** `Docs-Kavana/Infra-Kavana/DOCKER.md`

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


---

## 20. TESTING & QA SPECIFICATIONS (Docs-Kavana/Test-Kavana/README.md)

> **Sumber Berkas Asli:** `Docs-Kavana/Test-Kavana/README.md`

# Kavana Selenium WebDriver Testing Automation

**Status**: ✅ Configuration Complete - Ready for Testing  
**Version**: 1.0.0  
**Last Updated**: 2026-07-06

---

## 📋 Overview

Testing automation suite lengkap untuk Kavana menggunakan Selenium WebDriver. Konfigurasi telah direfactor mengikuti pola proven dari **Frontend-Kavana Playwright tests**.

### ✨ Highlights

- ✅ **14 Test Cases** - UI, API, RBAC, E2E
- ✅ **Role-Based Testing** - 6 roles support (mahasiswa, dosen, koordinator, kaprodi, admin, developer)
- ✅ **Page Object Model** - Clean, maintainable test structure
- ✅ **Cross-Browser** - Chrome, Firefox, Edge support
- ✅ **Environment Config** - Loads from Frontend-Kavana/.env
- ✅ **Playwright-Compatible** - Helpers adapted from Playwright tests

---

## 📁 Project Structure

```
selenium-tests/
├── config/
│   ├── constants.js         # Centralized constants (routes, endpoints, defaults)
│   ├── test.config.js       # Main config (env loading, credentials)
│   └── driver.config.js     # WebDriver setup (Chrome/Firefox/Edge)
│
├── pages/                   # Page Object Model
│   ├── BasePage.js          # Base page with common methods
│   ├── LoginPage.js         # Login page object
│   └── DashboardPage.js     # Dashboard page object
│
├── utils/                   # Helper utilities (from Playwright)
│   ├── logger.js            # Logging system
│   ├── auth-helpers.js      # Authentication (loginAsRole, logout)
│   ├── ui-helpers.js        # UI checks (error detection, overflow)
│   └── api-helpers.js       # API utilities (apiUrl, loginApi)
│
├── tests/                   # Test suites
│   ├── runner.js            # Main test orchestrator
│   ├── ui/
│   │   └── login.test.js    # 3 UI tests
│   ├── api/
│   │   ├── auth.test.js     # 5 API auth tests
│   │   └── rbac.test.js     # 3 RBAC tests
│   └── e2e/
│       └── full-flow.test.js # 3 E2E tests
│
├── fixtures/
│   └── test-data.js         # Test data & scenarios
│
├── reports/                 # Generated reports
│   ├── screenshots/         # Failure screenshots
│   └── logs/                # Test logs
│
├── .env                     # Local environment (override)
├── .env.example             # Environment template
├── package.json             # Dependencies & scripts
│
└── docs/
    ├── CONFIGURATION.md     # Configuration guide (11KB)
    ├── REFACTORING_SUMMARY.md # Refactoring details (10KB)
    └── README.md            # This file
```

---

## 🚀 Quick Start

### 1. Install Dependencies

```bash
cd D:/Kavana/selenium-tests
npm install
```

### 2. Configure Environment

Edit `.env` file (atau biarkan untuk load dari `Frontend-Kavana/.env`):

```env
# Backend URL (sudah correct)
BACKEND_URL=https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana

# Test Credentials (GANTI dengan credentials sebenarnya)
TEST_DOSEN_EMAIL=dosen.test@example.com
TEST_DOSEN_PASSWORD=password123

TEST_ADMIN_EMAIL=admin.test@example.com
TEST_ADMIN_PASSWORD=password123
```

### 3. Run Tests

```bash
# Run all tests
npm test

# Run specific suite
npm run test:ui       # UI tests only
npm run test:api      # API tests only
npm run test:e2e      # E2E tests only
npm run test:rbac     # RBAC tests only

# Browser-specific
npm run test:chrome   # Chrome (default)
npm run test:firefox  # Firefox
npm run test:headless # Headless mode (CI/CD)
```

---

## 🧪 Test Coverage

### UI Tests (3 tests)
- ✅ Valid Dosen Login
- ✅ Invalid Login Rejection
- ✅ Form Validation

### API Tests (5 tests)
- ✅ Backend Health Check
- ✅ Dosen Login
- ✅ Invalid Login Rejection
- ✅ Unauthorized Access Protection
- ✅ Swagger Docs Availability

### RBAC Tests (3 tests)
- ✅ Dosen Accesses Own Profile
- ✅ Admin Cannot Access Dosen Profile
- ✅ Dosen Bimbingan Access

### E2E Tests (3 tests)
- ✅ Full Login to Dashboard Flow
- ✅ Dashboard Navigation
- ✅ Logout Functionality

**Total: 14 Tests**

---

## 🔧 Configuration

### Environment Variables

Konfigurasi di-load dengan priority:
1. `D:\Kavana\Frontend-Kavana\.env` (primary)
2. `D:\Kavana\selenium-tests\.env` (override)

Key variables:

```env
# URLs
FRONTEND_URL=https://kavana.my.id
BACKEND_URL=https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana

# Browser
BROWSER=chrome                # chrome, firefox, edge
HEADLESS=false                # true for CI/CD
WINDOW_WIDTH=1920
WINDOW_HEIGHT=1080

# Timeouts (ms)
IMPLICIT_WAIT=10000
PAGE_LOAD_TIMEOUT=30000
SCRIPT_TIMEOUT=30000

# Test Settings
RETRY_COUNT=2
PARALLEL_INSTANCES=3
SCREENSHOT_ON_FAILURE=true

# Test Roles
TEST_ROLES=dosen,admin,koordinator,kaprodi
```

### Role-Based Credentials

Sistem mendukung 6 roles:

| Role | Env Variable | Required |
|------|-------------|----------|
| Mahasiswa | `TEST_MAHASISWA_EMAIL` + `PASSWORD` | Optional |
| Dosen | `TEST_DOSEN_EMAIL` + `PASSWORD` | ✅ Required |
| Koordinator | `TEST_KOORDINATOR_EMAIL` + `PASSWORD` | Optional |
| Kaprodi | `TEST_KAPRODI_EMAIL` + `PASSWORD` | Optional |
| Admin | `TEST_ADMIN_EMAIL` + `PASSWORD` | ✅ Required |
| Developer | `TEST_DEVELOPER_EMAIL` + `PASSWORD` | Optional |

---

## 📚 Documentation

| Document | Description | Size |
|----------|-------------|------|
| `README.md` | This file - overview & quick start | 8KB |
| `CONFIGURATION.md` | Complete configuration guide | 11KB |
| `REFACTORING_SUMMARY.md` | Refactoring details & Playwright mapping | 10KB |
| `SETUP.md` | Setup instructions | 2KB |

---

## 🎯 Key Features

### 1. **Playwright-Compatible Helpers**

Adapted dari Frontend-Kavana Playwright tests:

```javascript
// From tests/helpers/auth.js
import { loginAsRole } from './utils/auth-helpers.js';
await loginAsRole(driver, 'dosen');

// From tests/helpers/api.js
import { apiUrl, loginApi } from './utils/api-helpers.js';
const result = await loginApi(axiosInstance, 'dosen');

// From tests/helpers/ui.js
import { checkNoCriticalPageError } from './utils/ui-helpers.js';
await checkNoCriticalPageError(driver);
```

### 2. **Centralized Constants**

```javascript
import { API_ENDPOINTS, ROUTE_MAP, ALL_DOSEN } from './config/constants.js';

// API endpoints
const loginUrl = apiUrl(API_ENDPOINTS.auth.login);

// Role dashboard paths
const dosenDashboard = ROUTE_MAP.dosen;

// Comprehensive dosen list (9 accounts)
const allDosen = ALL_DOSEN;
```

### 3. **Adaptive Selectors**

```javascript
// Multiple locator strategies untuk fleksibilitas
emailInput: By.css('input[name="email"], input[name="nip"], input[placeholder*="Email"]')
```

### 4. **Environment Validation**

```javascript
import { config } from './config/test.config.js';

// Check if credentials are configured
if (!config.hasRoleCredential('dosen')) {
  throw new Error('TEST_DOSEN_EMAIL/PASSWORD not configured');
}

// Check if real (non-placeholder) credentials
if (!config.isRealCredential(email, password)) {
  console.warn('Using placeholder credentials - test will be skipped');
}
```

---

## 🔍 Usage Examples

### Example 1: Login Test

```javascript
import DriverManager from './config/driver.config.js';
import LoginPage from './pages/LoginPage.js';
import { config } from './config/test.config.js';

// Create driver
const driver = await DriverManager.createDriver('chrome');

// Login with role
const loginPage = new LoginPage(driver);
const user = config.testUsers.dosen;
await loginPage.login(user.email, user.password);

// Verify
const currentUrl = await driver.getCurrentUrl();
console.log('Logged in:', !currentUrl.includes('/login'));

await driver.quit();
```

### Example 2: API Test

```javascript
import axios from 'axios';
import { loginApi, apiUrl } from './utils/api-helpers.js';

const axiosInstance = axios.create();

// Login
const result = await loginApi(axiosInstance, 'dosen');

if (result && result.token) {
  // Call protected endpoint
  const response = await axiosInstance.get(
    apiUrl('/api/dosen/profile'),
    { headers: { Authorization: `Bearer ${result.token}` } }
  );
  
  console.log('Profile:', response.data);
}
```

### Example 3: Dashboard Navigation

```javascript
import DashboardPage from './pages/DashboardPage.js';

const dashboard = new DashboardPage(driver);

// Navigate to role-specific dashboard
await dashboard.navigateToDashboard('dosen');

// Verify dashboard loaded
const isVisible = await dashboard.isDashboardVisible();
console.log('Dashboard visible:', isVisible);

// Get user info
const userName = await dashboard.getUserName();
console.log('User:', userName);

// Logout
await dashboard.logout();
```

---

## 🐛 Troubleshooting

### Backend Connection Issues

```bash
# Test backend connectivity
curl https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana/health
```

**Expected response**:
```json
{
  "status": "degraded",
  "uptime": 106.325587919,
  "checks": {
    "database": "ok",
    "redis": "unavailable"
  }
}
```

### Invalid Credentials

```bash
# Check .env
cat .env | grep TEST_DOSEN

# Verify credentials are not placeholders
# Placeholder: dosen.test@example.com ❌
# Real: actual.email@ulbi.ac.id ✅
```

### Browser Issues

```bash
# Check installed browsers
where chromedriver  # Windows
which chromedriver  # Linux/macOS

# Install drivers
npm run install:drivers
```

---

## 📊 Test Execution

### Expected Output

```
========================================
  KAVANA TESTING AUTOMATION SUITE START
========================================

========== RUNNING API TESTS ==========
[INFO] Starting test: Backend Health Check
[SUCCESS] ✓ Backend Health Check (287ms)
[INFO] Starting test: Dosen Login
[SUCCESS] ✓ Dosen Login (156ms)
...

========== RUNNING UI TESTS ==========
[INFO] Starting test: Valid Dosen Login
[SUCCESS] ✓ Valid Dosen Login (5333ms)
...

========== RUNNING E2E TESTS ==========
[INFO] Starting test: Full Login to Dashboard Flow
[SUCCESS] ✓ Full Login to Dashboard Flow (8234ms)
...

========================================
        FINAL TEST SUMMARY REPORT
========================================

Total Tests Run:  14
Passed:           14
Failed:           0
Pass Rate:        100.00%

Total Time:       32.5 seconds
========================================
```

### Execution Time

- API Tests: ~5-10 seconds
- UI Tests: ~10-15 seconds
- E2E Tests: ~15-20 seconds
- **Total Suite: ~30-45 seconds**

---

## 🤝 Contributing

When adding new tests:

1. Follow Page Object Model pattern
2. Use helpers from `utils/` (auth, ui, api)
3. Add test data to `fixtures/test-data.js`
4. Update this README with new test count

---

## 📝 Notes

- **Configuration**: Mengikuti pola Playwright Frontend-Kavana
- **Credentials**: Load dari `Frontend-Kavana/.env` dengan override lokal
- **Helpers**: Adapted dari `tests/helpers/*.js` Playwright
- **No Auto-Run**: Tests tidak dijalankan otomatis - perlu manual trigger

---

## 📞 Support

Dokumentasi lengkap:
- **Configuration Guide**: `CONFIGURATION.md`
- **Refactoring Details**: `REFACTORING_SUMMARY.md`
- **Setup Instructions**: `SETUP.md`

---

**Created**: 2026-07-06  
**Status**: ✅ Production Ready  
**Framework**: Selenium WebDriver 4.27.0  
**Node**: ES Modules


---

