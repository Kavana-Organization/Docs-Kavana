# PRD: Rombak Desain UI/UX KAVANA

Versi 1.2 · 17 Nov 2026 · Pemilik: Renzip · Proyek: KAVANA, Sistem Bimbingan Online untuk Proyek dan Internship Mahasiswa (D4 Teknik Informatika, ULBI)

## Changelog

- **1.2 (17 Nov 2026)**: konsolidasi v1.1. Arah visual (DS-1) diubah jadi satu arah spesifik yang dipilih Renzip di Fase 2. Gate dirapikan (daftar fitur per role jadi output Fase 1, bukan blocker). Status menu di §7 diubah ke `cek`. Rubrik audit antislop didefinisikan. Label status disamakan dengan §5. Feature flag (rollback) dijadikan opsional. Duplikasi dihapus (NAV, SAFE/DD, A11Y digabung).
- **1.1 (16 Nov 2026)**: revisi pasca-audit: degradasi data, enforcement token, aksesibilitas status, data-viz, glosarium status, DoD per fase, risiko dan rollout, baseline regression, ukuran uji 1024x768.
- **1.0 (16 Nov 2026)**: draft awal.

## 1. Ringkasan

KAVANA dipakai 7 role: Mahasiswa, Dosen, Kaprodi, Koordinator, Penguji, Admin, Developer. UI saat ini terlalu generik dan monoton (kesan "AI slop"), sidebar masih flat, dan dimensi tidak konsisten. PRD ini mendefinisikan rombak desain total di level UI/UX tanpa mengubah business logic: design system sendiri, app shell baru dengan sidebar collapse/dropdown, dashboard yang berbeda bentuk untuk tiap role, dan standar dimensi yang tetap.

Eksekusi dilakukan AI agent dengan skill `antislop` dan `ui-ux-pro-max` sebagai skill utama, ditambah skill pendukung lain yang relevan dan tersedia di agent.

## 2. Masalah

1. **Default AI**: font umum, warna indigo/ungu, semua elemen `rounded-xl` + shadow, gradient di mana-mana. Langsung terbaca sebagai hasil AI.
2. **Satu template untuk 7 role**: dashboard semua role berbentuk sama (stat card + tabel), padahal kebutuhan tiap role berbeda.
3. **Semua jadi card**: tidak ada hierarki, semua elemen berbobot sama.
4. **Copy generik**: tidak terasa bahasa dunia kampus (logbook, ACC, revisi, sidang).
5. **Sidebar flat**: menu panjang dan tidak ada pengelompokan, collapse, atau dropdown.
6. **Dimensi ad-hoc**: nilai px, padding, dan tinggi komponen tidak mengikuti skala, sehingga layout tidak konsisten dan rawan overflow.

## 3. Tujuan dan non-tujuan

**Tujuan**

- T1. UI punya identitas sendiri dan lolos audit `antislop`.
- T2. Sidebar dan navigasi rapi, bisa collapse, dikelompokkan, dan berbasis role.
- T3. Dashboard tiap role berbeda bentuk sesuai tugas utamanya.
- T4. Semua dimensi mengikuti skala tetap dan aman di 6 ukuran uji (§6.6).
- T5. Keputusan UX bisa dijelaskan alasannya (acuan `ui-ux-pro-max`).
- T6. Seluruh interaksi dan alur yang sudah ada tetap berfungsi setelah redesign (regression-free).

**Non-tujuan**

- Mengubah business logic, endpoint API, routing, atau skema database.
- Menambah fitur baru di luar kebutuhan desain. Item bertanda **baru** di §6 dan §7 wajib mendapat persetujuan eksplisit sebelum diimplementasikan.
- Membuat aplikasi native.
- Print/export (berita acara, rekap nilai, halaman sidang): out of scope Fase 1-6, dibahas sebagai fase terpisah jika dibutuhkan.
- Dark mode: out of scope Fase 1-6 (future scope).

## 4. Pengguna dan role

Tugas dan karakter layar di bawah ini adalah asumsi awal dan divalidasi dengan fitur asli di kode pada Fase 1.

