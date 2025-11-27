# QA API Challenge - Testing Suite

Comprehensive test suite for the Firebase QA API Challenge using Postman and Newman with automated CI/CD integration.

[![Run API Tests](https://github.com/emZubair/CloudFunctionsAPI/actions/workflows/run-tests.yml/badge.svg)](https://github.com/emZubair/CloudFunctionsAPI/actions/workflows/run-tests.yml)

## API Endpoints Tested

- **POST /register** - Register new user
- **POST /login** - Login user and receive authentication token
- **GET /products** - Retrieve all products
- **GET /products/:id** - Get single product
- **GET /products?name=<exact_name>** - Search products by exact name (case-sensitive)
- **POST /orders** - Create new order (authenticated)
- **GET /orders** - Retrieve all orders (authenticated)
- **GET /orders/:id** - Retrieve specific order (authenticated)

---

## Setup & Testing

---

### Option 1: Using Postman GUI

1. **Download and Open Postman** from [postman.com](https://postman.com)
2. **Import Collection**:
   - Click "Import" button
   - Select `QA_API_Challenge.postman_collection.json`
3. **Import Environment**:
   - Click "Import" button
   - Select `QA_API_Challenge.postman_environment.json`
4. **Run Tests**:
   - Select the collection in left sidebar
   - Click "Run" to execute all tests
   - View results in Collection Runner

### Option 2: Using Newman CLI

### What is Newman?

**Newman** is a command-line tool that runs Postman tests automatically without needing to open the Postman application. Think of it as a "headless" version of Postman - instead of clicking buttons in the Postman GUI, you run tests from your terminal/command prompt. This is especially useful for automated testing in CI/CD pipelines (like GitHub Actions) where you need tests to run on a schedule without manual intervention.

**Simple analogy**: If Postman is like manually testing a website by clicking through it, Newman is like having a robot automatically test it for you.

1. **Install Newman**:

   ```bash
   npm install -g newman
   ```

2. **Run Tests**:

   ```bash
   newman run QA_API_Challenge.postman_collection.json \
     -e QA_API_Challenge.postman_environment.json \
     --reporters cli,json,html \
     --reporter-json-export results.json \
     --reporter-html-export report.html
   ```

3. **View Results**:
   - Check console output for test results
   - Open `report.html` in browser for detailed HTML report
   - Check `results.json` for structured test data

---

## Automated Testing via GitHub Actions

Tests run automatically on a daily schedule (2:00 AM UTC) and can also be triggered manually.

### Viewing Test Reports

Test reports are automatically published to **GitHub Pages** after each run:

1. Go to repository **Settings → Pages**
2. Click the GitHub Pages URL
3. View the test dashboard with:
   - Total tests run
   - Passed/Failed counts
   - Success percentage
   - Timestamp of last run
   - Links to detailed HTML and JSON reports

### Manual Trigger

To run tests manually:

1. Go to **Actions** tab
2. Select **"Run API Tests"** workflow
3. Click **"Run workflow"**
4. Monitor execution and view results

---

## Test Coverage

**Total Test Cases**: 17 API requests with 53+ assertions

- **13 Positive Tests** (happy path scenarios)
- **4 Negative Tests** (error handling and edge cases)
- **100% Pass Rate** with full schema validation

### Test Categories

- **Authentication**: 5 tests (register, login, error cases)
- **Products**: 5 tests (list, get by ID, search, error cases)
- **Orders**: 7 tests (create, list, get, authentication validation)

---

## Bugs Found

Three intentional bugs were discovered during testing:

1. **Negative Stock Values** (Product ID 3 - Mouse)

   - Issue: Stock value is -1 (should be ≥ 0)
   - Impact: Inventory inconsistency

2. **Missing Input Validation** (Registration endpoint)

   - Issue: API accepts requests without email/password
   - Impact: Invalid users can be created

3. **Zero-Price Products** (Product ID 999 - Legacy Device)
   - Issue: Price is 0 (should be > 0)
   - Impact: Pricing inconsistency

**For detailed test case information and bug descriptions**, see [`TEST_CASES_DOCUMENTATION.md`](TEST_CASES_DOCUMENTATION.md)

---

## Files

- `QA_API_Challenge.postman_collection.json` - Postman collection with 17 API requests and test scripts
- `QA_API_Challenge.postman_environment.json` - Pre-configured environment variables
- `.github/workflows/run-tests.yml` - GitHub Actions workflow for automated testing
- `TEST_CASES_DOCUMENTATION.md` - Complete reference of all 17 test cases with descriptions
- `README.md` - This file
- `INSTRUCTIONS.md` - Original QA challenge requirements

---

## Test Execution Results

Each test validates:

- ✅ HTTP status codes
- ✅ Response schemas and field types
- ✅ Business logic (user isolation, order totals)
- ✅ Authentication and authorization
- ✅ Data validation (non-negative values, case-sensitive search)
- ✅ Error handling

---

## Quick Reference

| Action              | Command                                                                                            |
| ------------------- | -------------------------------------------------------------------------------------------------- |
| Run tests locally   | `newman run QA_API_Challenge.postman_collection.json -e QA_API_Challenge.postman_environment.json` |
| View live reports   | GitHub Pages URL (check Actions → latest run)                                                      |
| Manual test trigger | Actions tab → Run API Tests → Run workflow                                                         |
| Test schedule       | Daily at 2:00 AM UTC                                                                               |

---

For complete test case documentation, bug reports, and technical details, see [`TEST_CASES_DOCUMENTATION.md`](TEST_CASES_DOCUMENTATION.md).
