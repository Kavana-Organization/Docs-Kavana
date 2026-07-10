# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: qa-comprehensive\rbac-cross-role.spec.js >> Fase 3 — RBAC Cross-Role Access Control >> Admin cannot access /api/koordinator/stats
- Location: tests\qa-comprehensive\rbac-cross-role.spec.js:97:3

# Error details

```
Error: expect(received).toContain(expected) // indexOf

Expected value: 200
Received array: [401, 403]
```

# Test source

```ts
  5   | const { apiUrl, isBackendUnavailableStatus, safeJson } = require('../helpers/api');
  6   | const { ALL_DOSEN, TEST_USERS, hasRoleCredential } = require('../helpers/env');
  7   | 
  8   | async function loginViaApi(request, email, password, extras = {}) {
  9   |   const res = await request.post(apiUrl('/api/auth/login'), {
  10  |     data: { email, password, ...extras },
  11  |   });
  12  |   const body = await safeJson(res);
  13  |   return { token: body?.token, role: body?.role, status: res.status() };
  14  | }
  15  | 
  16  | async function getDosenToken(request) {
  17  |   const dosen = ALL_DOSEN[1]; // Pak Rolly
  18  |   return loginViaApi(request, dosen.email, dosen.password);
  19  | }
  20  | 
  21  | async function getAdminToken(request) {
  22  |   const admin = TEST_USERS.admin;
  23  |   return loginViaApi(request, admin.email, admin.password);
  24  | }
  25  | 
  26  | async function getDeveloperToken(request) {
  27  |   const dev = TEST_USERS.developer;
  28  |   if (!dev?.email) return { token: null };
  29  |   await request.post(apiUrl('/api/auth/developer-device/enroll'), {
  30  |     data: { device_id: dev.device_id, device_token: dev.device_token },
  31  |   });
  32  |   return loginViaApi(request, dev.email, dev.password, {
  33  |     device_id: dev.device_id,
  34  |     device_token: dev.device_token,
  35  |   });
  36  | }
  37  | 
  38  | test.describe('Fase 3 — RBAC Cross-Role Access Control', () => {
  39  | 
  40  |   // ---- Dosen should NOT access admin endpoints ----
  41  |   test('Dosen cannot access /api/admin/stats', async ({ request }) => {
  42  |     const login = await getDosenToken(request);
  43  |     test.skip(!login.token, 'Dosen login failed');
  44  | 
  45  |     const res = await request.get(apiUrl('/api/admin/stats'), {
  46  |       headers: { Authorization: `Bearer ${login.token}` },
  47  |     });
  48  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  49  |     expect([401, 403]).toContain(res.status());
  50  |   });
  51  | 
  52  |   test('Dosen cannot access /api/admin/users', async ({ request }) => {
  53  |     const login = await getDosenToken(request);
  54  |     test.skip(!login.token, 'Dosen login failed');
  55  | 
  56  |     const res = await request.get(apiUrl('/api/admin/users'), {
  57  |       headers: { Authorization: `Bearer ${login.token}` },
  58  |     });
  59  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  60  |     expect([401, 403]).toContain(res.status());
  61  |   });
  62  | 
  63  |   test('Dosen cannot access /api/developer/health', async ({ request }) => {
  64  |     const login = await getDosenToken(request);
  65  |     test.skip(!login.token, 'Dosen login failed');
  66  | 
  67  |     const res = await request.get(apiUrl('/api/developer/health'), {
  68  |       headers: { Authorization: `Bearer ${login.token}` },
  69  |     });
  70  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  71  |     expect([401, 403]).toContain(res.status());
  72  |   });
  73  | 
  74  |   // ---- Admin should NOT access dosen-specific endpoints ----
  75  |   test('Admin cannot access /api/dosen/profile', async ({ request }) => {
  76  |     const login = await getAdminToken(request);
  77  |     test.skip(!login.token, 'Admin login failed');
  78  | 
  79  |     const res = await request.get(apiUrl('/api/dosen/profile'), {
  80  |       headers: { Authorization: `Bearer ${login.token}` },
  81  |     });
  82  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  83  |     expect([401, 403]).toContain(res.status());
  84  |   });
  85  | 
  86  |   test('Admin cannot access /api/dosen/bimbingan', async ({ request }) => {
  87  |     const login = await getAdminToken(request);
  88  |     test.skip(!login.token, 'Admin login failed');
  89  | 
  90  |     const res = await request.get(apiUrl('/api/dosen/bimbingan'), {
  91  |       headers: { Authorization: `Bearer ${login.token}` },
  92  |     });
  93  |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  94  |     expect([401, 403]).toContain(res.status());
  95  |   });
  96  | 
  97  |   test('Admin cannot access /api/koordinator/stats', async ({ request }) => {
  98  |     const login = await getAdminToken(request);
  99  |     test.skip(!login.token, 'Admin login failed');
  100 | 
  101 |     const res = await request.get(apiUrl('/api/koordinator/stats'), {
  102 |       headers: { Authorization: `Bearer ${login.token}` },
  103 |     });
  104 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
> 105 |     expect([401, 403]).toContain(res.status());
      |                        ^ Error: expect(received).toContain(expected) // indexOf
  106 |   });
  107 | 
  108 |   test('Admin cannot access /api/developer/health', async ({ request }) => {
  109 |     const login = await getAdminToken(request);
  110 |     test.skip(!login.token, 'Admin login failed');
  111 | 
  112 |     const res = await request.get(apiUrl('/api/developer/health'), {
  113 |       headers: { Authorization: `Bearer ${login.token}` },
  114 |     });
  115 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  116 |     expect([401, 403]).toContain(res.status());
  117 |   });
  118 | 
  119 |   // ---- No token should be rejected everywhere ----
  120 |   test('No token → /api/dosen/profile returns 401/403', async ({ request }) => {
  121 |     const res = await request.get(apiUrl('/api/dosen/profile'));
  122 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  123 |     expect([401, 403]).toContain(res.status());
  124 |   });
  125 | 
  126 |   test('No token → /api/admin/stats returns 401/403', async ({ request }) => {
  127 |     const res = await request.get(apiUrl('/api/admin/stats'));
  128 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  129 |     expect([401, 403]).toContain(res.status());
  130 |   });
  131 | 
  132 |   test('No token → /api/koordinator/stats returns 401/403', async ({ request }) => {
  133 |     const res = await request.get(apiUrl('/api/koordinator/stats'));
  134 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  135 |     expect([401, 403]).toContain(res.status());
  136 |   });
  137 | 
  138 |   test('No token → /api/kaprodi/stats returns 401/403', async ({ request }) => {
  139 |     const res = await request.get(apiUrl('/api/kaprodi/stats'));
  140 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  141 |     expect([401, 403]).toContain(res.status());
  142 |   });
  143 | 
  144 |   test('No token → /api/mahasiswa/profile returns 401/403', async ({ request }) => {
  145 |     const res = await request.get(apiUrl('/api/mahasiswa/profile'));
  146 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  147 |     expect([401, 403]).toContain(res.status());
  148 |   });
  149 | 
  150 |   test('No token → /api/developer/profile returns 401/403', async ({ request }) => {
  151 |     const res = await request.get(apiUrl('/api/developer/profile'));
  152 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  153 |     expect([401, 403]).toContain(res.status());
  154 |   });
  155 | 
  156 |   test('No token → /api/notifications/stats returns 401/403', async ({ request }) => {
  157 |     const res = await request.get(apiUrl('/api/notifications/stats'));
  158 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  159 |     expect([401, 403]).toContain(res.status());
  160 |   });
  161 | });
  162 | 
```