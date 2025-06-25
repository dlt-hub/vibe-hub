# dlt REST API Source Configuration Documentation for Chargebee

This document provides a comprehensive guide to configuring a dlt data pipeline for the Chargebee REST API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- The base URL for the Chargebee API is constructed dynamically based on the `site` or `full_site` configuration parameter. The typical format is: `https://{site}.chargebee.com/api/v2/`.

**Authentication:**
- **Type:** `basic`
- **Details:** The API requires basic authentication using an API key. The API key is passed as the username, with the password left empty.
- **Header:** The API key is included in the `Authorization` header in the format `Basic {base64_encoded_api_key:}`.

## 2. Available Endpoints

The Chargebee API provides multiple endpoints, each corresponding to a specific data stream. Below are the details for each endpoint:

### Example Endpoint: Customers

**Endpoint Details:**
- **Path:** `/customers`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `updated_at[after]`, `updated_at[before]`, `sort_by[asc]`
- **Response Data Structure:** JSON object with a `list` key containing customer records.

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:** `limit`, `offset`
- **Default/Maximum Page Sizes:** Default is 100 records per page.
- **Next Page Determination:** The `next_offset` parameter in the response indicates the next page.

**Data Extraction:**
- **JSONPath:** `list`
- **Primary Key:** `id`
- **Key Fields:** `id`, `updated_at`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `updated_at[after]`
- **Date Field:** `updated_at`
- **Expected Format:** Unix timestamps
- **Recommended Initial Values:** Use the `start_date` from the configuration or the last recorded `updated_at` value from the state.

## 4. Endpoint Dependencies

**Resource Relationships:**
- Some endpoints, like `usages`, depend on data from the `subscriptions` endpoint.
- **Mapping Identifiers:** The `subscription_id` from `subscriptions` is used to fetch related `usages`.
- **Processing Order:** Fetch `subscriptions` first, then use their IDs to fetch `usages`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** 100 requests per minute.
- **Burst Limits:** If a 429 status code is received, respect the `Retry-After` header or default to a 60-second delay.

**Special Requirements:**
- **Custom Headers:** `User-Agent` can be customized via configuration.
- **Response Format:** JSON
- **Error Handling:** Retry on 4xx and 429 errors using exponential backoff.

## Output Format

Below is an example configuration for the Chargebee API using dlt:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{site}.chargebee.com/api/v2/",
    "auth": {
        "type": "basic",
        "username": "{api_key}",
        "password": ""
    }
}

ENDPOINTS = [
    {
        "name": "customers",
        "path": "/customers",
        "method": "GET",
        "data_selector": "list",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at[after]",
            "format": "unix"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "usages",
        "depends_on": "subscriptions",
        "param_mapping": {"subscription_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for extracting data from the Chargebee API efficiently.