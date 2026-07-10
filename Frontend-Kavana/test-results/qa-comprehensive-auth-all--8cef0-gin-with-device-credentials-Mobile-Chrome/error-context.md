# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: qa-comprehensive\auth-all-roles.spec.js >> Fase 2 — Developer Authentication >> Developer login with device credentials
- Location: tests\qa-comprehensive\auth-all-roles.spec.js:113:3

# Error details

```
TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
Call log:
  - → POST https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana/api/auth/login
    - user-agent: Mozilla/5.0 (Linux; Android 11; Pixel 5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.7727.15 Mobile Safari/537.36
    - accept: */*
    - accept-encoding: gzip,deflate,br
    - content-type: application/json
    - content-length: 112
    - cookie: kavana_dev_device=eyJkZXZpY2VfaWQiOiJSZW56IiwiZGV2aWNlX3Rva2VuIjoiS2F2YW5hRGV2ZWxvcGVyIn0

```

# Test source

```ts
  1   | // ============================================
  2   | // Fase 2: Authentication API Tests — All Roles
  3   | // ============================================
  4   | const { test, expect } = require('@playwright/test');
  5   | const { apiUrl, isBackendUnavailableStatus, safeJson } = require('../helpers/api');
  6   | const { ALL_DOSEN, TEST_USERS, hasRoleCredential } = require('../helpers/env');
  7   | 
  8   | // ---- Helper: login via API ----
  9   | async function loginViaApi(request, email, password, extras = {}) {
> 10  |   const res = await request.post(apiUrl('/api/auth/login'), {
      |                             ^ TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
  11  |     data: { email, password, ...extras },
  12  |   });
  13  |   const body = await safeJson(res);
  14  |   if (res.status() !== 200) {
  15  |     console.log(`Login failed for ${email} with status ${res.status()}:`, body);
  16  |   }
  17  |   return { response: res, body, token: body?.token || body?.data?.token, status: res.status() };
  18  | }
  19  | 
  20  | // =============================================
  21  | // Dosen Login Tests (9 accounts)
  22  | // =============================================
  23  | test.describe('Fase 2 — Dosen Authentication (9 accounts)', () => {
  24  |   for (const dosen of ALL_DOSEN) {
  25  |     test(`Login: ${dosen.name} (${dosen.email})`, async ({ request }) => {
  26  |       const result = await loginViaApi(request, dosen.email, dosen.password);
  27  |       test.skip(isBackendUnavailableStatus(result.status), `Backend unavailable (${result.status})`);
  28  | 
  29  |       expect(result.status).toBe(200);
  30  |       expect(result.body.token).toBeTruthy();
  31  |       expect(result.body.user_id).toBeTruthy();
  32  |       // Dosen role can be: dosen, koordinator, kaprodi, penguji
  33  |       expect(['dosen', 'koordinator', 'kaprodi', 'penguji']).toContain(result.body.role);
  34  |     });
  35  | 
  36  |     test(`Profile after login: ${dosen.name}`, async ({ request }) => {
  37  |       const login = await loginViaApi(request, dosen.email, dosen.password);
  38  |       test.skip(isBackendUnavailableStatus(login.status), `Backend unavailable`);
  39  |       test.skip(!login.token, `Login failed for ${dosen.email}`);
  40  | 
  41  |       const profileRes = await request.get(apiUrl('/api/auth/profile'), {
  42  |         headers: { Authorization: `Bearer ${login.token}` },
  43  |       });
  44  |       expect(profileRes.status()).toBe(200);
  45  |       const profile = await safeJson(profileRes);
  46  |       expect(profile.email).toBe(dosen.email);
  47  |       expect(profile.nama).toBeTruthy();
  48  |     });
  49  |   }
  50  | });
  51  | 
  52  | // =============================================
  53  | // Admin Login Tests
  54  | // =============================================
  55  | test.describe('Fase 2 — Admin Authentication', () => {
  56  |   const admin = TEST_USERS.admin;
  57  | 
  58  |   test('Admin login succeeds', async ({ request }) => {
  59  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  60  |     const result = await loginViaApi(request, admin.email, admin.password);
  61  |     test.skip(isBackendUnavailableStatus(result.status), `Backend unavailable`);
  62  | 
  63  |     expect(result.status).toBe(200);
  64  |     expect(result.body.token).toBeTruthy();
  65  |     expect(result.body.role).toBe('admin');
  66  |   });
  67  | 
  68  |   test('Admin profile accessible after login', async ({ request }) => {
  69  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  70  |     const login = await loginViaApi(request, admin.email, admin.password);
  71  |     test.skip(!login.token, 'Login failed');
  72  | 
  73  |     const profileRes = await request.get(apiUrl('/api/auth/profile'), {
  74  |       headers: { Authorization: `Bearer ${login.token}` },
  75  |     });
  76  |     expect(profileRes.status()).toBe(200);
  77  |     const profile = await safeJson(profileRes);
  78  |     expect(profile.role).toBe('admin');
  79  |   });
  80  | 
  81  |   test('Admin /api/auth/me accessible', async ({ request }) => {
  82  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  83  |     const login = await loginViaApi(request, admin.email, admin.password);
  84  |     test.skip(!login.token, 'Login failed');
  85  | 
  86  |     const meRes = await request.get(apiUrl('/api/auth/me'), {
  87  |       headers: { Authorization: `Bearer ${login.token}` },
  88  |     });
  89  |     expect([200, 404]).toContain(meRes.status()); // 404 if endpoint not yet implemented
  90  |   });
  91  | });
  92  | 
  93  | // =============================================
  94  | // Developer Login Tests (3-step)
  95  | // =============================================
  96  | test.describe('Fase 2 — Developer Authentication', () => {
  97  |   const dev = TEST_USERS.developer;
  98  | 
  99  |   test('Developer device enrollment', async ({ request }) => {
  100 |     test.skip(!dev?.email || !dev?.device_id, 'Developer credential not configured');
  101 | 
  102 |     const enrollRes = await request.post(apiUrl('/api/auth/developer-device/enroll'), {
  103 |       data: {
  104 |         device_id: dev.device_id,
  105 |         device_token: dev.device_token,
  106 |       },
  107 |     });
  108 |     // Enroll might be 200 (success), 409 (already enrolled), or other
  109 |     const body = await safeJson(enrollRes);
  110 |     expect(enrollRes.status()).toBeLessThan(500);
```