| Role | Tugas utama | Karakter layar |
| --- | --- | --- |
| Mahasiswa | Ajukan bimbingan, isi logbook, upload dokumen, lihat revisi dan nilai | Dipandu, lega, fokus "langkah berikutnya" |
| Dosen | Review logbook dan dokumen, setujui jadwal, beri nilai | Padat, mirip inbox, cepat dipindai |
| Kaprodi | Pantau progres, setujui pembimbing dan penguji, lihat laporan | Ringkasan analitik |
| Koordinator | Atur periode, penempatan pembimbing, jadwal sidang | Operasional: kalender dan daftar |
| Penguji | Lihat jadwal sidang, isi penilaian, berita acara | Fokus, minim distraksi |
| Admin | Kelola user, role, master data | Utilitarian: tabel dan form |
| Developer | Audit log, error log, system health | Teknis, padat |

## 5. Alur utama dan glosarium status

**Alur utama yang harus mudah di UI**

1. Penempatan pembimbing (Koordinator mengatur, Kaprodi menyetujui).
2. Mahasiswa mengajukan jadwal bimbingan, dosen menyetujui.
3. Bimbingan berlangsung, mahasiswa mengisi logbook, dosen mereview.
4. Mahasiswa upload dokumen, dosen memberi revisi, ulang sampai ACC.
5. Penjadwalan sidang (Koordinator), penguji menilai.
6. Rekap nilai dan pemantauan oleh Kaprodi.

**Glosarium status** (label baku, tanpa sinonim alternatif)

| Status | Label UI | Makna |
| --- | --- | --- |
| Menunggu | `Menunggu` | Belum ditindaklanjuti oleh pemilik aksi |
| Revisi | `Revisi` | Dikembalikan untuk perbaikan |
| ACC | `ACC` | Disetujui dan dikunci |
| Ditolak | `Ditolak` | Tidak disetujui, alur berhenti |
| Selesai | `Selesai` | Alur tuntas (mis. sidang selesai dinilai) |

Status wajib dibedakan sesuai A11Y-4 (§6.7). Format tanggal: "16 Nov 2026", zona WIB.

## 6. Kebutuhan fungsional

### 6.1 Design system

- DS-1. **Satu arah visual yang spesifik**, dipilih Renzip di Fase 2. Agent mengajukan 2-3 arah konkret (masing-masing: palet, font, contoh layar dashboard dan tabel, alasan cocok untuk tool akademik harian dengan data padat), lalu berhenti sampai Renzip memilih satu. Dilarang menggabungkan beberapa gaya jadi satu arah "campuran". Referensi produk hanya untuk mengambil prinsip, bukan menyalin tampilan. Alasan keputusan ditulis di `DESIGN.md`.
- DS-2. Warna: 1 brand dominan, 1 accent, neutral hangat, plus semantic (success, warning, danger, info).
- DS-3. Tipografi: bukan font default. 1 font UI, 1 font heading (opsional), 1 mono untuk NPM, ID, dan timestamp. Skala ukuran tegas (12, 14, 16, 20, 28, 36).
- DS-4. Spacing hanya dari skala 4/8px: 4, 8, 12, 16, 24, 32, 48, 64.
- DS-5. Radius maksimal 2 nilai (kontrol kecil dan container). Elevasi: border dan perbedaan surface dulu, shadow hanya untuk overlay.
- DS-6. Satu set ikon dengan stroke dan ukuran konsisten. Tanpa emoji sebagai ikon.
- DS-7. Komponen inti: button, input, select, table, badge status, tabs, modal/drawer, toast, empty state, skeleton, breadcrumb.
- DS-8. Semua nilai desain berasal dari token, tanpa hardcode di komponen (lihat §6.10).
- DS-9. Komponen dasar dan bahasa visual (warna status, tipografi, kontrol, tabel, feedback) konsisten di seluruh role. Perbedaan dashboard ditentukan oleh prioritas informasi, susunan, dan densitas, bukan tujuh design system terpisah.
- DS-10. Card, border, shadow, dan aksen dekoratif dipakai hanya jika membantu pengelompokan atau hierarki. Tidak dilarang mutlak, tetapi tidak boleh jadi pola default untuk semua konten.

### 6.2 App shell dan sidebar

Kolom **Status**: `redesign` = komponen existing yang dirombak tampilannya tanpa ubah perilaku; `baru` = belum ada, wajib persetujuan eksplisit; `cek` = validasi dulu keberadaannya di kode, jika belum ada diperlakukan sebagai `baru`.

