# QA API Challenge - Testing Suite

Comprehensive test suite for the Firebase QA API Challenge using Postman and Newman. This project includes automated testing for all API endpoints with both positive and negative test cases.

## Project Overview

This testing suite validates a backend API that simulates a Firebase service with intentional inconsistencies and bugs. The suite includes:

- **Postman Collection**: Complete API endpoint requests with built-in test scripts
- **Postman Environment**: Pre-configured variables for easy test execution
- **Newman Integration**: Automated test runs via CLI
- **GitHub Actions**: Continuous testing with scheduled and manual triggers

## Files

- `QA_API_Challenge.postman_collection.json` - Postman collection with all API requests and test scripts
- `QA_API_Challenge.postman_environment.json` - Environment variables (base URL, auth tokens, test data)
- `README.md` - This documentation file
- `.github/workflows/run-tests.yml` - GitHub Actions workflow configuration

## API Endpoints Tested

### Products
- **GET /products** - Retrieve all products
- **GET /products?name=<exact_name>** - Search products by exact name (case-sensitive)
- **GET /products/:id** - Get single product with detailed information

### Authentication
- **POST /register** - Register new user with email and password
- **POST /login** - Login user and receive authentication token

### Orders
- **POST /orders** - Create new order for authenticated user
- **GET /orders** - Retrieve all orders for authenticated user
- **GET /orders/:id** - Retrieve specific order by ID

## Test Coverage

### Positive Test Cases
- ✅ Retrieve all products successfully
- ✅ Search products by exact matching name
- ✅ Get product details by ID with description field
- ✅ Register new user with valid credentials
- ✅ Login with valid credentials and receive token
- ✅ Create order with valid products and receive order ID
- ✅ Calculate order totals correctly
- ✅ Retrieve user's orders
- ✅ Retrieve specific order details

### Negative Test Cases
- ❌ Search with case mismatch (lowercase search)
- ❌ Get non-existent product (404)
- ❌ Register without email field (400)
- ❌ Register without password field (400)
- ❌ Login with invalid credentials (401)
- ❌ Create order without authentication (401)
- ❌ Create order with invalid token (401)
- ❌ Create order with empty products array
- ❌ Get orders without authentication (401)
- ❌ Get non-existent order (404)

## Known Issues/Bugs Found

### Data Validation Issues
1. **Negative Stock Values**: Product ID 3 (Mouse) has stock value of -1, violating the requirement that "Stock values should not be negative"
2. **Zero Price**: Product ID 999 (Legacy Device) has a price of 0, which may indicate incomplete data or legacy product handling

### Expected Behavior Inconsistencies
The test suite identifies these as potential bugs that should be addressed:
- Stock should be validated to ensure non-negative values
- Product pricing should have minimum value constraints

## Setup & Prerequisites

### Local Testing

1. **Install Postman**: Download from [postman.com](https://postman.com)
2. **Install Node.js & Newman**:
   ```bash
   npm install -g newman
   ```
3. **Import Collection & Environment**:
   - Open Postman
   - Import `QA_API_Challenge.postman_collection.json`
   - Import `QA_API_Challenge.postman_environment.json`

### Running Tests Locally

#### Using Postman GUI
1. Open Postman
2. Select the imported environment: "QA API Challenge Environment"
3. Click "Run" on the collection to execute all tests
4. View results in the Collection Runner

#### Using Newman CLI
```bash
# Run all tests with environment variables
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters cli,json \
  --reporter-json-export results.json

# Run specific collection folder
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --folder "Products"

# Run with HTML report
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters cli,html \
  --reporter-html-export report.html
```

## GitHub Actions Integration

Tests run automatically on:
- **Schedule**: Daily at 2:00 AM UTC (configurable in workflow)
- **Manual Trigger**: Via GitHub Actions UI

### Workflow Features
- Installs dependencies (Node.js, Newman)
- Runs complete test suite
- Generates test reports
- Uploads artifacts (test results, reports)
- Provides test summary in workflow logs

### Accessing Results
1. Go to your GitHub repository
2. Click "Actions" tab
3. Select the latest "Run API Tests" workflow run
4. View test summary and logs
5. Download artifacts (JSON reports, HTML reports)

## Test Environment Configuration

The environment file includes:
- **base_url**: API endpoint (https://us-central1-nn-api-challenge.cloudfunctions.net/api)
- **auth_token**: Dynamically set during login tests
- **test_email**: Test user email for registration/login
- **test_password**: Test user password
- **order_id**: Dynamically set when orders are created

### Modifying Environment Variables

Edit `QA_API_Challenge.postman_environment.json`:
```json
{
  "key": "variable_name",
  "value": "new_value",
  "type": "string",
  "enabled": true
}
```

## Test Script Details

Each request includes JavaScript test scripts that verify:

### Response Validation
- Status codes (200, 400, 401, 404)
- Response format (JSON, arrays, objects)
- Required fields presence

### Business Logic Validation
- Stock values are non-negative
- Prices are valid numbers
- Order totals are calculated correctly
- Tokens are properly generated
- Authentication is enforced

### Data Integrity
- Product fields match specifications
- Search results are case-sensitive
- Order data contains correct products
- User can only access their own orders

## Troubleshooting

### Issue: "Request without token should fail" returns 200
**Cause**: Authentication middleware not properly validating missing tokens
**Solution**: Verify API implements proper token validation in middleware

### Issue: Negative stock values not caught
**Cause**: Input validation not enforcing constraints
**Solution**: Add validation layer to prevent negative inventory

### Issue: Case-sensitive search not working
**Cause**: Search function doing case-insensitive comparison
**Solution**: Update search logic to use case-sensitive matching

### Issue: Newman not finding collection file
**Ensure**:
- File paths are correct and absolute
- Collection JSON is valid (use online validator)
- Node.js and Newman are installed: `newman --version`

## Best Practices

1. **Update Test Data**: Modify test emails/passwords in environment for security
2. **Monitor API Changes**: Review test failures after API updates
3. **Performance Testing**: Use Newman with detailed reporters for performance metrics
4. **CI/CD Integration**: Extend GitHub Actions workflow with additional validations
5. **Regular Reporting**: Archive test reports for trend analysis

## Resources

- [Postman Documentation](https://learning.postman.com)
- [Newman GitHub](https://github.com/postmanlabs/newman)
- [Postman Test Scripts](https://learning.postman.com/docs/writing-scripts/test-scripts/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

## Contact & Support

For issues with tests or API behavior, check:
1. Test execution logs in GitHub Actions
2. Newman console output for detailed error messages
3. Postman test results for individual request failures

