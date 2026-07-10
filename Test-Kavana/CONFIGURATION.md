# Konfigurasi Selenium WebDriver Testing untuk Kavana

## 1. File Konfigurasi Utama

### 1.1. `.env` (Environment Variables)
```env
# Base URLs
FRONTEND_URL=https://kavana.my.id
BACKEND_URL=https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana

# Test Credentials (GANTI dengan akun test sebenarnya)
TEST_DOSEN_EMAIL=198001012005011001
TEST_DOSEN_PASSWORD=password123
TEST_ADMIN_EMAIL=admin001
TEST_ADMIN_PASSWORD=admin123

# Browser Configuration
BROWSER=chrome              # chrome, firefox, edge
HEADLESS=false              # true untuk mode headless (CI/CD)
WINDOW_WIDTH=1920
WINDOW_HEIGHT=1080

# Timeout Configuration (milliseconds)
IMPLICIT_WAIT=10000         # Element wait timeout
PAGE_LOAD_TIMEOUT=30000     # Page load timeout
SCRIPT_TIMEOUT=30000        # JavaScript execution timeout

# Test Configuration
PARALLEL_INSTANCES=3        # Parallel test threads
RETRY_COUNT=2               # Retry failed tests
SCREENSHOT_ON_FAILURE=true  # Capture screenshots on failure

# Reporting
REPORT_PATH=./reports
SCREENSHOT_PATH=./reports/screenshots
```

### 1.2. `config/test.config.js` (JavaScript Configuration)
File ini meng-import configuration dari `.env` dan mengexport object konfigurasi:

```javascript
// File: config/test.config.js
export const config = {
  // Base URLs
  frontendUrl: 'https://kavana.my.id',
  backendUrl: 'https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana',
  
  // Test Credentials (default values)
  testDosen: { nip: '198001012005011001', password: 'password123' },
  testAdmin: { nip: 'admin001', password: 'admin123' },
  
  // Browser Configuration
  browser: 'chrome',        // chrome, firefox, edge
  headless: false,
  windowSize: { width: 1920, height: 1080 },
  
  // Timeouts
  timeouts: {
    implicit: 10000,
    pageLoad: 30000,
    script: 30000
  },
  
  // Test Configuration
  parallel: { instances: 3, retryCount: 2 },
  
  // Reporting
  reporting: {
    screenshotOnFailure: true,
    reportPath: './reports',
    screenshotPath: './reports/screenshots'
  }
};
```

### 1.3. `config/driver.config.js` (WebDriver Configuration)
Konfigurasi untuk Selenium WebDriver:

```javascript
// Supports:
// - Chrome (default)
// - Firefox
// - Edge

export class DriverManager {
  static async createDriver(browserType = null) {
    const browser = browserType || config.browser;
    // Returns configured WebDriver instance
  }
}
```

## 2. Dependencies Configuration (`package.json`)

```json
{
  "name": "kavana-selenium-tests",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "test": "node tests/runner.js",
    "test:ui": "node tests/ui/login.test.js",
    "test:api": "node tests/api/auth.test.js",
    "test:e2e": "node tests/e2e/full-flow.test.js",
    "test:chrome": "BROWSER=chrome node tests/runner.js",
    "test:firefox": "BROWSER=firefox node tests/runner.js",
    "test:headless": "HEADLESS=true node tests/runner.js"
  },
  "dependencies": {
    "selenium-webdriver": "^4.27.0",
    "axios": "^1.7.9",
    "dotenv": "^17.4.2"
  },
  "devDependencies": {
    "chromedriver": "^131.0.3"
  }
}
```

## 3. Lokators Configuration (Page Objects)

### 3.1. `pages/LoginPage.js` - Login Page Locators
```javascript
export class LoginPage extends BasePage {
  constructor(driver) {
    super(driver);
    this.locators = {
      nipInput: By.css('input[name="nip"], input[type="text"][placeholder*="NIP"]'),
      passwordInput: By.css('input[name="password"], input[type="password"]'),
      loginButton: By.css('button[type="submit"], button:contains("Login")'),
      errorMessage: By.css('.error-message, .alert-error'),
      successMessage: By.css('.success-message, .alert-success'),
      forgotPasswordLink: By.css('a[href*="forgot"]'),
      rememberMeCheckbox: By.css('input[type="checkbox"]')
    };
  }
}
```