| ID | Spesifikasi | Status |
| --- | --- | --- |
| NAV-1 | Sidebar expanded 264px dan collapsed 72px (icon rail), tombol toggle dan shortcut keyboard, state tersimpan | redesign |
| NAV-2 | Menu dikelompokkan per modul; grup bisa collapse/dropdown (accordion) | redesign |
| NAV-3 | Mode collapsed: tooltip nama menu (juga muncul via keyboard focus) dan flyout untuk submenu | redesign |
| NAV-4 | Active state jelas; grup induk menandai ada anak yang aktif | redesign |
| NAV-5 | Tablet dan mobile: drawer off-canvas, tertutup otomatis setelah navigasi, focus ter-trap dengan benar | redesign |
| NAV-6 | Menu dibuat dari satu file config per role; role-based guard yang ada tetap berjalan | redesign |
| NAV-7 | Role switcher di header untuk user multi-role | cek |
| NAV-8 | Header sticky 56px: breadcrumb, pencarian (Ctrl/Cmd+K), notifikasi, profil. Target pencarian wajib didefinisikan sebelum implementasi | cek |

### 6.3 Dashboard per role (bentuk harus berbeda)

- DB-1. **Mahasiswa**: panduan progres. Stepper/timeline tahapan, "aksi berikutnya" sebagai CTA utama, bimbingan terdekat, logbook belum diisi.
- DB-2. **Dosen**: inbox kerja. Antrian review (logbook, dokumen, permintaan jadwal) dan daftar mahasiswa beserta status, densitas tinggi.
- DB-3. **Kaprodi**: ringkasan analitik. Progres per angkatan/kelompok, mahasiswa berisiko (lama tidak bimbingan), approval tertunda. Metrik agregat hanya jika tersedia (§6.8).
- DB-4. **Koordinator**: kontrol operasional. Kalender periode, penempatan belum lengkap, jadwal sidang bentrok.
- DB-5. **Penguji**: fokus. Jadwal sidang hari ini dan akses langsung ke form penilaian.
- DB-6. **Admin**: pengelolaan data. Tabel user, role, master data, pencarian, filter, form pengelolaan.
- DB-7. **Developer**: observabilitas teknis. System health, error log, audit log, integrasi, status layanan; monospace hanya untuk data teknis yang membutuhkannya. Metrik agregat hanya jika tersedia (§6.8).

Semua dashboard wajib menangani **partial failure**: jika satu widget gagal memuat, widget lain tetap tampil; widget yang gagal menampilkan error inline dengan opsi retry, tidak menjatuhkan seluruh halaman.

### 6.4 Halaman kunci

Pola halaman dipilih sesuai fungsi, bukan semua grid card. Daftar ini adalah pola acuan, **bukan izin membuat halaman atau fitur baru**. Terapkan hanya pada halaman existing yang ditemukan saat audit. Jika alur aktual berbeda, pertahankan alurnya dan pilih pola UI yang paling sesuai. Pola yang butuh komponen baru ditandai `baru` dan butuh persetujuan.

- PG-1. Daftar mahasiswa bimbingan: split view (daftar dan detail).
- PG-2. Alur bimbingan: tabel status atau board.
- PG-3. Logbook: timeline.
- PG-4. Jadwal: kalender.
- PG-5. Dokumen dan revisi: panel review dengan komentar dan riwayat revisi.
- PG-6. Penilaian: form bersegmen, auto-save, validasi inline.

### 6.5 State

- ST-1. Setiap halaman yang masuk scope Fase 5 punya state yang relevan: loading (skeleton sesuai bentuk konten), empty (dengan arahan aksi yang memang tersedia), error, dan forbidden. Jangan tampilkan state atau CTA yang tidak sesuai permission dan kemampuan aktual halaman.

### 6.6 Dimensi dan responsif

| Elemen | Spesifikasi |
| --- | --- |
| Sidebar | 264px (expanded), 72px (collapsed) |
| Header | 56px, sticky |
| Page padding | 24px desktop, 16px mobile |
| Lebar konten | max 1280px; tabel dan kalender boleh full-width |
| Tinggi kontrol | 40px default, 32px compact, area sentuh minimal 44px di mobile |
| Baris tabel | 44px (padat), 52px (nyaman); header sticky; kolom aksi rata kanan |
| Tinggi viewport | `dvh`, dengan fallback `100vh` untuk browser lama |
| Ukuran uji | 1366x768, 1440x900, 1920x1080, 1024x768, 768x1024, 390x844 |

