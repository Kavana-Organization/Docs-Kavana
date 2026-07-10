# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: qa-comprehensive\developer-api.spec.js >> Fase 6 — Developer API Endpoints >> GET /api/developer/health
- Location: tests\qa-comprehensive\developer-api.spec.js:61:3

# Error details

```
TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
Call log:
  - → POST https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana/api/auth/developer-device/enroll
    - user-agent: Mozilla/5.0 (Linux; Android 11; Pixel 5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.7727.15 Mobile Safari/537.36
    - accept: */*
    - accept-encoding: gzip,deflate,br
    - content-type: application/json
    - content-length: 53

```

# Test source

```ts
  1   | // ============================================
  2   | // Fase 6: Developer API Tests
  3   | // ============================================
  4   | const { test, expect } = require('@playwright/test');
  5   | const { apiUrl, isBackendUnavailableStatus, safeJson } = require('../helpers/api');
  6   | const { TEST_USERS } = require('../helpers/env');
  7   | 
  8   | async function getDeveloperToken(request) {
  9   |   const dev = TEST_USERS.developer;
  10  |   if (!dev?.email || !dev?.device_id) return { token: null };
  11  | 
  12  |   // Enroll device first
> 13  |   await request.post(apiUrl('/api/auth/developer-device/enroll'), {
      |                 ^ TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
  14  |     data: { device_id: dev.device_id, device_token: dev.device_token },
  15  |   });
  16  | 
  17  |   // Login
  18  |   const res = await request.post(apiUrl('/api/auth/login'), {
  19  |     data: {
  20  |       email: dev.email,
  21  |       password: dev.password,
  22  |       device_id: dev.device_id,
  23  |       device_token: dev.device_token,
  24  |     },
  25  |   });
  26  |   const body = await safeJson(res);
  27  |   return { token: body?.token, status: res.status(), role: body?.role };
  28  | }
  29  | 
  30  | const hasDeveloperCreds = () => {
  31  |   const dev = TEST_USERS.developer;
  32  |   return Boolean(dev?.email && dev?.device_id && dev?.device_token);
  33  | };
  34  | 
  35  | test.describe('Fase 6 — Developer API Endpoints', () => {
  36  |   test('Developer login and verify role', async ({ request }) => {
  37  |     test.skip(!hasDeveloperCreds(), 'Developer credentials not configured');
  38  |     const { token, role, status } = await getDeveloperToken(request);
  39  |     test.skip(isBackendUnavailableStatus(status), 'Backend unavailable');
  40  | 
  41  |     if (status === 200) {
  42  |       expect(token).toBeTruthy();
  43  |       expect(role).toBe('developer');
  44  |     }
  45  |   });
  46  | 
  47  |   test('GET /api/developer/profile', async ({ request }) => {
  48  |     test.skip(!hasDeveloperCreds(), 'Developer credentials not configured');
  49  |     const { token } = await getDeveloperToken(request);
  50  |     test.skip(!token, 'Developer login failed');
  51  | 
  52  |     const res = await request.get(apiUrl('/api/developer/profile'), {
  53  |       headers: { Authorization: `Bearer ${token}` },
  54  |     });
  55  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  56  |     expect(res.status()).toBe(200);
  57  |     const body = await safeJson(res);
  58  |     expect(body.role || body.data?.role).toBe('developer');
  59  |   });
  60  | 
  61  |   test('GET /api/developer/health', async ({ request }) => {
  62  |     test.skip(!hasDeveloperCreds(), 'Developer credentials not configured');
  63  |     const { token } = await getDeveloperToken(request);
  64  |     test.skip(!token, 'Developer login failed');
  65  | 
  66  |     const res = await request.get(apiUrl('/api/developer/health'), {
  67  |       headers: { Authorization: `Bearer ${token}` },
  68  |     });
  69  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  70  |     expect(res.status()).toBe(200);
  71  |     const body = await safeJson(res);
  72  |     console.log('  Developer health:', JSON.stringify(body).slice(0, 300));
  73  |   });
  74  | 
  75  |   test('GET /api/developer/system/config', async ({ request }) => {
  76  |     test.skip(!hasDeveloperCreds(), 'Developer credentials not configured');
  77  |     const { token } = await getDeveloperToken(request);
  78  |     test.skip(!token, 'Developer login failed');
  79  | 
  80  |     const res = await request.get(apiUrl('/api/developer/system/config'), {
  81  |       headers: { Authorization: `Bearer ${token}` },
  82  |     });
  83  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  84  |     expect(res.status()).toBe(200);
  85  |   });
  86  | 
  87  |   test('GET /api/developer/audit-logs', async ({ request }) => {
  88  |     test.skip(!hasDeveloperCreds(), 'Developer credentials not configured');
  89  |     const { token } = await getDeveloperToken(request);
  90  |     test.skip(!token, 'Developer login failed');
  91  | 
  92  |     const res = await request.get(apiUrl('/api/developer/audit-logs?limit=10'), {
  93  |       headers: { Authorization: `Bearer ${token}` },
  94  |     });
  95  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  96  |     expect(res.status()).toBe(200);
  97  |   });
  98  | 
  99  |   test('GET /api/developer/auth-logs', async ({ request }) => {
  100 |     test.skip(!hasDeveloperCreds(), 'Developer credentials not configured');
  101 |     const { token } = await getDeveloperToken(request);
  102 |     test.skip(!token, 'Developer login failed');
  103 | 
  104 |     const res = await request.get(apiUrl('/api/developer/auth-logs?limit=10'), {
  105 |       headers: { Authorization: `Bearer ${token}` },
  106 |     });
  107 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  108 |     expect(res.status()).toBe(200);
  109 |   });
  110 | 
  111 |   test('GET /api/developer/devices', async ({ request }) => {
  112 |     test.skip(!hasDeveloperCreds(), 'Developer credentials not configured');
  113 |     const { token } = await getDeveloperToken(request);
```