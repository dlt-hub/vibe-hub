# Brightpearl REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Brightpearl REST API. It covers all necessary parameters and configurations to effectively extract data from the API.

## 1. Client Configuration

**Base URL:**
- `https://{domain}/public-api/{account_id}/`
- Replace `{domain}` and `{account_id}` with your specific Brightpearl domain and account ID.

**Authentication:**
- **Type:** `api_key`
- **Location:** Header
- **Parameter Name:** `brightpearl-account-token`
- **Additional Header:** `brightpearl-app-ref`

## 2. Available Endpoints

### Example Endpoint: Product Search

**Endpoint Details:**
- **Path:** `/product-service/product-search`
- **HTTP Method:** GET
- **Required Query Parameters:** None
- **Optional Query Parameters:** `firstResult`, `lastResult`, `includeOptional`
- **Response Data Structure:** JSON object with `results` array

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:** `firstResult`, `lastResult`
- **Default Page Size:** 500
- **How Next Page is Determined:** Use `metaData.morePagesAvailable` to check if more pages are available. Increment `firstResult` by the number of results returned.

**Data Extraction:**
- **JSONPath:** `results`
- **Primary Key:** `id` (or other unique identifier depending on the endpoint)
- **Key Fields:** Fields like `id`, `name`, `updatedOn` are typically used to uniquely identify records.

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `updatedOn`
- **Date/Timestamp Field:** `updatedOn`
- **Expected Format:** ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value:** Use the current date minus a buffer period (e.g., 30 days) for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Example:** The `goods_note_out` endpoint depends on the `order-service/order` endpoint.
- **Mapping Identifiers:** Use the `order_id` from the `order-service/order` to fetch related `goods_note_out`.
- **Processing Order:** Fetch data from parent endpoints (e.g., orders) before child endpoints (e.g., goods notes).

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Hour:** Managed via `brightpearl-requests-remaining` header.
- **Burst Limits:** If requests remaining fall below 30, use `brightpearl-next-throttle-period` to delay requests.

**Special Requirements:**
- **Custom Headers:** `brightpearl-account-token`, `brightpearl-app-ref`
- **Response Format Considerations:** Handle JSON responses with potential nested structures.
- **Error Handling Patterns:** Handle `401` for token expiration and `429` for rate limits. Retry with appropriate delays.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{domain}/public-api/{account_id}/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "brightpearl-account-token",
        "additional_headers": {
            "brightpearl-app-ref": "{app_ref}"
        }
    }
}

ENDPOINTS = [
    {
        "name": "product_search",
        "path": "/product-service/product-search",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "firstResult",
            "offset_param": "lastResult",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "updatedOn",
            "param_name": "updatedOn",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "goods_note_out",
        "depends_on": "order-service/order",
        "param_mapping": {"order_id": "id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Brightpearl API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*