- DIM-1. Tidak ada horizontal scroll di body dan tidak ada overflow di semua ukuran uji. **Pengecualian:** horizontal scroll diizinkan hanya di dalam wrapper tabel, dengan indikator scroll yang terlihat (mis. shadow tepi). Kalender wajib responsif tanpa scroll horizontal di 390x844.
- DIM-2. Teks panjang (nama lengkap, judul proyek) harus truncate atau wrap tanpa merusak layout.

### 6.7 Aksesibilitas

- A11Y-1. Kontras teks minimal 4.5:1 dan komponen UI minimal 3:1 (WCAG AA), diverifikasi dengan alat (axe, Contrast Checker). Focus ring terlihat, navigasi keyboard penuh, label pada semua form.
- A11Y-2. Atribut aria untuk sidebar, dropdown, modal, dan tabs.
- A11Y-3. Hormati `prefers-reduced-motion`. Transisi state 150 sampai 200ms.
- A11Y-4. Status dibedakan oleh **warna + ikon + teks**, bukan warna saja (WCAG 1.4.1).
- A11Y-5. Tooltip sidebar collapsed muncul via keyboard focus, bukan hanya hover.
- A11Y-6. Semua dropdown, modal, dan tabs punya urutan fokus yang wajar dan bisa ditutup dengan `Esc`.

### 6.8 Integritas data dan degradasi

- SAFE-1. UI hanya menampilkan data yang benar-benar tersedia dari API, state aplikasi, atau sumber yang sudah ada. Dilarang mengarang statistik, aktivitas, progres, status, jadwal, atau informasi bisnis.
- SAFE-2. Data contoh hanya untuk mockup, storybook, atau lingkungan pengembangan. Jangan mencampur data dummy dengan data produksi.
- SAFE-3. Kontrak API tidak diubah: nama field, bentuk request/response, endpoint, dan mekanisme error handling.
- SAFE-4. Jangan mengubah autentikasi, otorisasi, permission, role guard, role switching, validasi bisnis, routing, penyimpanan, atau proses upload dan approval. Masalah di area ini dicatat sebagai temuan terpisah dan butuh persetujuan sebelum diubah.
- SAFE-5. Refactor kode hanya jika diperlukan untuk implementasi UI dan tidak mengubah perilaku bisnis.
- DD-1. Dashboard hanya menampilkan metrik yang tersedia langsung dari API/state existing. Metrik turunan (progres per angkatan, mahasiswa berisiko, system health agregat, dan sejenisnya) tidak boleh dikarang.
- DD-2. Jika metrik turunan tidak tersedia, dashboard degradasi ke data mentah yang ada (mis. daftar mahasiswa dan status terakhir), dengan label eksplisit "ringkasan belum tersedia" dan aksi yang memang ada.
- DD-3. Endpoint atau agregasi baru adalah temuan terpisah di Fase 1, butuh persetujuan Renzip, dan bukan bagian redesign.
- DD-4. Setiap widget dashboard di Fase 4 mencantumkan sumber data (endpoint/field) di laporan fase.

### 6.9 Regression testing

- TEST-1. Setelah tiap fase yang menyentuh interaksi, uji alur terkait agar fungsi yang ada tetap berjalan.
- TEST-2. Minimal verifikasi: login/session, perpindahan role (user multi-role), navigasi dan route guard, CRUD yang terdampak, upload dokumen, approval/review, penjadwalan, dan penilaian. Hanya untuk fitur yang memang ada.
- TEST-3. Periksa console dan network untuk memastikan tidak ada error baru. Catat pengujian yang tidak bisa dilakukan beserta alasannya; jangan mengklaim lulus tanpa bukti.
- TEST-4. Baseline di Fase 1, sebelum redesign: screenshot 5 halaman kunci, log console, dan log network untuk alur kritis (login, navigasi, CRUD, upload, approval). Baseline jadi pembanding TEST-3.

### 6.10 Enforcement token

- ENF-1. Tidak ada nilai hardcode warna, spacing, atau radius di komponen. Dicek lewat lint rule (stylelint/tailwind config) **atau** checklist PR wajib.
- ENF-2. Review PR menolak `#hex`, `px` mentah, atau kelas radius di luar token.
- ENF-3. Acceptance #7 (§12) dibuktikan dengan hasil lint/checklist, bukan klaim.

