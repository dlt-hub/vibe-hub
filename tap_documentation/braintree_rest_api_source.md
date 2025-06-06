# Braintree REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source, specifically for the Braintree API integration. The configuration will enable you to build an effective data pipeline to extract all available data from the Braintree API.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://api.braintreegateway.com/`
- Note: The Braintree API does not specify a version in the base URL.

**Authentication:**
- Type: `basic`
- Required Parameters:
  - `merchant_id`: Your Braintree merchant ID.
  - `public_key`: Your Braintree public key.
  - `private_key`: Your Braintree private key.
- Authentication is handled using Basic Auth with the public key as the username and the private key as the password.

## 2. Available Endpoints

### Transactions Endpoint

**Endpoint Details:**
- Path: `/transactions`
- HTTP Method: `GET`
- Required Query Parameters:
  - `created_at`: Date range for transactions (e.g., `created_at[between]=start_date,end_date`)
- Response Data Structure: JSON object containing transaction details.

**Pagination Configuration:**
- Type: `single_page`
- The Braintree API does not support traditional pagination. Instead, it uses date ranges to fetch data in chunks.

**Data Extraction:**
- JSONPath: Transactions are located directly in the response object.
- Primary Key: `id` (unique identifier for each transaction)
- Key Fields: `id`, `created_at`, `updated_at`, `disbursement_details`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `created_at[between]`
- Date Field: `created_at` and `updated_at` for tracking updates.
- Expected Format: ISO 8601 date format (e.g., `2023-01-01T00:00:00Z`)
- Recommended Initial Values: Start from a fixed date or the last known `updated_at` value.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `transactions` endpoint does not have direct dependencies on other endpoints. However, related data such as customer details may require separate API calls.

## 5. API Behavior & Limits

**Rate Limiting:**
- The Braintree API does not explicitly document rate limits, but it is recommended to implement retry logic with exponential backoff in case of rate limiting.

**Special Requirements:**
- Required Headers: None beyond standard authentication.
- Response Format: JSON
- Error Handling: Implement error handling for common HTTP errors (e.g., 401 Unauthorized, 500 Internal Server Error).
- Data Type Specifications: Ensure correct data type handling as per the Braintree API schema.

## Output Format

Below is the structured configuration for the Braintree API integration:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.braintreegateway.com/",
    "auth": {
        "type": "basic",
        "parameters": {
            "merchant_id": "<your_merchant_id>",
            "public_key": "<your_public_key>",
            "private_key": "<your_private_key>"
        }
    }
}

ENDPOINTS = [
    {
        "name": "transactions",
        "path": "/transactions",
        "method": "GET",
        "data_selector": "transactions",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "created_at[between]",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

## Focus Areas

1. **API Communication**: Ensure correct HTTP methods and authentication headers are used.
2. **Data Location**: Identify where in the JSON response the transaction data is located.
3. **Pagination**: Understand how to use date ranges to fetch data in manageable chunks.
4. **Incremental Updates**: Use the `created_at` and `updated_at` fields to fetch only new or updated records.
5. **Resource Dependencies**: While the transactions endpoint is standalone, consider additional API calls for related data if needed.

This configuration guide should help you set up a robust data pipeline for extracting transaction data from the Braintree API using dlt.

---
*dlt REST API Source Configuration Guide*