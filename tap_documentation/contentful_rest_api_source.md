# Contentful REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Contentful REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://cdn.contentful.com/spaces/{space_id}/environments/master`
- Replace `{space_id}` with the actual space ID for your Contentful account.

**Authentication:**
- **Type:** `api_key`
- **Location:** `params`
- **Parameter Name:** `access_token`
- **Format:** The API key is passed as a query parameter in the URL.

## 2. Available Endpoints

### Entries Endpoint

**Endpoint Details:**
- **Path:** `/entries`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `order` (for sorting by `updated_at`)

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:** `skip` (offset), `limit` (page size)
- **Default/Maximum Page Sizes:** Default is 100
- **Next Page Determination:** Calculated using the `skip` and `limit` parameters. The paginator checks if `skip + limit < total` to determine if more pages are available.

**Data Extraction:**
- **JSONPath:** `$.items[*]`
- **Primary Key:** `id` (extracted from `sys.id`)
- **Key Fields:** `id`, `updated_at`

### Content Types Endpoint

**Endpoint Details:**
- **Path:** `/content_types`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:** `skip` (offset), `limit` (page size)
- **Default/Maximum Page Sizes:** Default is 100
- **Next Page Determination:** Similar to the Entries endpoint.

**Data Extraction:**
- **JSONPath:** `$.items[*]`
- **Primary Key:** `id` (extracted from `sys.id`)
- **Key Fields:** `id`, `updated_at`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `order`
- **Date/Timestamp Field:** `sys.updatedAt`
- **Expected Format:** ISO 8601 date format
- **Recommended Initial Values:** Use the earliest possible date or a specific starting point for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `entries` endpoint can be dependent on the `content_types` endpoint if specific content type information is needed for processing entries.
- **Mapping Identifiers:** Use `sys.contentType.sys.id` from entries to match with `id` in content types.

## 5. API Behavior & Limits

**Rate Limiting:**
- Contentful API has rate limits, typically allowing a certain number of requests per second/minute. Check the Contentful documentation for specific limits.

**Special Requirements:**
- **Custom Headers:** Optionally set a `User-Agent` header if specified in the configuration.
- **Response Format:** JSON
- **Error Handling:** Implement retry logic for handling rate limit errors and other transient issues.
- **Data Type Specifications:** Ensure data types conform to the JSON schema defined in the streams.

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://cdn.contentful.com/spaces/{space_id}/environments/master",
    "auth": {
        "type": "api_key",
        "location": "params",
        "name": "access_token",
        "format": "{token}"
    }
}

ENDPOINTS = [
    {
        "name": "entries",
        "path": "/entries",
        "method": "GET",
        "data_selector": "$.items[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "skip",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "sys.updatedAt",
            "param_name": "order",
            "format": "iso"
        }
    },
    {
        "name": "content_types",
        "path": "/content_types",
        "method": "GET",
        "data_selector": "$.items[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "skip",
            "limit": 100
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "entries",
        "depends_on": "content_types",
        "param_mapping": {"content_type_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Contentful API using a dlt data pipeline. Adjust the configuration as needed based on specific project requirements and API changes.

---
*dlt REST API Source Configuration Guide*