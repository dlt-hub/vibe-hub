# Activecampaign REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for ActiveCampaign

This document provides a comprehensive guide to configuring a dlt REST API source for ActiveCampaign. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://<your-domain>.api-us1.com/api/3/`
- Ensure to replace `<your-domain>` with your specific ActiveCampaign domain.

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `Api-Token`
- Format: `Api-Token: {api_token}`

## 2. Available Endpoints

Below are some of the key endpoints available in the ActiveCampaign API:

### Accounts Endpoint
**Endpoint Details:**
- Path: `/accounts`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `limit`, `offset`
- Response Data Structure: JSON object with a key `accounts` containing an array of account objects.

**Pagination Configuration:**
- Type: `offset`
- Limit Parameter: `limit`
- Offset Parameter: `offset`
- Default/Maximum Page Size: 100

**Data Extraction:**
- JSONPath: `accounts`
- Primary Key: `id`

### Contacts Endpoint
**Endpoint Details:**
- Path: `/contacts`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `limit`, `offset`, `filters[updated_after]`
- Response Data Structure: JSON object with a key `contacts` containing an array of contact objects.

**Pagination Configuration:**
- Type: `offset`
- Limit Parameter: `limit`
- Offset Parameter: `offset`
- Default/Maximum Page Size: 100

**Data Extraction:**
- JSONPath: `contacts`
- Primary Key: `id`

## 3. Incremental Data Loading

### Accounts Endpoint
**Incremental Support:**
- Query Parameter: `updated_after`
- Date Field: `updated_timestamp`
- Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Value: The date from which you want to start syncing data.

### Contacts Endpoint
**Incremental Support:**
- Query Parameter: `filters[updated_after]`
- Date Field: `udate`
- Format: ISO 8601
- Recommended Initial Value: The date from which you want to start syncing data.

## 4. Endpoint Dependencies

### Ecommerce Orders and Products
- **Parent Endpoint:** `ecommerce_orders`
- **Child Endpoint:** `ecommerce_order_products`
- **Dependency Mapping:** `order_id` from `ecommerce_orders` is used to fetch products for each order.
- **Processing Order:** Fetch orders first, then fetch products for each order using the `order_id`.

## 5. API Behavior & Limits

**Rate Limiting:**
- Maximum Requests: 5 requests per second
- Burst Limits: Exponential backoff is recommended for handling rate limits.

**Special Requirements:**
- Custom Headers: `User-Agent` header is recommended for identifying your application.
- Response Format: JSON
- Error Handling: Implement retries with exponential backoff for 5xx and 429 status codes.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://<your-domain>.api-us1.com/api/3/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Api-Token",
        "format": "{api_token}"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/accounts",
        "method": "GET",
        "data_selector": "accounts",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_timestamp",
            "param_name": "updated_after",
            "format": "iso"
        }
    },
    {
        "name": "contacts",
        "path": "/contacts",
        "method": "GET",
        "data_selector": "contacts",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "udate",
            "param_name": "filters[updated_after]",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "ecommerce_order_products",
        "depends_on": "ecommerce_orders",
        "param_mapping": {"order_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for ActiveCampaign, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*