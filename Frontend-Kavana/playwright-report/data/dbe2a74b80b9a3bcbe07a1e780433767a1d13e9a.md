# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: qa-comprehensive\health-api.spec.js >> Fase 1 — Backend Health & Connectivity >> GET /docs returns Swagger UI
- Location: tests\qa-comprehensive\health-api.spec.js:43:3

# Error details

```
Error: expect(received).toContain(expected) // indexOf

Expected value: 404
Received array: [200, 301, 302]
```

# Test source

```ts
  1  | // ============================================
  2  | // Fase 1: Backend Health & Connectivity Tests
  3  | // ============================================
  4  | const { test, expect } = require('@playwright/test');
  5  | const { apiUrl, isBackendUnavailableStatus, safeJson } = require('../helpers/api');
  6  | 
  7  | test.describe('Fase 1 — Backend Health & Connectivity', () => {
  8  |   test('GET / returns API OK message', async ({ request }) => {
  9  |     const res = await request.get(apiUrl('/'));
  10 |     expect(res.status()).toBe(200);
  11 |     const body = await safeJson(res);
  12 |     expect(body.message).toContain('API OK');
  13 |   });
  14 | 
  15 |   test('GET /ping returns pong', async ({ request }) => {
  16 |     const res = await request.get(apiUrl('/ping'));
  17 |     expect(res.status()).toBe(200);
  18 |     const body = await safeJson(res);
  19 |     expect(body.status).toBe('ok');
  20 |     expect(body.message).toBe('pong');
  21 |     expect(body.timestamp).toBeTruthy();
  22 |     expect(body.uptime).toBeGreaterThan(0);
  23 |   });
  24 | 
  25 |   test('GET /health returns health status', async ({ request }) => {
  26 |     const res = await request.get(apiUrl('/health'));
  27 |     // 200 = healthy, 503 = degraded but still responding
  28 |     expect([200, 503]).toContain(res.status());
  29 |     const body = await safeJson(res);
  30 |     expect(body).toBeTruthy();
  31 |   });
  32 | 
  33 |   test('GET /ready returns readiness status', async ({ request }) => {
  34 |     const res = await request.get(apiUrl('/ready'));
  35 |     expect([200, 503]).toContain(res.status());
  36 |   });
  37 | 
  38 |   test('GET /live returns liveness status', async ({ request }) => {
  39 |     const res = await request.get(apiUrl('/live'));
  40 |     expect(res.status()).toBe(200);
  41 |   });
  42 | 
  43 |   test('GET /docs returns Swagger UI', async ({ request }) => {
  44 |     const res = await request.get(apiUrl('/docs'));
  45 |     // Swagger serves HTML, may redirect
> 46 |     expect([200, 301, 302]).toContain(res.status());
     |                             ^ Error: expect(received).toContain(expected) // indexOf
  47 |   });
  48 | 
  49 |   test('GET /docs.json returns OpenAPI spec', async ({ request }) => {
  50 |     const res = await request.get(apiUrl('/docs.json'));
  51 |     if (res.status() === 200) {
  52 |       const body = await safeJson(res);
  53 |       expect(body.openapi).toBeTruthy();
  54 |       expect(body.info).toBeTruthy();
  55 |       expect(body.paths).toBeTruthy();
  56 |     }
  57 |   });
  58 | 
  59 |   test('GET /openapi.json returns OpenAPI spec', async ({ request }) => {
  60 |     const res = await request.get(apiUrl('/openapi.json'));
  61 |     if (res.status() === 200) {
  62 |       const body = await safeJson(res);
  63 |       expect(body.openapi).toBe('3.0.3');
  64 |       expect(body.info.title).toContain('Bimbingan');
  65 |     }
  66 |   });
  67 | 
  68 |   test('Non-existent endpoint returns 404 (not 500)', async ({ request }) => {
  69 |     const res = await request.get(apiUrl('/api/nonexistent-route-xyz'));
  70 |     expect(res.status()).not.toBe(500);
  71 |   });
  72 | });
  73 | 
```