### 3.2. `pages/DashboardPage.js` - Dashboard Page Locators
```javascript
export class DashboardPage extends BasePage {
  constructor(driver) {
    super(driver);
    this.locators = {
      welcomeMessage: By.css('.welcome-message, h1:contains("Welcome")'),
      statsCards: By.css('.stats-card, .metric-card'),
      logoutButton: By.css('button:contains("Logout")'),
      adminPanel: By.css('.admin-panel, [data-role="admin"]'),
      dosenPanel: By.css('.dosen-panel, [data-role="dosen"]'),
      notificationBell: By.css('.notification-bell')
    };
  }
}
```

## 4. Test Data Configuration

### 4.1. `fixtures/test-data.js`
```javascript
export const testData = {
  users: {
    dosen: {
      nip: '198001012005011001',
      password: 'password123',
      name: 'Dr. Dosen Test',
      role: 'dosen'
    },
    admin: {
      nip: 'admin001',
      password: 'admin123',
      name: 'Administrator',
      role: 'admin'
    }
  },
  
  endpoints: {
    // Auth
    login: '/api/auth/login',
    logout: '/api/auth/logout',
    
    // Dosen
    dosenProfile: '/api/dosen/profile',
    dosenBimbingan: '/api/dosen/bimbingan',
    
    // Health
    health: '/health',
    ready: '/ready',
    live: '/live',
    
    // Docs
    docsJson: '/docs.json'
  }
};
```

## 5. Browser Configuration Details

### 5.1. Chrome Configuration
```javascript
const options = new chrome.Options();
options.addArguments(
  '--no-sandbox',
  '--disable-dev-shm-usage',
  '--disable-gpu',
  '--window-size=1920,1080',
  '--user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
);

if (config.headless) {
  options.addArguments('--headless=new');
}
```

### 5.2. Firefox Configuration
```javascript
const options = new firefox.Options();

if (config.headless) {
  options.addArguments('-headless');
}

options.addArguments(
  '--width=' + config.windowSize.width,
  '--height=' + config.windowSize.height
);
```

### 5.3. Edge Configuration
```javascript
const options = new edge.Options();

if (config.headless) {
  options.addArguments('--headless=new');
}

options.addArguments(
  '--no-sandbox',
  '--disable-dev-shm-usage',
  '--window-size=' + config.windowSize.width + ',' + config.windowSize.height
);
```

## 6. Timeout Configuration

```javascript
// Set in DriverManager class
await driver.manage().setTimeouts({
  implicit: config.timeouts.implicit,      // 10 seconds
  pageLoad: config.timeouts.pageLoad,      // 30 seconds
  script: config.timeouts.script          // 30 seconds
});
```

## 7. Logging Configuration

### 7.1. `utils/logger.js`
```javascript
class Logger {
  constructor() {
    this.logDir = path.join(__dirname, '../reports/logs');
  }
  
  info(message, data = null) {
    console.log('\x1b[36m%s\x1b[0m', message);  // Cyan
  }
  
  success(message, data = null) {
    console.log('\x1b[32m%s\x1b[0m', message);  // Green
  }
  
  error(message, data = null) {
    console.error('\x1b[31m%s\x1b[0m', message); // Red
  }
  
  warn(message, data = null) {
    console.warn('\x1b[33m%s\x1b[0m', message);  // Yellow
  }
}
```

## 8. Screenshot Configuration

```javascript
// In TestHelpers class
static async takeScreenshot(driver, testName) {
  if (!config.reporting.screenshotOnFailure) {
    return null;
  }
  
  const screenshotDir = path.join(__dirname, '../', config.reporting.screenshotPath);
  
  if (!fs.existsSync(screenshotDir)) {
    fs.mkdirSync(screenshotDir, { recursive: true });
  }
  
  const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
  const filename = `${testName}_${timestamp}.png`;
  const filepath = path.join(screenshotDir, filename);
  
  const screenshot = await driver.takeScreenshot();
  fs.writeFileSync(filepath, screenshot, 'base64');
  
  return filepath;
}
```

## 9. Test Categories Configuration

### 9.1. UI Tests (`tests/ui/login.test.js`)
```javascript
const tests = [
  { name: 'Valid Dosen Login', fn: () => this.testValidLogin() },
  { name: 'Invalid Login', fn: () => this.testInvalidLogin() },
  { name: 'Form Validation', fn: () => this.testFormValidation() }
];
```

### 9.2. API Tests (`tests/api/auth.test.js`)
```javascript
const tests = [
  { name: 'Backend Health Check', fn: () => this.testBackendHealth() },
  { name: 'Dosen Login', fn: () => this.testDosenLogin() },
  { name: 'Invalid Login Rejection', fn: () => this.testInvalidLogin() },
  { name: 'Unauthorized Access Protection', fn: () => this.testAccessWithoutToken() },
  { name: 'Swagger Docs', fn: () => this.testSwaggerDocs() }
];
```

