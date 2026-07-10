# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: qa-comprehensive\frontend-pages.spec.js >> Fase 7 — Landing & Public Pages >> Login page has developer device section
- Location: tests\qa-comprehensive\frontend-pages.spec.js:46:3

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText(/device developer/i)
Expected: visible
Timeout: 10000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 10000ms
  - waiting for getByText(/device developer/i)

```

# Page snapshot

```yaml
- generic [active] [ref=e1]:
  - generic [ref=e2]:
    - generic [ref=e4]:
      - generic [ref=e5]:
        - link "Kembali ke Beranda" [ref=e6] [cursor=pointer]:
          - /url: /
          - img [ref=e7]
          - text: Kembali ke Beranda
        - generic [ref=e9]:
          - generic [ref=e10]:
            - img [ref=e11]
            - text: Portal Akademik yang Lebih Tertata
          - heading "Masuk ke ruang kerja bimbingan tanpa tampilan yang melelahkan." [level=1] [ref=e14]
          - paragraph [ref=e15]: "Fokus utama Kavanahub adalah membantu pengguna melihat apa yang penting lebih cepat: progres, dokumen, dan tindak lanjut yang masih menunggu."
        - generic [ref=e16]:
          - generic [ref=e17]:
            - img [ref=e19]
            - paragraph [ref=e22]: Riwayat bimbingan tersimpan rapi dalam satu akun.
          - generic [ref=e23]:
            - img [ref=e25]
            - paragraph [ref=e28]: Status proposal, laporan, dan jadwal lebih mudah dipantau.
          - generic [ref=e29]:
            - img [ref=e31]
            - paragraph [ref=e34]: Mahasiswa, dosen, dan pengelola bekerja di alur yang sama.
      - generic [ref=e36]:
        - generic [ref=e37]:
          - img [ref=e39]
          - generic [ref=e42]: Masuk ke Kavana
          - generic [ref=e43]: Gunakan email atau NPM untuk mengakses dashboard Anda.
        - generic [ref=e44]:
          - generic [ref=e45]:
            - generic [ref=e46]:
              - generic [ref=e47]: Email atau NPM
              - textbox "Email atau NPM" [ref=e48]:
                - /placeholder: contoh@email.com atau 1234567890
            - generic [ref=e49]:
              - generic [ref=e50]:
                - generic [ref=e51]: Password
                - link "Lupa password?" [ref=e52] [cursor=pointer]:
                  - /url: /forgot-password
              - generic [ref=e53]:
                - textbox "Password" [ref=e54]:
                  - /placeholder: Masukkan password
                - button "Tampilkan password" [ref=e55]:
                  - img [ref=e56]
            - generic [ref=e59]:
              - generic [ref=e60]:
                - img [ref=e61]
                - text: Verifikasi Keamanan
              - paragraph [ref=e67]: Dilindungi oleh Cloudflare Turnstile
            - button "Masuk" [disabled]:
              - generic:
                - img
                - text: Masuk
          - generic [ref=e70]: akun baru
          - generic [ref=e72]:
            - text: Belum punya akun?
            - link "Daftar sekarang" [ref=e73] [cursor=pointer]:
              - /url: /register
            - text: untuk mulai mengelola proses bimbingan Anda.
    - region "Notifications alt+T"
    - button "Chat via WhatsApp" [ref=e75]:
      - img
  - alert [ref=e76]
