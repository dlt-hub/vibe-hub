# Thinkific REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Thinkific REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.thinkific.com/api/public/v1`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `X-Auth-API-Key`
- **Additional Header**: `X-Auth-Subdomain` (required for subdomain identification)
- **User-Agent**: `Meltano Tap for Thinkific: https://hub.meltano.com/extractors/tap-thinkific/`

## 2. Available Endpoints

### Courses Endpoint
- **Path**: `/courses`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Data Selector**: `$.items[*]`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameter Name**: `page`
  - **Next Page Token JSONPath**: `$.meta.pagination.next_page`

### Enrollments Endpoint
- **Path**: `/enrollments`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Data Selector**: `$.items[*]`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameter Name**: `page`
  - **Next Page Token JSONPath**: `$.meta.pagination.next_page`

## 3. Incremental Data Loading

### Enrollments Stream
- **Incremental Support**: Yes
- **Cursor Path**: `updated_at`
- **Parameter Name**: `query[updated_after]`
- **Date Format**: ISO 8601 (`%Y-%m-%dT%H:%M:%SZ`)
- **Recommended Initial Value**: Use the `start_date` configuration parameter to set the initial sync date.

## 4. Endpoint Dependencies

- **Enrollments Endpoint**: No direct dependencies on other endpoints.
- **Courses Endpoint**: No direct dependencies on other endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Handling**: Implement exponential backoff with a maximum of 16 retries and a factor of 10 for delays.
- **Response Code for Rate Limiting**: `429` (Too Many Requests)

### Special Requirements
- **Custom Headers**: Ensure `X-Auth-API-Key` and `X-Auth-Subdomain` are included in all requests.
- **Error Handling**: Use `RetriableAPIError` for handling rate limit responses and other retriable errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.thinkific.com/api/public/v1",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "X-Auth-API-Key",
        "additional_headers": {
            "X-Auth-Subdomain": "{subdomain}",
            "User-Agent": "Meltano Tap for Thinkific: https://hub.meltano.com/extractors/tap-thinkific/"
        }
    }
}

ENDPOINTS = [
    {
        "name": "courses",
        "path": "/courses",
        "method": "GET",
        "data_selector": "$.items[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "next_page_token_jsonpath": "$.meta.pagination.next_page"
        }
    },
    {
        "name": "enrollments",
        "path": "/enrollments",
        "method": "GET",
        "data_selector": "$.items[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "next_page_token_jsonpath": "$.meta.pagination.next_page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "query[updated_after]",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the Thinkific API using a dlt data pipeline. Ensure all configurations are correctly applied to facilitate seamless data extraction and loading.

---
*dlt REST API Source Configuration Guide*