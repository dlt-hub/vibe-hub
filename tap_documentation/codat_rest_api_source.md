# Codat REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source using the `tap-codat` from the `singer-io` organization. The configuration is structured to facilitate seamless data extraction from the Codat API into a data pipeline.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.codat.io`
- **UAT URL**: `https://api-uat.codat.io`
- **Version Path**: Not specified, but endpoints are structured with `/companies/{companyId}/...`

### Authentication
- **Type**: `basic`
- **Header**: `Authorization`
- **Format**: `Basic {base64_encoded_api_key}`
- **API Key**: Provided in the configuration file under `api_key`

## 2. Available Endpoints

### Endpoint Details

#### Companies
- **Path**: `/companies`
- **Method**: `GET`
- **Response Data Structure**: JSON array under `results`

#### Accounts
- **Path**: `/companies/{companyId}/data/accounts`
- **Method**: `GET`
- **Response Data Structure**: JSON array under `results`
- **Primary Key**: `id`, `companyId`

#### Bank Accounts
- **Path**: `/companies/{companyId}/connections/{connectionId}/data/bankAccounts`
- **Method**: `GET`
- **Response Data Structure**: JSON array under `results`
- **Primary Key**: `accountName`, `companyId`, `connectionId`

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `pageSize`, `page`
- **Default Page Size**: 500
- **Determination**: Continue fetching until the number of records is less than `PAGE_SIZE`

### Data Extraction
- **JSONPath**: Typically `results` or `data` for collection-based responses
- **Primary Key Fields**: Varies by endpoint, e.g., `id`, `companyId`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `modifiedDate` or `eventTimeUtc` for events
- **Date Field**: `modifiedDate`, `eventTimeUtc`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Configured via `start_date` in the configuration file

## 4. Endpoint Dependencies

### Resource Relationships
- **Bank Account Transactions** depend on **Bank Accounts**
- **Bank Statement Lines** depend on **Bank Statements**
- **Mapping**: Use `companyId` and specific identifiers like `bankAccountId`

## 5. API Behavior & Limits

### Rate Limiting
- **Handling**: Implemented with exponential backoff using `backoff` library
- **Retry on**: 429, 500, 501, 502, 503 status codes

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration
- **Error Handling**: Logs warnings for 404 and 409 status codes
- **Data Type Specifications**: Ensure date-time fields are transformed to RFC3339 format

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.codat.io",
    "auth": {
        "type": "basic",
        "name": "Authorization",
        "format": "Basic {base64_encoded_api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "companies",
        "path": "/companies",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageSize",
            "offset_param": "page",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "modifiedDate",
            "param_name": "modifiedDate",
            "format": "iso"
        }
    },
    {
        "name": "accounts",
        "path": "/companies/{companyId}/data/accounts",
        "method": "GET",
        "data_selector": "results",
        "primary_key": ["id", "companyId"],
        "pagination": {
            "type": "page_number",
            "limit_param": "pageSize",
            "offset_param": "page",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "modifiedDate",
            "param_name": "modifiedDate",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "bank_account_transactions",
        "depends_on": "bank_accounts",
        "param_mapping": {"bankAccountId": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Codat API, ensuring efficient data extraction and management.

---
*dlt REST API Source Configuration Guide*