# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: qa-comprehensive\auth-all-roles.spec.js >> Fase 2 — Negative Authentication Tests >> Login with missing password returns 400
- Location: tests\qa-comprehensive\auth-all-roles.spec.js:210:3

# Error details

```
TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
Call log:
  - → POST https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana/api/auth/login
    - user-agent: Mozilla/5.0 (Linux; Android 11; Pixel 5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.7727.15 Mobile Safari/537.36
    - accept: */*
    - accept-encoding: gzip,deflate,br
    - content-type: application/json
    - content-length: 30

```

# Test source

```ts
  111 |   });
  112 | 
  113 |   test('Developer login with device credentials', async ({ request }) => {
  114 |     test.skip(!dev?.email || !dev?.device_id, 'Developer credential not configured');
  115 | 
  116 |     // Enroll first
  117 |     await request.post(apiUrl('/api/auth/developer-device/enroll'), {
  118 |       data: {
  119 |         device_id: dev.device_id,
  120 |         device_token: dev.device_token,
  121 |       },
  122 |     });
  123 | 
  124 |     // Login
  125 |     const result = await loginViaApi(request, dev.email, dev.password, {
  126 |       device_id: dev.device_id,
  127 |       device_token: dev.device_token,
  128 |     });
  129 |     test.skip(isBackendUnavailableStatus(result.status), `Backend unavailable`);
  130 | 
  131 |     if (result.status === 200) {
  132 |       expect(result.body.token).toBeTruthy();
  133 |       expect(result.body.role).toBe('developer');
  134 |       expect(result.body.roles).toBeTruthy();
  135 |     }
  136 |   });
  137 | 
  138 |   test('Developer profile accessible after login', async ({ request }) => {
  139 |     test.skip(!dev?.email || !dev?.device_id, 'Developer credential not configured');
  140 | 
  141 |     await request.post(apiUrl('/api/auth/developer-device/enroll'), {
  142 |       data: { device_id: dev.device_id, device_token: dev.device_token },
  143 |     });
  144 | 
  145 |     const login = await loginViaApi(request, dev.email, dev.password, {
  146 |       device_id: dev.device_id,
  147 |       device_token: dev.device_token,
  148 |     });
  149 |     test.skip(!login.token, 'Developer login failed');
  150 | 
  151 |     const profileRes = await request.get(apiUrl('/api/auth/profile'), {
  152 |       headers: { Authorization: `Bearer ${login.token}` },
  153 |     });
  154 |     expect(profileRes.status()).toBe(200);
  155 |     const profile = await safeJson(profileRes);
  156 |     expect(profile.role).toBe('developer');
  157 |   });
  158 | });
  159 | 
  160 | // =============================================
  161 | // Negative Auth Tests
  162 | // =============================================
  163 | test.describe('Fase 2 — Negative Authentication Tests', () => {
  164 |   test('Login with empty body returns 400', async ({ request }) => {
  165 |     const res = await request.post(apiUrl('/api/auth/login'), { data: {} });
  166 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  167 |     expect(res.status()).toBeGreaterThanOrEqual(400);
  168 |     expect(res.status()).toBeLessThan(500);
  169 |   });
  170 | 
  171 |   test('Login with wrong password returns 400', async ({ request }) => {
  172 |     const res = await request.post(apiUrl('/api/auth/login'), {
  173 |       data: { email: 'awangga@ulbi.ac.id', password: 'wrongpassword123' },
  174 |     });
  175 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  176 |     expect([400, 401, 403]).toContain(res.status());
  177 |   });
  178 | 
  179 |   test('Login with non-existent email returns 400', async ({ request }) => {
  180 |     const res = await request.post(apiUrl('/api/auth/login'), {
  181 |       data: { email: 'nonexistent@ulbi.ac.id', password: 'password123' },
  182 |     });
  183 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  184 |     expect([400, 401, 403, 404]).toContain(res.status());
  185 |   });
  186 | 
  187 |   test('Login with invalid email format returns 400', async ({ request }) => {
  188 |     const res = await request.post(apiUrl('/api/auth/login'), {
  189 |       data: { email: 'not-an-email', password: 'password123' },
  190 |     });
  191 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  192 |     expect(res.status()).toBeGreaterThanOrEqual(400);
  193 |     expect(res.status()).toBeLessThan(500);
  194 |   });
  195 | 
  196 |   test('Profile without token returns 401 or 403', async ({ request }) => {
  197 |     const res = await request.get(apiUrl('/api/auth/profile'));
  198 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  199 |     expect([401, 403]).toContain(res.status());
  200 |   });
  201 | 
  202 |   test('Profile with invalid token returns 401 or 403', async ({ request }) => {
  203 |     const res = await request.get(apiUrl('/api/auth/profile'), {
  204 |       headers: { Authorization: 'Bearer invalid-token-xyz-123' },
  205 |     });
  206 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  207 |     expect([401, 403]).toContain(res.status());
  208 |   });
  209 | 
  210 |   test('Login with missing password returns 400', async ({ request }) => {
> 211 |     const res = await request.post(apiUrl('/api/auth/login'), {
      |                               ^ TimeoutError: apiRequestContext.post: Timeout 15000ms exceeded.
  212 |       data: { email: 'awangga@ulbi.ac.id' },
  213 |     });
  214 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  215 |     expect(res.status()).toBeGreaterThanOrEqual(400);
  216 |     expect(res.status()).toBeLessThan(500);
  217 |   });
  218 | 
  219 |   test('Login with missing email returns 400', async ({ request }) => {
  220 |     const res = await request.post(apiUrl('/api/auth/login'), {
  221 |       data: { password: 'bagas7474' },
  222 |     });
  223 |     test.skip(isBackendUnavailableStatus(res.status()), 'Backend unavailable');
  224 |     expect(res.status()).toBeGreaterThanOrEqual(400);
  225 |     expect(res.status()).toBeLessThan(500);
  226 |   });
  227 | });
  228 | 
```