# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: qa-comprehensive\admin-api.spec.js >> Fase 5 — Admin API Endpoints >> GET /api/admin/audit-logs with limit param
- Location: tests\qa-comprehensive\admin-api.spec.js:133:3

# Error details

```
TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
Call log:
  - → POST https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana/api/auth/login
    - user-agent: Mozilla/5.0 (Linux; Android 11; Pixel 5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.7727.15 Mobile Safari/537.36
    - accept: */*
    - accept-encoding: gzip,deflate,br
    - content-type: application/json
    - content-length: 55

```

# Test source

```ts
  1   | // ============================================
  2   | // Fase 5: Admin API Tests
  3   | // ============================================
  4   | const { test, expect } = require('@playwright/test');
  5   | const { apiUrl, isBackendUnavailableStatus, safeJson } = require('../helpers/api');
  6   | const { TEST_USERS, hasRoleCredential } = require('../helpers/env');
  7   | 
  8   | async function getAdminToken(request) {
  9   |   const admin = TEST_USERS.admin;
  10  |   if (!hasRoleCredential('admin')) return { token: null };
> 11  |   const res = await request.post(apiUrl('/api/auth/login'), {
      |                             ^ TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
  12  |     data: { email: admin.email, password: admin.password },
  13  |   });
  14  |   const body = await safeJson(res);
  15  |   return { token: body?.token, status: res.status() };
  16  | }
  17  | 
  18  | test.describe('Fase 5 — Admin API Endpoints', () => {
  19  |   test('GET /api/admin/profile', async ({ request }) => {
  20  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  21  |     const { token } = await getAdminToken(request);
  22  |     test.skip(!token, 'Admin login failed');
  23  | 
  24  |     const res = await request.get(apiUrl('/api/admin/profile'), {
  25  |       headers: { Authorization: `Bearer ${token}` },
  26  |     });
  27  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  28  |     expect(res.status()).toBe(200);
  29  |     const body = await safeJson(res);
  30  |     expect(body.role || body.data?.role).toBeTruthy();
  31  |   });
  32  | 
  33  |   test('GET /api/admin/stats', async ({ request }) => {
  34  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  35  |     const { token } = await getAdminToken(request);
  36  |     test.skip(!token, 'Admin login failed');
  37  | 
  38  |     const res = await request.get(apiUrl('/api/admin/stats'), {
  39  |       headers: { Authorization: `Bearer ${token}` },
  40  |     });
  41  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  42  |     expect(res.status()).toBe(200);
  43  |     const stats = await safeJson(res);
  44  |     expect(stats).toBeTruthy();
  45  |     console.log('  Admin stats:', JSON.stringify(stats).slice(0, 200));
  46  |   });
  47  | 
  48  |   test('GET /api/admin/users', async ({ request }) => {
  49  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  50  |     const { token } = await getAdminToken(request);
  51  |     test.skip(!token, 'Admin login failed');
  52  | 
  53  |     const res = await request.get(apiUrl('/api/admin/users'), {
  54  |       headers: { Authorization: `Bearer ${token}` },
  55  |     });
  56  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  57  |     expect(res.status()).toBe(200);
  58  |     const body = await safeJson(res);
  59  |     expect(body).toBeTruthy();
  60  |   });
  61  | 
  62  |   test('GET /api/admin/dosen', async ({ request }) => {
  63  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  64  |     const { token } = await getAdminToken(request);
  65  |     test.skip(!token, 'Admin login failed');
  66  | 
  67  |     const res = await request.get(apiUrl('/api/admin/dosen'), {
  68  |       headers: { Authorization: `Bearer ${token}` },
  69  |     });
  70  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  71  |     expect(res.status()).toBe(200);
  72  |     const body = await safeJson(res);
  73  |     // Should be array of dosen
  74  |     const dosenList = body.data || body;
  75  |     if (Array.isArray(dosenList)) {
  76  |       console.log(`  Admin sees ${dosenList.length} dosen`);
  77  |       expect(dosenList.length).toBeGreaterThan(0);
  78  |     }
  79  |   });
  80  | 
  81  |   test('GET /api/admin/mahasiswa', async ({ request }) => {
  82  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  83  |     const { token } = await getAdminToken(request);
  84  |     test.skip(!token, 'Admin login failed');
  85  | 
  86  |     const res = await request.get(apiUrl('/api/admin/mahasiswa'), {
  87  |       headers: { Authorization: `Bearer ${token}` },
  88  |     });
  89  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  90  |     expect(res.status()).toBe(200);
  91  |   });
  92  | 
  93  |   test('GET /api/admin/report', async ({ request }) => {
  94  |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  95  |     const { token } = await getAdminToken(request);
  96  |     test.skip(!token, 'Admin login failed');
  97  | 
  98  |     const res = await request.get(apiUrl('/api/admin/report'), {
  99  |       headers: { Authorization: `Bearer ${token}` },
  100 |     });
  101 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  102 |     expect([200, 404]).toContain(res.status()); // 404 if not implemented
  103 |   });
  104 | 
  105 |   test('GET /api/admin/activity', async ({ request }) => {
  106 |     test.skip(!hasRoleCredential('admin'), 'Admin credential not configured');
  107 |     const { token } = await getAdminToken(request);
  108 |     test.skip(!token, 'Admin login failed');
  109 | 
  110 |     const res = await request.get(apiUrl('/api/admin/activity'), {
  111 |       headers: { Authorization: `Bearer ${token}` },
```