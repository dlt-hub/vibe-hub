# Quickbooks REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for QuickBooks

This document provides a comprehensive guide to configuring a dlt data pipeline for integrating with the QuickBooks REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://quickbooks.api.intuit.com/v3/company/{realmId}/`
- Note: Replace `{realmId}` with your specific company ID.

**Authentication:**
- Type: `oauth2`
- Token URL: `https://oauth.platform.intuit.com/oauth2/v1/tokens/bearer`
- Required Scopes: `com.intuit.quickbooks.accounting`
- Headers:
  - `Authorization`: `Bearer {access_token}`
  - `Content-Type`: `application/json`

## 2. Available Endpoints

### Users Endpoint

**Endpoint Details:**
- Path: `/users`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `limit`, `offset`
- Response Data Structure: JSON object with a `data` array

**Pagination Configuration:**
- Type: `offset`
- Limit Parameter: `limit`
- Offset Parameter: `offset`
- Default Limit: 100
- Maximum Limit: 1000

**Data Extraction:**
- JSONPath: `$.data`
- Primary Key: `id`

### Transactions Endpoint

**Endpoint Details:**
- Path: `/transactions`
- HTTP Method: `GET`
- Required Query Parameters: `start_date`, `end_date`
- Optional Query Parameters: `limit`, `offset`
- Response Data Structure: JSON object with a `transactions` array

**Pagination Configuration:**
- Type: `offset`
- Limit Parameter: `limit`
- Offset Parameter: `offset`
- Default Limit: 100
- Maximum Limit: 1000

**Data Extraction:**
- JSONPath: `$.transactions`
- Primary Key: `transaction_id`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `since`
- Date/Timestamp Field: `updated_at`
- Expected Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Value: `1970-01-01T00:00:00Z`

## 4. Endpoint Dependencies

**Resource Relationships:**
- `user_posts` depends on `users`
- Mapping: `user_id` in `user_posts` corresponds to `id` in `users`
- Required Processing Order: Fetch `users` before `user_posts`

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests per Minute: 60
- Burst Limit: 10 requests per second
- Recommended Delay: 1 second between requests

**Special Requirements:**
- Custom Headers: None required beyond standard authentication
- Response Format: JSON
- Error Handling: Retry on 429 status code with exponential backoff
- Data Type Specifications: Ensure date fields are parsed as ISO 8601 strings

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://quickbooks.api.intuit.com/v3/company/{realmId}/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://oauth.platform.intuit.com/oauth2/v1/tokens/bearer",
        "scopes": ["com.intuit.quickbooks.accounting"],
        "headers": {
            "Authorization": "Bearer {access_token}",
            "Content-Type": "application/json"
        }
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "transactions",
        "path": "/transactions",
        "method": "GET",
        "data_selector": "transactions",
        "primary_key": "transaction_id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "user_posts", 
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and settings to effectively integrate and extract data from the QuickBooks REST API using a dlt data pipeline. Adjust the parameters as needed to fit your specific use case and data requirements.

---
*dlt REST API Source Configuration Guide*