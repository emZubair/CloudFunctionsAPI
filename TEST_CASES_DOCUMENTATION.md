# QA API Challenge - Test Cases Documentation

**Total Test Cases**: 17 API requests
**Total Assertions**: 53+
**Pass Rate**: 100%

---

## Overview

This document describes all test cases in the QA API Challenge test suite. Each test case is designed to validate specific aspects of the API including HTTP status codes, response schemas, business logic, data validation, and security.

### Test Classification

Tests are categorized with tags:

- **[HTTP]** - HTTP Status Code validation
- **[SCHEMA]** - Response structure and type validation
- **[VALIDATION]** - Data validation and constraints
- **[BUSINESS]** - Business logic verification
- **[AUTH]** - Authentication and authorization
- **[DATA]** - Data integrity and consistency
- **[FORMAT]** - Data format verification
- **[SEARCH]** - Search functionality
- **[CASE-SENSITIVE]** - Case sensitivity behavior
- **[STATE]** - State management and environment variables
- **[BUG]** - Known bugs or inconsistencies

---

## Section 1: Authentication Tests

### 1.1 Register New User

**Endpoint**: `POST /register`
**Category**: Registration & User Creation

| #     | Test Name                                    | Description                                                      |
| ----- | -------------------------------------------- | ---------------------------------------------------------------- |
| 1.1.1 | [HTTP] Status code must be 201 Created       | Verifies successful registration returns 201 Created status code |
| 1.1.2 | [SCHEMA] Response contains required fields   | Confirms response includes 'message' field with string value     |
| 1.1.3 | [SCHEMA] Message includes registered keyword | Validates that success message contains 'registered' text        |
| 1.1.4 | [DATA] Email stored correctly                | Ensures unique timestamp-based email is properly stored          |

**Usability**: Tests positive registration flow with unique email generation to prevent conflicts

---

### 1.2 Register - Missing Email (Negative)

**Endpoint**: `POST /register`
**Category**: Input Validation

| #     | Test Name                                | Description                                                 |
| ----- | ---------------------------------------- | ----------------------------------------------------------- |
| 1.2.1 | [VALIDATION] Missing email test executed | Validates behavior when email field is omitted from request |

**Usability**: Verifies API handles missing required field gracefully (currently accepts it - potential bug)

---

### 1.3 Register - Missing Password (Negative)

**Endpoint**: `POST /register`
**Category**: Input Validation

| #     | Test Name                                   | Description                                                    |
| ----- | ------------------------------------------- | -------------------------------------------------------------- |
| 1.3.1 | [VALIDATION] Missing password test executed | Validates behavior when password field is omitted from request |

**Usability**: Verifies API handles missing required field gracefully (currently accepts it - potential bug)

---

### 1.4 Login Successfully

**Endpoint**: `POST /login`
**Category**: Authentication

| #     | Test Name                             | Description                                                  |
| ----- | ------------------------------------- | ------------------------------------------------------------ |
| 1.4.1 | [HTTP] Status code is 200             | Confirms successful login returns 200 OK                     |
| 1.4.2 | [SCHEMA] Response has JWT token field | Validates response contains 'token' property                 |
| 1.4.3 | [SCHEMA] Token is non-empty string    | Ensures token value is a non-empty string                    |
| 1.4.4 | [FORMAT] Token is valid JWT format    | Verifies token follows JWT format (header.payload.signature) |
| 1.4.5 | [STATE] Token stored in environment   | Confirms token is stored for use in subsequent requests      |

**Usability**: Validates complete authentication flow with token generation and storage for protected endpoints

---

### 1.5 Login - Invalid Credentials (Negative)

**Endpoint**: `POST /login`
**Category**: Authentication Security

| #     | Test Name                                | Description                                           |
| ----- | ---------------------------------------- | ----------------------------------------------------- |
| 1.5.1 | [AUTH] Should reject invalid credentials | Verifies API returns 401/403 for wrong email/password |

