# Mambu REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide to configuring a dlt REST API source for the Mambu API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://{subdomain}.mambu.com/api/`
- Note: Replace `{subdomain}` with your specific Mambu subdomain.

**Authentication:**
- Type: `basic` or `api_key`
- For Basic Authentication:
  - Username: Provided in the configuration
  - Password: Provided in the configuration
- For API Key Authentication:
  - Name: `apikey`
  - Location: Header
  - Parameter Name: `apikey`
- For Audit API Key:
  - Name: `apikey_audit`
  - Location: Header
  - Parameter Name: `apikey`

## 2. Available Endpoints

### Endpoint: `branches`
- **Path:** `/branches`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Response Data Structure:** JSON object with branch details
- **Pagination Configuration:**
  - Type: `offset`
  - Limit Parameter: `limit`
  - Offset Parameter: `offset`
  - Default/Maximum Page Size: 200

### Endpoint: `clients`
- **Path:** `/clients:search`
- **HTTP Method:** `POST`
- **Required Query Parameters:** None
- **Response Data Structure:** JSON object with client details
- **Pagination Configuration:**
  - Type: `offset`
  - Limit Parameter: `limit`
  - Offset Parameter: `offset`
  - Default/Maximum Page Size: 200

### Endpoint: `loan_accounts`
- **Path:** `/loans:search`
- **HTTP Method:** `POST`
- **Required Query Parameters:** None
- **Response Data Structure:** JSON object with loan account details
- **Pagination Configuration:**
  - Type: `offset`
  - Limit Parameter: `limit`
  - Offset Parameter: `offset`
  - Default/Maximum Page Size: 200

## 3. Incremental Data Loading

### Endpoint: `branches`
- **Incremental Support:** Yes
- **Query Parameter for Incremental Loading:** `lastModifiedDate`
- **Date/Timestamp Field:** `lastModifiedDate`
- **Expected Format:** ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value:** Use the earliest date of interest or `null` for full sync.

### Endpoint: `clients`
- **Incremental Support:** Yes
- **Query Parameter for Incremental Loading:** `lastModifiedDate`
- **Date/Timestamp Field:** `lastModifiedDate`
- **Expected Format:** ISO 8601
- **Recommended Initial Value:** Use the earliest date of interest or `null` for full sync.

## 4. Endpoint Dependencies

### Endpoint: `loan_repayments`
- **Depends On:** `loan_accounts`
- **Parameter Mapping:** `loan_id` from `loan_accounts` to `loan_repayments`
- **Required Processing Order:** Fetch `loan_accounts` first, then `loan_repayments`.

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests per second: Not explicitly documented, but recommended to handle with exponential backoff on 429 errors.
- Burst Limits: Handle with backoff strategies.

**Special Requirements:**
- Required Custom Headers: `User-Agent` must be set to `MambuTap-{user_agent}`.
- Response Format Considerations: JSON
- Error Handling Patterns: Implement retries with exponential backoff for 5xx errors and 429 rate limit errors.
- Data Type Specifications: Ensure date fields are handled in UTC and converted as necessary.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{subdomain}.mambu.com/api/",
    "auth": {
        "type": "basic",
        "username": "{username}",
        "password": "{password}"
    }
}

ENDPOINTS = [
    {
        "name": "branches",
        "path": "/branches",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "lastModifiedDate",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "clients",
        "path": "/clients:search",
        "method": "POST",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "lastModifiedDate",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "loan_repayments",
        "depends_on": "loan_accounts",
        "param_mapping": {"loan_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Mambu API using dlt, ensuring comprehensive data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*