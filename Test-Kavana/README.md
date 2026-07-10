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