### 6.11 Data-viz

- DV-1. Chart hanya ditampilkan jika datanya tersedia (§6.8). Tipe yang diizinkan: bar, line, donut. Tidak ada pie 3D, gauge dekoratif, atau chart tanpa sumbu/label.
- DV-2. Palet aman buta warna, dengan tabel data pendamping.
- DV-3. Chart punya judul, label sumbu, dan satuan; legend hanya jika ada lebih dari 1 seri.

## 7. Struktur menu per role

Semua menu di bawah ini **asumsi awal** (status `cek`). Agent memvalidasi terhadap fitur asli di Fase 1 dan hasilnya jadi inventaris resmi. Menu yang tidak ditemukan di kode diperlakukan sebagai `baru` dan butuh persetujuan.

| Role | Menu (asumsi) | Status |
| --- | --- | --- |
| Mahasiswa | Dashboard, Proyek/Internship Saya, Jadwal Bimbingan, Logbook, Dokumen dan Revisi, Nilai, Notifikasi | cek |
| Dosen | Dashboard, Mahasiswa Bimbingan, Permintaan Bimbingan, Jadwal, Review Logbook, Penilaian | cek |
| Kaprodi | Dashboard Monitoring, Rekap Bimbingan, Persetujuan Pembimbing/Penguji, Laporan | cek |
| Koordinator | Periode/Batch, Penempatan Pembimbing, Jadwal Sidang, Rekap, Pengumuman | cek |
| Penguji | Jadwal Sidang, Mahasiswa Uji, Form Penilaian, Berita Acara | cek |
| Admin | Manajemen User dan Role, Master Data (prodi, periode, kelas), Pengaturan | cek |
| Developer | Audit Log, System Health, Error Log, Integrasi/API, Feature Flag | cek |

## 8. Aturan anti-slop

**Dilarang**

- Gradient ungu/indigo-biru, gradient pada teks, glow neon.
- Glassmorphism atau blur di mana-mana.
- Grid card seragam: ikon di lingkaran berwarna, judul, dua baris deskripsi.
- Baris 4 stat card identik dengan panah tren di setiap halaman.
- Semua elemen `rounded-2xl` + shadow besar, semua rata tengah.
- Emoji sebagai ikon atau sapaan.
- Copy generik ("Seamlessly manage...", "Powerful dashboard").
- Data dummy generik (John Doe, Lorem ipsum, 1234).
- Palet dan font default tanpa kurasi dan alasan.

**Wajib**

- Satu elemen dominan per layar, sisanya sekunder. **Pengecualian:** layar tabel-dominan (Admin, Developer): hierarki ditentukan oleh kolom prioritas dan aksi.
- Copy bahasa Indonesia khas kampus dan label status baku sesuai §5: `Menunggu`, `ACC`, `Revisi`, `Ditolak`, `Selesai`; kalimat seperti "Bimbingan ke-3" dan "Sidang Proyek 3". Format tanggal "16 Nov 2026", zona WIB.
- Jika butuh data dummy untuk pengembangan atau mockup: data realistis (nama Indonesia, NPM 9 digit yang divalidasi ke data asli di Fase 1, judul proyek masuk akal, status beragam). Data dummy tidak boleh menggantikan atau menyusup ke data produksi.
- Densitas disesuaikan role: dosen padat, mahasiswa lega.
- Satu atau dua detail khas yang disengaja, bukan dekorasi acak.
- Motion hemat dan punya fungsi.

## 9. Penggunaan skill

