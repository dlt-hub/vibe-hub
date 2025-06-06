# Invoiced REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Invoiced REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.invoiced.com/`

**Authentication:**
- **Type:** `api_key`
- **Location:** `header`
- **Name:** `Authorization`
- **Format:** `Bearer {token}`

## 2. Available Endpoints

### Endpoint: Credit Notes

**Endpoint Details:**
- **Path:** `/credit_notes`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `per_page`, `page`, `include`, `sort`, `updated_after`
- **Response Data Structure:** JSON array of credit note objects

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `per_page`, `page`
- **Default Page Size:** 100
- **Maximum Page Size:** 100
- **Next Page Determination:** Presence of `"next"` in `listMetadata.links`

**Data Extraction:**
- **JSONPath:** Root level array
- **Primary Key:** `id`
- **Key Fields:** `id`, `updated_at`

### Endpoint: Customers

**Endpoint Details:**
- **Path:** `/customers`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `per_page`, `page`, `include`, `sort`, `updated_after`
- **Response Data Structure:** JSON array of customer objects

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `per_page`, `page`
- **Default Page Size:** 100
- **Maximum Page Size:** 100
- **Next Page Determination:** Presence of `"next"` in `listMetadata.links`

**Data Extraction:**
- **JSONPath:** Root level array
- **Primary Key:** `id`
- **Key Fields:** `id`, `updated_at`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `updated_after`
- **Date/Timestamp Field:** `updated_at`
- **Expected Format:** Unix timestamps
- **Recommended Initial Value:** Start date as specified in configuration

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Endpoint:** `user_posts`
- **Depends On:** `users`
- **Parameter Mapping:** `user_id` to `id`

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Second:** Not explicitly documented, but typical REST APIs may have limits
- **Burst Limits:** Implement retry logic with exponential backoff

**Special Requirements:**
- **Custom Headers:** None beyond authentication
- **Response Format Considerations:** Ensure JSON parsing
- **Error Handling Patterns:** Implement retries for 5xx errors and handle 4xx errors gracefully
- **Data Type Specifications:** Ensure correct handling of Unix timestamps for `created_at` and `updated_at`

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.invoiced.com/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "credit_notes",
        "path": "/credit_notes",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "unix"
        }
    },
    {
        "name": "customers",
        "path": "/customers",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "unix"
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

This configuration guide provides all necessary parameters and considerations for integrating with the Invoiced API using dlt, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*