### 9.3. RBAC Tests (`tests/api/rbac.test.js`)
```javascript
const tests = [
  { name: 'Dosen Accesses Own Profile', fn: () => this.testDosenAccessesOwnProfile() },
  { name: 'Admin Cannot Access Dosen Profile', fn: () => this.testAdminCannotAccessDosenProfile() },
  { name: 'Dosen Bimbingan Access', fn: () => this.testDosenBimbinganAccess() }
];
```

### 9.4. E2E Tests (`tests/e2e/full-flow.test.js`)
```javascript
const tests = [
  { name: 'Full Login to Dashboard Flow', fn: () => this.testFullLoginToDashboard() },
  { name: 'Dashboard Navigation', fn: () => this.testDashboardNavigation() },
  { name: 'Logout Functionality', fn: () => this.testLogout() }
];
```

## 10. Running Configuration

### 10.1. Default Run
```bash
# Default: Chrome browser, not headless
npm test
```

### 10.2. Browser-specific
```bash
# Chrome (default)
npm run test:chrome

# Firefox
npm run test:firefox

# Headless mode (for CI/CD)
npm run test:headless
```

### 10.3. Test Suite-specific
```bash
# UI tests only
npm run test:ui

# API tests only
npm run test:api

# E2E tests only
npm run test:e2e
```

## 11. Environment Variables Reference

| Variable | Default | Description |
|----------|---------|-------------|
| `FRONTEND_URL` | `https://kavana.my.id` | Frontend URL |
| `BACKEND_URL` | `https://asia-southeast2-renzip-478811.cloudfunctions.net/kavana` | Backend URL |
| `TEST_DOSEN_EMAIL` | `198001012005011001` | Dosen test NIP |
| `TEST_DOSEN_PASSWORD` | `password123` | Dosen test password |
| `BROWSER` | `chrome` | Browser to use |
| `HEADLESS` | `false` | Headless mode |
| `WINDOW_WIDTH` | `1920` | Browser window width |
| `WINDOW_HEIGHT` | `1080` | Browser window height |
| `IMPLICIT_WAIT` | `10000` | Implicit wait in ms |
| `PAGE_LOAD_TIMEOUT` | `30000` | Page load timeout in ms |
| `SCRIPT_TIMEOUT` | `30000` | Script timeout in ms |
| `PARALLEL_INSTANCES` | `3` | Parallel test instances |
| `RETRY_COUNT` | `2` | Retry count for failed tests |
| `SCREENSHOT_ON_FAILURE` | `true` | Take screenshots on failure |

## 12. Configuration Validation

Untuk memvalidasi konfigurasi, jalankan:
```bash
cd D:/Kavana/selenium-tests
node -e "import dotenv from 'dotenv'; dotenv.config(); console.log('Config loaded:', process.env.FRONTEND_URL, process.env.BACKEND_URL);"
```

## 13. Troubleshooting Configuration

### 13.1. Invalid Backend URL
```bash
# Test backend connectivity
curl $BACKEND_URL/health
```

### 13.2. Invalid Browser Configuration
```bash
# Check installed browsers
where chromedriver  # Windows
which chromedriver # Linux/macOS
```

### 13.3. Environment Variables Not Loading
```bash
# Check .env file
cat .env

# Test with dotenv
node -e "require('dotenv').config(); console.log(process.env.BACKEND_URL);"
```

## 14. Summary

Konfigurasi Selenium Testing Automation untuk Kavana sudah lengkap dan mencakup:

✅ **Environment Configuration** - `.env` dan `config/test.config.js`
✅ **Browser Configuration** - Chrome, Firefox, Edge support
✅ **Page Object Configuration** - Locators untuk semua halaman
✅ **Test Data Configuration** - Fixtures dan sample data
✅ **Logging Configuration** - Color-coded output dan file logging
✅ **Screenshot Configuration** - Automatic capture on failure
✅ **Timeout Configuration** - Flexible timeout settings
✅ **CI/CD Configuration** - Headless mode support

Untuk menjalankan testing setelah konfigurasi:
```bash
# Install dependencies (jika belum)
npm install

# Update .env dengan credentials sebenarnya
# (Ganti TEST_DOSEN_EMAIL, TEST_DOSEN_PASSWORD, dll)

# Jalankan testing
npm test
```