- **Skill utama (wajib)**: `antislop` untuk audit dan gerbang kualitas visual (Fase 1 dan Fase 6). `ui-ux-pro-max` untuk keputusan UX: hierarki, flow, pola komponen, aksesibilitas.
- **Skill pendukung**: di awal Fase 1, agent mendata semua skill yang tersedia, membaca deskripsinya, lalu memilih yang menunjang pekerjaan (misalnya yang terkait design system, frontend/komponen, aksesibilitas, responsif, testing visual, dokumentasi). Pemetaan skill ke fase ditulis di laporan Fase 1.
- **Konflik**: keputusan visual dan anti-slop ikut `antislop`; keputusan flow dan UX ikut `ui-ux-pro-max`. Skill pendukung tidak boleh melonggarkan aturan dua skill utama.
- **Disiplin**: tidak memakai skill yang tidak relevan. Jika tidak ada skill pendukung yang cocok, agent menyatakannya dengan jelas.
- **Transparansi**: tiap akhir fase, agent melaporkan skill apa yang dipakai, untuk apa, dan hasilnya.
- **Independensi audit Fase 6**: audit `antislop` dijalankan oleh sesi/agent terpisah dari sesi implementasi, atau ditandatangani Renzip. Identitas auditor dicantumkan di laporan Fase 6.
- Jika `antislop` atau `ui-ux-pro-max` tidak tersedia, agent memberi tahu dulu sebelum lanjut, lalu memakai §8 sebagai fallback.

## 10. Referensi (ambil prinsipnya, jangan ditiru mentah)

- Linear: kerapian navigasi, densitas, kecepatan terasa.
- Notion: sidebar yang bisa collapse dan bersarang.
- GitHub: alur review (diff dan komentar) untuk panel review dokumen.
- Stripe Dashboard: tabel dan data padat yang tetap enak dibaca.
- Vercel: restraint, tipografi, whitespace.
- Cal.com: pola penjadwalan.
- Google Classroom: konteks pendidikan, alur tugas dan feedback.

Jika ada screenshot referensi tambahan, agent menganalisis dulu apa yang membuatnya bagus (hierarki, spacing, tipografi) sebelum mengadopsi prinsipnya. Alat bantu uji: axe DevTools, Contrast Checker, DevTools device toolbar.

## 11. Fase kerja, deliverable, dan Definisi Selesai

Setiap fase berhenti dan melapor, lalu menunggu persetujuan Renzip sebelum lanjut. Fase tidak boleh dimulai jika gate terkait di §13 belum terpenuhi.

| Fase | Isi | Deliverable | Skill utama |
| --- | --- | --- | --- |
| 1. Audit (read-only) | Petakan halaman, fitur, role, alur, sumber data, komponen, temuan slop, masalah dimensi dan sidebar, skill tersedia; deteksi stack; rekam baseline regression | Inventaris halaman/fitur aktual per role (menggantikan asumsi §4 dan §7), laporan audit berprioritas (kritis, sedang, minor), risk register, pemetaan skill, baseline regression, daftar metrik yang tidak tersedia | antislop |
| 2. Design system | 2-3 arah visual konkret, Renzip memilih satu, lalu token dan komponen inti | `DESIGN.md` dan token di config | ui-ux-pro-max |
| 3. App shell | Sidebar, header, config menu per role | Shell baru yang berjalan di 7 role | ui-ux-pro-max |
| 4. Dashboard per role | 7 dashboard berbeda bentuk | Dashboard terimplementasi, tiap widget mencantumkan sumber data | ui-ux-pro-max |
| 5. Halaman kunci | Terapkan PG-1 sampai PG-6 hanya pada halaman existing; lengkapi state | Halaman aktual terimplementasi tanpa fitur baru atau perubahan alur bisnis | ui-ux-pro-max |
| 6. QA dan polish | Audit ulang, uji dimensi di 6 ukuran, aksesibilitas, regression testing | Laporan QA, hasil uji fungsional, hasil audit antislop, daftar isu tersisa, screenshot | antislop |

**Definisi Selesai (DoD) per fase**

- **DoD-F1**: inventaris halaman/fitur aktual per role, baseline regression terekam, daftar metrik yang tidak tersedia, risk register, dan daftar skill yang akan dipakai.
- **DoD-F2**: Renzip sudah memilih satu arah visual; `DESIGN.md` berisi token lengkap (warna, tipografi, spacing, radius, elevasi, ikon), alasan tiap keputusan, dan contoh komponen inti.
- **DoD-F3**: shell berjalan di 7 role; sidebar collapse/dropdown/drawer berfungsi; state tersimpan.
- **DoD-F4**: 7 dashboard berbeda bentuk; tiap widget mencantumkan sumber data; partial failure ditangani.
- **DoD-F5**: semua halaman di scope Fase 5 punya state loading/empty/error/forbidden; tidak ada fitur baru.
- **DoD-F6**: semua item "Wajib" §8 lulus dan nol pelanggaran item "Dilarang". Jika skill `antislop` menyediakan skor/rubrik, laporkan skornya; jika tidak, laporkan checklist §8 item per item. Tidak ada horizontal scroll di 6 ukuran uji. Semua regression test lulus atau tercatat dengan alasan. Audit dilakukan sesi/agent terpisah atau ditandatangani Renzip.