```

# Test source

```ts
  1   | // ============================================
  2   | // Fase 7: Frontend UI Tests (Public Pages)
  3   | // ============================================
  4   | const { test, expect } = require('@playwright/test');
  5   | const { expectNoCriticalPageError, expectNoHorizontalOverflow } = require('../helpers/ui');
  6   | 
  7   | test.describe('Fase 7 — Landing & Public Pages', () => {
  8   |   test('Landing page loads successfully', async ({ page }) => {
  9   |     await page.goto('/');
  10  |     await expect(page).toHaveTitle(/.+/); // Has some title
  11  |     await expectNoCriticalPageError(page);
  12  |     // Should have some content
  13  |     await expect(page.locator('body')).toContainText(/kavana|bimbingan|proyek/i);
  14  |   });
  15  | 
  16  |   test('Landing page has navigation to login', async ({ page }) => {
  17  |     await page.goto('/');
  18  |     const loginLink = page.getByRole('link', { name: /masuk|login/i }).first();
  19  |     await expect(loginLink).toBeVisible();
  20  |   });
  21  | 
  22  |   test('Login page renders all required elements', async ({ page }) => {
  23  |     await page.goto('/login');
  24  |     await expectNoCriticalPageError(page);
  25  | 
  26  |     // Email/NPM field
  27  |     await expect(page.getByLabel(/email atau npm/i)).toBeVisible();
  28  |     // Password field
  29  |     await expect(page.getByLabel(/^password$/i)).toBeVisible();
  30  |     // Submit button
  31  |     await expect(page.getByRole('button', { name: /masuk|login/i })).toBeVisible();
  32  |     // Back to home link
  33  |     await expect(page.getByText(/kembali|beranda/i).first()).toBeVisible();
  34  |   });
  35  | 
  36  |   test('Login page has "Lupa password?" link', async ({ page }) => {
  37  |     await page.goto('/login');
  38  |     await expect(page.getByText(/lupa password/i)).toBeVisible();
  39  |   });
  40  | 
  41  |   test('Login page has "Daftar sekarang" link', async ({ page }) => {
  42  |     await page.goto('/login');
  43  |     await expect(page.getByText(/daftar sekarang/i)).toBeVisible();
  44  |   });
  45  | 
  46  |   test('Login page has developer device section', async ({ page }) => {
  47  |     await page.goto('/login');
> 48  |     await expect(page.getByText(/device developer/i)).toBeVisible();
      |                                                       ^ Error: expect(locator).toBeVisible() failed
  49  |   });
  50  | 
  51  |   test('Login page has Turnstile security section', async ({ page }) => {
  52  |     await page.goto('/login');
  53  |     await expect(page.getByText(/verifikasi keamanan/i)).toBeVisible();
  54  |   });
  55  | 
  56  |   test('Register page renders all fields', async ({ page }) => {
  57  |     await page.goto('/register');
  58  |     await expectNoCriticalPageError(page);
  59  | 
  60  |     await expect(page.getByLabel(/nama lengkap/i)).toBeVisible();
  61  |     await expect(page.getByLabel(/^npm$/i)).toBeVisible();
  62  |     await expect(page.getByLabel(/angkatan/i)).toBeVisible();
  63  |     await expect(page.getByLabel(/^email$/i)).toBeVisible();
  64  |     await expect(page.getByLabel(/^password$/i)).toBeVisible();
  65  |     await expect(page.getByLabel(/konfirmasi password/i)).toBeVisible();
  66  |   });
  67  | 
  68  |   test('Forgot password page loads', async ({ page }) => {
  69  |     await page.goto('/forgot-password');
  70  |     await expectNoCriticalPageError(page);
  71  |     await expect(page.locator('body')).toContainText(/email|reset|lupa/i);
  72  |   });
  73  | 
  74  |   test('FAQ page loads', async ({ page }) => {
  75  |     await page.goto('/faq-sistem');
  76  |     await expectNoCriticalPageError(page);
  77  |   });
  78  | 
  79  |   test('Kebijakan Privasi page loads', async ({ page }) => {
  80  |     await page.goto('/kebijakan-privasi');
  81  |     await expectNoCriticalPageError(page);
  82  |   });
  83  | 
  84  |   test('Syarat Layanan page loads', async ({ page }) => {
  85  |     await page.goto('/syarat-layanan');
  86  |     await expectNoCriticalPageError(page);
  87  |   });
  88  | 
  89  |   test('Panduan Pengguna page loads', async ({ page }) => {
  90  |     await page.goto('/panduan-pengguna');
  91  |     await expectNoCriticalPageError(page);
  92  |   });
  93  | 
  94  |   test('Luaran Proyek 1 page loads', async ({ page }) => {
  95  |     await page.goto('/luaran-proyek-1');
  96  |     await expectNoCriticalPageError(page);
  97  |   });
  98  | 
  99  |   test('Luaran Proyek 2 page loads', async ({ page }) => {
  100 |     await page.goto('/luaran-proyek-2');
  101 |     await expectNoCriticalPageError(page);
  102 |   });
  103 | 
  104 |   test('Luaran Proyek 3 page loads', async ({ page }) => {
  105 |     await page.goto('/luaran-proyek-3');
  106 |     await expectNoCriticalPageError(page);
  107 |   });
  108 | });
  109 | 
  110 | test.describe('Fase 7 — Protected Routes Redirect', () => {
  111 |   test('Dashboard redirects to login when not authenticated', async ({ page }) => {
  112 |     await page.goto('/dashboard/mahasiswa');
  113 |     // Should redirect to login or show auth required
  114 |     await expect(page).toHaveURL(/login|\/$/);
  115 |   });
  116 | 
  117 |   test('Dosen dashboard redirects to login when not authenticated', async ({ page }) => {
  118 |     await page.goto('/dashboard/dosen');
  119 |     await expect(page).toHaveURL(/login|\/$/);
  120 |   });
  121 | 
  122 |   test('Admin dashboard redirects to login when not authenticated', async ({ page }) => {
  123 |     await page.goto('/dashboard/admin');
  124 |     await expect(page).toHaveURL(/login|\/$/);
  125 |   });
  126 | 
  127 |   test('Koordinator dashboard redirects to login when not authenticated', async ({ page }) => {
  128 |     await page.goto('/dashboard/koordinator');
  129 |     await expect(page).toHaveURL(/login|\/$/);
  130 |   });
  131 | 
  132 |   test('Kaprodi dashboard redirects to login when not authenticated', async ({ page }) => {
  133 |     await page.goto('/dashboard/kaprodi');
  134 |     await expect(page).toHaveURL(/login|\/$/);
  135 |   });
  136 | 
  137 |   test('Developer dashboard redirects to login when not authenticated', async ({ page }) => {
  138 |     await page.goto('/dashboard/developer');
  139 |     await expect(page).toHaveURL(/login|\/$/);
  140 |   });
  141 | 
  142 |   test('Profile page redirects to login when not authenticated', async ({ page }) => {
  143 |     await page.goto('/dashboard/profile');
  144 |     await expect(page).toHaveURL(/login|\/$/);
  145 |   });
  146 | });
  147 | 
  148 | test.describe('Fase 7 — Responsive Design Smoke Tests', () => {
```