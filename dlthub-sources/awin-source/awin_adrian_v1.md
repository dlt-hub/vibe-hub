# dlt REST API Source Configuration Documentation for Awin API

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Awin REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.awin.com`
- This is the root URL for all API requests.

**Authentication:**
- **Type:** `bearer`
- **Token Format:** Bearer token is required for authentication.
- **Parameter Name:** `Authorization`
- **Location:** Header
- The token should be provided in the format `Bearer {token}`.

## 2. Available Endpoints

### Accounts Endpoint

**Endpoint Details:**
- **Path:** `/accounts`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array located at `$.accounts[*]`

**Pagination Configuration:**
- **Type:** `single_page`
- This endpoint does not require pagination as it returns all accounts in a single response.

**Data Extraction:**
- **JSONPath:** `$.accounts[*]`
- **Primary Key:** `accountId`

### Transactions Endpoint

**Endpoint Details:**
- **Path:** `/{account_type}s/{account_id}/transactions/`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `startDate`, `endDate`, `timezone`, `dateType`, `accessToken`
- **Response Data Structure:** JSON array located at `$[*]`

**Pagination Configuration:**
- **Type:** `date_range`
- **Parameter Names:** `startDate`, `endDate`
- **Default/Maximum Page Sizes:** Batch size is determined by `request_batch_size_days` (maximum 31 days).

**Data Extraction:**
- **JSONPath:** `$[*]`
- **Primary Key:** Combination of `id` and `transactionDate`

### Publishers Endpoint

**Endpoint Details:**
- **Path:** `/advertisers/{account_id}/publishers/`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array located at `$[*]`

**Pagination Configuration:**
- **Type:** `single_page`
- This endpoint does not require pagination.

**Data Extraction:**
- **JSONPath:** `$[*]`
- **Primary Key:** `id`

### Report By Publisher Endpoint

**Endpoint Details:**
- **Path:** `/{account_type}s/{account_id}/reports/publisher`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `startDate`, `endDate`, `timezone`, `dateType`, `accessToken`
- **Response Data Structure:** JSON array located at `$[*]`

**Pagination Configuration:**
- **Type:** `date_range`
- **Parameter Names:** `startDate`, `endDate`
- **Default/Maximum Page Sizes:** 1 day per request

**Data Extraction:**
- **JSONPath:** `$[*]`
- **Primary Key:** Combination of `advertiserId`, `publisherId`, and `transactionDate`

## 3. Incremental Data Loading

**Incremental Support:**
- **Transactions and Report By Publisher Endpoints:**
  - **Query Parameter:** `startDate`, `endDate`
  - **Date/Timestamp Field:** `transactionDate`
  - **Expected Format:** ISO 8601 (e.g., `2023-01-01T00:00:00`)
  - **Recommended Initial Values:** Use `start_date` from configuration, defaulting to `2016-01-01T00:00:00Z`.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Transactions and Publishers Endpoints:**
  - Depend on `Accounts` endpoint to provide `account_id` and `account_type`.
  - **Mapping:** `account_id` and `account_type` are used to construct the path for dependent endpoints.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API documentation does not specify rate limits, but it is recommended to implement backoff strategies for handling retries.

**Special Requirements:**
- **Headers:** Custom `User-Agent` header can be set via configuration.
- **Response Format:** JSON
- **Error Handling:** Implement retries for 5xx server errors and handle 4xx client errors as fatal.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.awin.com",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/accounts",
        "method": "GET",
        "data_selector": "$.accounts[*]",
        "primary_key": "accountId",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "transactions",
        "path": "/{account_type}s/{account_id}/transactions/",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": ["id", "transactionDate"],
        "pagination": {
            "type": "date_range",
            "start_date_param": "startDate",
            "end_date_param": "endDate",
            "batch_size_days": 31
        },
        "incremental": {
            "cursor_path": "transactionDate",
            "param_name": "startDate",
            "format": "iso"
        }
    },
    {
        "name": "publishers",
        "path": "/advertisers/{account_id}/publishers/",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "report_by_publisher",
        "path": "/{account_type}s/{account_id}/reports/publisher",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": ["advertiserId", "publisherId", "transactionDate"],
        "pagination": {
            "type": "date_range",
            "start_date_param": "startDate",
            "end_date_param": "endDate",
            "batch_size_days": 1
        },
        "incremental": {
            "cursor_path": "transactionDate",
            "param_name": "startDate",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "transactions",
        "depends_on": "accounts",
        "param_mapping": {"account_id": "accountId", "account_type": "accountType"}
    },
    {
        "endpoint": "publishers",
        "depends_on": "accounts",
        "param_mapping": {"account_id": "accountId"}
    },
    {
        "endpoint": "report_by_publisher",
        "depends_on": "accounts",
        "param_mapping": {"account_id": "accountId", "account_type": "accountType"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the Awin API using a dlt data pipeline.