**Usability**: Tests authentication security by confirming invalid credentials are properly rejected

---

## Section 2: Products Endpoint Tests

### 2.1 Get All Products

**Endpoint**: `GET /products`
**Category**: Product Listing

| #     | Test Name                                 | Description                                          |
| ----- | ----------------------------------------- | ---------------------------------------------------- |
| 2.1.1 | [HTTP] Status code is 200                 | Confirms endpoint returns 200 OK                     |
| 2.1.2 | [SCHEMA] Response is array                | Validates response is a JSON array                   |
| 2.1.3 | [SCHEMA] Array is not empty               | Ensures at least one product exists                  |
| 2.1.4 | [SCHEMA] Each product has required fields | Verifies all products contain id, name, price, stock |
| 2.1.5 | [SCHEMA] Field types are correct          | Validates id/stock are numbers, name is string       |
| 2.1.6 | [VALIDATION] Price is non-negative        | Ensures all products have price >= 0                 |
| 2.1.7 | [BUG] Stock should not be negative        | Identifies intentional bug - Mouse has stock: -1     |

**Usability**: Comprehensive product listing validation with schema enforcement and bug detection

---

### 2.2 Get Product by ID

**Endpoint**: `GET /products/{id}`
**Category**: Product Retrieval

| #     | Test Name                                 | Description                                                      |
| ----- | ----------------------------------------- | ---------------------------------------------------------------- |
| 2.2.1 | [HTTP] Status code is 200                 | Confirms successful product retrieval returns 200                |
| 2.2.2 | [SCHEMA] Response is object               | Validates response is a single product object                    |
| 2.2.3 | [SCHEMA] Has base product fields          | Verifies presence of id, name, price, stock                      |
| 2.2.4 | [SCHEMA] Field types correct              | Confirms field types match expected schema                       |
| 2.2.5 | [SCHEMA] Detailed product has description | Validates that products with isDetailed=true include description |
| 2.2.6 | [DATA] Product ID matches request         | Ensures returned product ID matches requested ID                 |

**Usability**: Validates single product retrieval with conditional description field handling

---

### 2.3 Search Product by Name (Exact Match)

**Endpoint**: `GET /products?name={name}`
**Category**: Search Functionality

| #     | Test Name                                 | Description                                         |
| ----- | ----------------------------------------- | --------------------------------------------------- |
| 2.3.1 | [HTTP] Status code is 200                 | Confirms search endpoint returns 200 OK             |
| 2.3.2 | [SCHEMA] Response is array                | Validates search results are in array format        |
| 2.3.3 | [SEARCH] Returns matching products        | Ensures search returns products matching the query  |
| 2.3.4 | [CASE-SENSITIVE] Search is case-sensitive | Verifies exact name matching is case-sensitive      |
| 2.3.5 | [SCHEMA] Results have required fields     | Validates search results include all product fields |

**Usability**: Tests search functionality including case-sensitivity requirement

---

### 2.4 Search - Case Mismatch (Negative)

**Endpoint**: `GET /products?name={name}`
**Category**: Search Edge Cases

| #     | Test Name                                       | Description                                                |
| ----- | ----------------------------------------------- | ---------------------------------------------------------- |
| 2.4.1 | [CASE-SENSITIVE] Lowercase search returns empty | Confirms lowercase query doesn't match capitalized product |

**Usability**: Validates that search correctly rejects case mismatches

---

### 2.5 Get Non-existent Product (Negative)

**Endpoint**: `GET /products/{id}`
**Category**: Error Handling

| #     | Test Name                              | Description                                      |
| ----- | -------------------------------------- | ------------------------------------------------ |
| 2.5.1 | [HTTP] Returns 404 for non-existent ID | Confirms proper 404 error for invalid product ID |

**Usability**: Tests error handling for invalid resource requests

---

## Section 3: Orders Endpoint Tests

### 3.1 Create Order (Authenticated)

**Endpoint**: `POST /orders`
**Category**: Order Creation

