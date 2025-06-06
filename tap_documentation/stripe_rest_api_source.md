# Stripe REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Stripe REST API using the `tap-stripe` connector. The configuration is structured to ensure seamless data extraction from Stripe's API endpoints.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.stripe.com/v1/`
- **Version**: The API version is set to `2022-11-15` to ensure consistent access.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {token}`
- **Token**: The `client_secret` from Stripe is used as the API key.

## 2. Available Endpoints

The following endpoints are available for data extraction. Each endpoint corresponds to a specific Stripe resource.

### Endpoint: Charges
- **Path**: `/charges`
- **Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Limit**: 100
- **Incremental**:
  - **Cursor Path**: `created`
  - **Parameter Name**: `created`
  - **Format**: Unix timestamp

### Endpoint: Customers
- **Path**: `/customers`
- **Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Limit**: 100
- **Incremental**:
  - **Cursor Path**: `created`
  - **Parameter Name**: `created`
  - **Format**: Unix timestamp

### Endpoint: Invoices
- **Path**: `/invoices`
- **Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Limit**: 100
- **Incremental**:
  - **Cursor Path**: `created`
  - **Parameter Name**: `created`
  - **Format**: Unix timestamp

## 3. Incremental Data Loading

Incremental data loading is supported through the `created` parameter, which allows fetching records created after a specific timestamp. The expected format for timestamps is Unix time.

## 4. Endpoint Dependencies

### Resource Relationships
- **Invoices** depend on **Invoice Line Items**.
- **Subscriptions** depend on **Subscription Items**.
- **Payouts** depend on **Payout Transactions**.

### Mapping Identifiers
- **Invoice Line Items** are linked to **Invoices** via the `invoice` field.
- **Subscription Items** are linked to **Subscriptions** via the `subscription` field.
- **Payout Transactions** are linked to **Payouts** via the `payout_id` field.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: The API allows a certain number of requests per second/minute/hour. The exact limits are managed by Stripe and can be found in their documentation.
- **Retries**: The client is configured to retry requests up to 15 times in case of network errors.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: JSON
- **Error Handling**: The client handles `RateLimitError` with exponential backoff.
- **Data Type Specifications**: Ensure all date fields are handled as Unix timestamps.

## Output Format

The configuration is structured in Python as follows:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.stripe.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "charges",
        "path": "/charges",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "starting_after",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "created",
            "param_name": "created",
            "format": "unix"
        }
    },
    {
        "name": "customers",
        "path": "/customers",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "starting_after",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "created",
            "param_name": "created",
            "format": "unix"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "invoice_line_items",
        "depends_on": "invoices",
        "param_mapping": {"invoice": "id"}
    },
    {
        "endpoint": "subscription_items",
        "depends_on": "subscriptions",
        "param_mapping": {"subscription": "id"}
    },
    {
        "endpoint": "payout_transactions",
        "depends_on": "payouts",
        "param_mapping": {"payout_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Stripe API using the dlt data pipeline.

---
*dlt REST API Source Configuration Guide*