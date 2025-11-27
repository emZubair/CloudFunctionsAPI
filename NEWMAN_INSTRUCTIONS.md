# Newman - Command Line Test Execution Guide

## Overview

Newman is a command-line collection runner for Postman. It allows you to run Postman collections as automated tests outside of the GUI, making it perfect for CI/CD pipelines and local development.

## Installation

### Prerequisites
- Node.js 14.0 or higher
- npm (Node Package Manager)

### Install Newman Globally

```bash
npm install -g newman
```

### Install Reporters (Optional but Recommended)

Newman can generate reports in various formats:

```bash
# HTML Report Generator
npm install -g newman-reporter-html

# JSON Report Generator (built-in)
npm install -g newman-reporter-json

# JUnit Report Generator (for CI/CD)
npm install -g newman-reporter-junitxml
```

### Verify Installation

```bash
newman --version
```

## Basic Usage

### Simple Test Run

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json
```

### Run with Reports

```bash
# JSON Report
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters json \
  --reporter-json-export test-results.json

# HTML Report
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters html \
  --reporter-html-export test-report.html

# Multiple Reporters
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters cli,json,html \
  --reporter-json-export results.json \
  --reporter-html-export report.html
```

## Advanced Options

### Timeout Configuration

```bash
# Global timeout for all requests (in milliseconds)
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --timeout 10000

# Per-request timeout
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --timeout-request 5000 \
  --timeout-script 10000
```

### Verbose Output

```bash
# Show detailed request/response information
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --verbose
```

### Run Specific Collection Items

```bash
# Run only a specific folder
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --folder "Products"

# Run only a specific request
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --grep "Get All Products"
```

### Iterate Tests Multiple Times

```bash
# Run the collection 3 times
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  -n 3
```

### SSL/TLS Options

```bash
# Disable SSL verification (use with caution)
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --insecure

# Use custom CA certificate
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --ca /path/to/ca-certificate.pem
```

## Common Workflows

### Full Test Suite with All Reports

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters cli,json,html \
  --reporter-json-export test-results.json \
  --reporter-html-export test-report.html \
  --timeout-request 10000 \
  --verbose
```

### Quick Smoke Test

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --timeout 5000 \
  --folder "Products"
```

### CI/CD Pipeline Run

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters json,junitxml \
  --reporter-json-export test-results.json \
  --reporter-junit-export test-results.xml \
  --timeout-request 10000 \
  --exit-code \
  --bail  # Stop on first failure
```

### Performance Testing

```bash
# Run with detailed timing information
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --reporters json \
  --reporter-json-export performance-results.json \
  --verbose

# Run 5 iterations for load testing
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  -n 5 \
  --reporters json \
  --reporter-json-export load-test-results.json
```

## Understanding Test Results

### JSON Report Structure

```json
{
  "run": {
    "stats": {
      "tests": {
        "total": 50,
        "failed": 0,
        "pending": 0
      },
      "assertions": {
        "total": 75,
        "failed": 0
      },
      "requests": {
        "total": 10,
        "failed": 0
      }
    },
    "failures": [],
    "iterations": []
  }
}
```

### Exit Codes
- **0**: All tests passed
- **1**: Tests failed
- **2**: Problem running the collection
- **3**: Aborted before completion

### CLI Output Interpretation

```
Getting All Products
  ✓ Status code is 200
  ✓ Response is a valid JSON array
  ✓ Each product has required fields
  ✓ Stock values should be non-negative
  ✓ Prices should be valid numbers

  5/5 | ✓ Passed | ✗ Failed

Run Summary
───────────────────────────────
  Requests      │  15
  Test-Scripts  │  15
  Pre-request   │  1
  Assertions    │  75
───────────────────────────────
  Total Tests   │  75
  Passed        │  75
  Failed        │  0
```

## Troubleshooting

### Common Errors

#### "Collection not found"
```bash
# Ensure files are in the correct directory
ls -la *.postman_collection.json
ls -la *.postman_environment.json
```

#### "Cannot find module 'newman'"
```bash
# Reinstall Newman
npm uninstall -g newman
npm install -g newman
```

#### "Invalid JSON in collection"
```bash
# Validate collection syntax
npm install -g jsonlint
jsonlint QA_API_Challenge.postman_collection.json
```

#### "Request timeout"
```bash
# Increase timeout value
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --timeout-request 15000
```

#### "Authentication failed"
```bash
# Verify environment variables, especially auth_token
# Login tests should populate the token before order tests

# Check if pre-request scripts are executed
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --verbose
```

## Environment Variable Management

### Setting Variables via Command Line

```bash
newman run QA_API_Challenge.postman_collection.json \
  -e QA_API_Challenge.postman_environment.json \
  --env-var base_url="https://custom-api.example.com/api" \
  --env-var test_email="custom@example.com"
```

### Exporting Variables After Run

Newman doesn't directly export variables after run, but you can modify the environment file and re-import it.

## Integration with CI/CD Systems

### GitHub Actions
See `.github/workflows/run-tests.yml` for a complete example.

### GitLab CI
```yaml
api_tests:
  image: node:20
  script:
    - npm install -g newman newman-reporter-html
    - newman run QA_API_Challenge.postman_collection.json \
        -e QA_API_Challenge.postman_environment.json \
        --reporters json,html \
        --reporter-json-export results.json \
        --reporter-html-export report.html
  artifacts:
    paths:
      - results.json
      - report.html
```

### Jenkins
```groovy
stage('API Tests') {
  steps {
    sh '''
      npm install -g newman newman-reporter-html
      newman run QA_API_Challenge.postman_collection.json \
        -e QA_API_Challenge.postman_environment.json \
        --reporters json,html \
        --reporter-json-export test-results.json \
        --reporter-html-export test-report.html
    '''
    publishHTML([
      reportDir: '.',
      reportFiles: 'test-report.html',
      reportName: 'Newman Test Report'
    ])
  }
}
```

## Performance Tips

1. **Parallel Execution**: Use Newman with tools like `parallel` for running multiple collections
2. **Request Delays**: Add delays between requests if API rate limiting is an issue
3. **Large Data Sets**: Use data files for parameterized testing
4. **Memory Usage**: Monitor memory for long-running test suites

## Scripting with Newman

### Node.js Integration

```javascript
const newman = require('newman');

newman.run({
  collection: require('./QA_API_Challenge.postman_collection.json'),
  environment: require('./QA_API_Challenge.postman_environment.json'),
  reporters: ['json', 'html'],
  reporter: {
    json: { export: './results.json' },
    html: { export: './report.html' }
  }
}, (err, summary) => {
  if (err) throw err;
  console.log('Tests completed');
  process.exit(summary.run.failures.length > 0 ? 1 : 0);
});
```

## Resources

- [Newman Official Documentation](https://learning.postman.com/docs/running-collections/using-newman-cli/command-line-integration-with-newman/)
- [Postman Collection Format](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [GitHub Actions Integration](https://docs.github.com/en/actions)

---

**Last Updated**: November 27, 2025