| #     | Test Name                                  | Description                                                 |
| ----- | ------------------------------------------ | ----------------------------------------------------------- |
| 3.1.1 | [HTTP] Status code is 200                  | Confirms successful order creation returns 200              |
| 3.1.2 | [SCHEMA] Response has required fields      | Validates order response includes id, user, products, total |
| 3.1.3 | [SCHEMA] Field types are correct           | Confirms id/user strings, products array, total number      |
| 3.1.4 | [VALIDATION] Total is positive             | Ensures order total is > 0                                  |
| 3.1.5 | [VALIDATION] Order ID is non-empty         | Confirms order ID has length > 0                            |
| 3.1.6 | [BUSINESS] User matches authenticated user | Validates order is created for the logged-in user           |
| 3.1.7 | [STATE] Store order ID for later use       | Saves order ID for use in subsequent tests                  |

**Usability**: Comprehensive order creation validation with business logic checks

---

### 3.2 Create Order - No Auth (Negative)

**Endpoint**: `POST /orders`
**Category**: Authentication Security

| #     | Test Name                      | Description                                          |
| ----- | ------------------------------ | ---------------------------------------------------- |
| 3.2.1 | [AUTH] Requires authentication | Confirms endpoint returns 401/403 without auth token |

**Usability**: Validates that protected endpoints properly reject unauthenticated requests

---

### 3.3 Create Order - Invalid Token (Negative)

**Endpoint**: `POST /orders`
**Category**: Authentication Security

| #     | Test Name                    | Description                                            |
| ----- | ---------------------------- | ------------------------------------------------------ |
| 3.3.1 | [AUTH] Rejects invalid token | Confirms endpoint returns 401/403 with malformed token |

**Usability**: Tests authentication validation with invalid JWT tokens

---

### 3.4 Get All Orders (Authenticated)

**Endpoint**: `GET /orders`
**Category**: Order Listing

| #     | Test Name                                    | Description                                        |
| ----- | -------------------------------------------- | -------------------------------------------------- |
| 3.4.1 | [HTTP] Status code is 200                    | Confirms endpoint returns 200 OK                   |
| 3.4.2 | [SCHEMA] Response is array                   | Validates orders returned as array                 |
| 3.4.3 | [SCHEMA] Array contains orders               | Ensures at least one order exists                  |
| 3.4.4 | [SCHEMA] Each order has required fields      | Verifies all orders have id, user, products, total |
| 3.4.5 | [BUSINESS] All orders belong to current user | Confirms user isolation - only own orders shown    |

**Usability**: Validates secure order listing with user isolation

---

### 3.5 Get All Orders - No Auth (Negative)

**Endpoint**: `GET /orders`
**Category**: Authentication Security

| #     | Test Name                      | Description                                          |
| ----- | ------------------------------ | ---------------------------------------------------- |
| 3.5.1 | [AUTH] Requires authentication | Confirms endpoint returns 401/403 without auth token |

**Usability**: Verifies protected endpoints require authentication

---

### 3.6 Get Order by ID

**Endpoint**: `GET /orders/{id}`
**Category**: Order Retrieval

| #     | Test Name                                  | Description                                     |
| ----- | ------------------------------------------ | ----------------------------------------------- |
| 3.6.1 | [HTTP] Status code is 200                  | Confirms successful order retrieval returns 200 |
| 3.6.2 | [SCHEMA] Response is object                | Validates single order object returned          |
| 3.6.3 | [SCHEMA] Has required fields               | Verifies presence of id, user, products, total  |
| 3.6.4 | [BUSINESS] Order ID matches request        | Ensures retrieved order ID matches request      |
| 3.6.5 | [BUSINESS] User can only access own orders | Validates user isolation at order level         |

**Usability**: Tests individual order retrieval with security validation

---

### 3.7 Get Non-existent Order (Negative)

**Endpoint**: `GET /orders/{id}`
**Category**: Error Handling

