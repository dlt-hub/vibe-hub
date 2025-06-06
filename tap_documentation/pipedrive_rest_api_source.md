# Pipedrive REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Pipedrive

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Pipedrive REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.pipedrive.com/v1`
- This is the base URL for all API requests.

**Authentication:**
- **Type:** `api_key`
- **Location:** `query`
- **Parameter Name:** `api_token`
- The API key should be included as a query parameter in each request.

## 2. Available Endpoints

### Users Endpoint

**Endpoint Details:**
- **Path:** `/users`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Response Data Structure:** JSON object with a `data` array containing user details.

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:** `start`, `limit`
- **Default/Maximum Page Sizes:** 100
- **Next Page Determination:** Use the `start` parameter to specify the starting point for the next page.

**Data Extraction:**
- **JSONPath:** `data`
- **Primary Key:** `id`
- **Key Fields:** `id`, `name`, `email`

### Deals Endpoint

**Endpoint Details:**
- **Path:** `/deals`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Response Data Structure:** JSON object with a `data` array containing deal details.

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:** `start`, `limit`
- **Default/Maximum Page Sizes:** 100
- **Next Page Determination:** Use the `start` parameter to specify the starting point for the next page.

**Data Extraction:**
- **JSONPath:** `data`
- **Primary Key:** `id`
- **Key Fields:** `id`, `title`, `value`

## 3. Incremental Data Loading

**Incremental Support:**
- **Deals Endpoint:**
  - **Query Parameter:** `since`
  - **Date/Timestamp Field:** `update_time`
  - **Expected Format:** ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
  - **Recommended Initial Value:** The current date minus a reasonable buffer period (e.g., 30 days).

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Deals and Deal Products:**
  - **Dependency:** Deal Products depend on Deals.
  - **Mapping:** Use `deal_id` from the Deals endpoint to fetch related products.
  - **Processing Order:** Fetch Deals first, then Deal Products using the `deal_id`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Second:** Not explicitly defined; monitor `X-RateLimit-Remaining` and `X-RateLimit-Reset` headers.
- **Burst Limits:** Handle 429 status codes by implementing exponential backoff.

**Special Requirements:**
- **Custom Headers:** `User-Agent` should be set to `tap-pipedrive (+support@stitchdata.com)`
- **Response Format Considerations:** Ensure JSON responses are parsed correctly.
- **Error Handling Patterns:** Implement retries for 5xx errors and handle specific error codes as defined in the Pipedrive API documentation.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.pipedrive.com/v1",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "api_token"
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
            "offset_param": "start",
            "limit": 100
        }
    },
    {
        "name": "deals",
        "path": "/deals",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "start",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "update_time",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "deal_products",
        "depends_on": "deals",
        "param_mapping": {"deal_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline to extract data from the Pipedrive API effectively. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*