## 12. Kriteria penerimaan

1. Tes tutup logo: tanpa logo, UI tetap bisa dibedakan dari template admin generik.
2. Dashboard 7 role berbeda bentuk, bukan hanya beda isi.
3. Sidebar collapse, dropdown, tooltip, dan drawer mobile berfungsi di semua role.
4. Nol pelanggaran "Dilarang" dan semua "Wajib" lulus (§8), dibuktikan hasil audit Fase 6.
5. Nol horizontal scroll di body dan nol overflow di 6 ukuran layar, dengan screenshot. Scroll dalam wrapper tabel diizinkan (DIM-1).
6. Semua halaman di scope Fase 5 punya state loading, empty, error, dan forbidden.
7. Semua nilai spacing, warna, dan radius berasal dari token, dibuktikan hasil lint/checklist (§6.10).
8. Laporan tiap fase mencantumkan skill yang dipakai dan alasannya.
9. Tidak ada perubahan pada kontrak API, autentikasi, otorisasi, permission, routing, database, atau business logic.
10. Alur terdampak lulus regression testing terhadap baseline Fase 1; pengujian yang belum bisa dilakukan dicatat transparan.
11. Tidak ada data bisnis atau statistik fiktif di produksi, termasuk di dashboard yang datanya tidak tersedia (§6.8).
12. Audit Fase 6 dilakukan sesi/agent terpisah atau ditandatangani Renzip, dengan identitas auditor tercantum.

## 13. Batasan, asumsi, dan gate

**Batasan**

- Hanya perubahan UI. Business logic, kontrak API, routing, autentikasi/otorisasi, permission, dan database tidak disentuh.
- Library komponen yang sudah ada dipertahankan. Dependency baru wajib disertai justifikasi tertulis (alternatif existing apa yang ditolak dan alasannya).
- Commit per fase dengan pesan yang jelas.
- Print/export dan dark mode out of scope Fase 1-6.

**Asumsi**

- Aplikasi web responsif dengan target utama laptop 1366x768 dan 1440.
- Daftar tugas role (§4) dan menu (§7) adalah asumsi sampai divalidasi di Fase 1.

**Gate**

Agent melapor dan berhenti jika gate yang memblokir fase terkait belum terpenuhi.

| # | Hal | Status | Memblokir |
| --- | --- | --- | --- |
| 1 | Stack frontend dan library komponen | Agent mendeteksi dari repo di Fase 1; Renzip mengonfirmasi | Fase 2 |
| 2 | Screenshot UI saat ini (3-5 halaman) | Opsional jika agent bisa menjalankan app untuk membuat baseline; jika tidak, **[LAMPIRKAN]** | Fase 1 (hanya jika app tidak bisa dijalankan) |
| 3 | Daftar fitur dan halaman asli per role | Output Fase 1, bukan input | - |
| 4 | Identitas warna ULBI (jika wajib dipakai) | **[ISI]** | Fase 2 |
| 5 | Format NPM (asumsi 9 digit) | **[VALIDASI]** di Fase 1 | Fase 4 (mockup) |
| 6 | Dark mode | Out of scope (future scope) | - |

## 14. Risiko dan rollout

- R-1. **Rollout bertahap per role** (disarankan): mulai dari Admin/Developer (internal), lanjut Dosen/Penguji, terakhir Mahasiswa. Hindari big-bang.
- R-2. **Rollback** (opsional, hanya jika stack mendukung): feature flag per role untuk shell baru, agar shell lama bisa dikembalikan per role saat ada regression kritis. Jika belum ada mekanisme flag, jangan membangunnya sebagai bagian redesign; gunakan branch dan commit per fase sebagai jalur rollback.
- R-3. **Risk register** disusun di Fase 1, minimal mencakup: perbedaan data aktual vs asumsi §4/§7, library komponen yang tidak mendukung pola baru, ketergantungan pada endpoint agregat yang belum ada, dan waktu audit aksesibilitas.