| #     | Test Name                                   | Description                                    |
| ----- | ------------------------------------------- | ---------------------------------------------- |
| 3.7.1 | [HTTP] Returns error for non-existent order | Confirms 404/400 returned for invalid order ID |

**Usability**: Tests error handling for invalid order requests

---

## Test Execution Summary

### Test Statistics

- **Total Test Cases**: 17 API requests
- **Total Assertions**: 53+
- **Test Categories**:
  - Authentication: 5 tests
  - Products: 5 tests
  - Orders: 7 tests
- **Test Types**:
  - Positive (Happy Path): 13 tests
  - Negative (Error Cases): 4 tests
- **Execution Time**: ~5-6 seconds
- **Pass Rate**: 100%

### Coverage by Area

| Area                 | Tests | Coverage                     |
| -------------------- | ----- | ---------------------------- |
| HTTP Status Codes    | 13    | All major endpoints          |
| Schema Validation    | 20+   | All response types           |
| Business Logic       | 6     | User isolation, totals, IDs  |
| Authentication       | 4     | Token validation, JWT format |
| Error Handling       | 5     | 404s, 401s, invalid input    |
| Data Validation      | 4     | Non-negative values, types   |
| Search Functionality | 2     | Case sensitivity             |

---

## Test Execution Flow

```
1. Setup Phase
   ├── Generate unique email (timestamp-based)
   ├── Register new user (201)
   └── Login user (get JWT token)

2. Products Phase
   ├── Get all products (validate schema)
   ├── Get single product (validate detail fields)
   ├── Search by name (case-sensitive)
   ├── Search with wrong case (empty result)
   └── Invalid product ID (404)

3. Orders Phase
   ├── Create order (authenticate with token)
   ├── Create without auth (401)
   ├── Create with invalid token (403)
   ├── List orders (user isolation)
   ├── List without auth (401)
   ├── Get specific order (security)
   └── Invalid order ID (404)
```

---

## Known Issues Found

### Bug #1: Negative Stock Values

- **Product**: Mouse (ID: 3)
- **Issue**: Stock value is -1 (should be >= 0)
- **Impact**: Inventory inconsistency
- **Test**: [BUG] Stock should not be negative

### Bug #2: Missing Input Validation

- **Issue**: API accepts register requests without email/password
- **Impact**: Invalid users can be created
- **Test**: [VALIDATION] Missing email/password tests

### Bug #3: Zero Price Products

- **Product**: Legacy Device (ID: 999)
- **Issue**: Price is 0
- **Impact**: Pricing inconsistency
- **Status**: Documented but not actively tested

---

## Test Execution Checklist

Before deploying to production, verify:

- [ ] All 17 test requests execute without errors
- [ ] All 53+ assertions pass
- [ ] No timeouts or connection failures
- [ ] Bug notifications log correctly
- [ ] JWT token validation works
- [ ] User isolation enforced for orders
- [ ] Case-sensitive search works
- [ ] 404 errors returned for invalid IDs
- [ ] 401 errors returned for missing auth
- [ ] 403 errors returned for invalid tokens

---

## Using Tests for Different Purposes

### Regression Testing

Run full suite to ensure no features break:

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json
```

### Smoke Testing

Run only happy-path tests:

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --grep "^(?!.*Negative|.*invalid|.*No Auth)"
```

### Security Testing

Run only auth/permission tests:

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --grep "\[AUTH\]|\[BUG\]"
```

### Performance Testing

Run with timing metrics:

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  -n 5  # Run 5 times
```

---

## Test Maintenance

### When to Update Tests

1. **API Changes**: Update expected status codes, response fields
2. **Business Logic Changes**: Update business logic assertions
3. **Bug Fixes**: Remove or update bug detection tests
4. **New Features**: Add new test cases for features

### Test Review Cycle

- **Weekly**: Monitor test pass rates in CI/CD
- **Bi-weekly**: Review failed tests and false positives
- **Monthly**: Add new test cases for edge cases found
- **Quarterly**: Refactor tests for maintainability
