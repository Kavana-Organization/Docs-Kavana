# Setup Testing Automation untuk Kavana

## Langkah 1: Install Dependencies
```bash
cd selenium-tests
npm install
```

## Langkah 2: Konfigurasi Environment
```bash
cp .env.example .env
# Edit file .env dengan credentials test Anda
```

Isi `.env` minimal:
```env
FRONTEND_URL=https://kavana.my.id
BACKEND_URL=https://api-kavana-v2-360171485946.asia-southeast2.run.app
TEST_DOSEN_NIP=<your_nip>
TEST_DOSEN_PASSWORD=<your_password>
```

## Langkah 3: Verifikasi Setup
```bash
# Test koneksi backend
curl https://api-kavana-v2-360171485946.asia-southeast2.run.app/health

# Test koneksi frontend
curl https://kavana.my.id
```

## Langkah 4: Run Tests
```bash
# Run all tests
npm test

# Run specific test suites
npm run test:ui      # UI login tests
npm run test:api     # API tests
npm run test:e2e     # E2E tests
```

## Troubleshooting Cepat

### Error: Port in use
```bash
npm run test:firefox    # Gunakan Firefox
# atau
pkill chromedriver      # Linux/macOS
taskkill /f /im chromedriver.exe  # Windows
```

### Error: Connection timeout
Edit `.env`:
```env
PAGE_LOAD_TIMEOUT=60000
IMPLICIT_WAIT=20000
```

### Error: Invalid credentials
Pastikan `.env` credentials benar:
```env
TEST_DOSEN_NIP=<actual_nip>
TEST_DOSEN_PASSWORD=<actual_password>
```

## Browser Options
```bash
npm run test:chrome    # Google Chrome
npm run test:firefox   # Mozilla Firefox
npm run test:headless  # Headless mode untuk CI/CD
```

## View Results
- **Logs**: `reports/logs/test-*.log`
- **Screenshots**: `reports/screenshots/`
- **Console**: Test output langsung di terminal

## Test Duration
- UI Tests: ~10-15 detik
- API Tests: ~5-10 detik
- E2E Tests: ~15-20 detik
- **Total**: ~30-45 detik

## Next Steps
1. Run `npm test` untuk initial test
2. Check logs untuk results
3. Customize tests jika perlu
4. Integrate dengan CI/CD
