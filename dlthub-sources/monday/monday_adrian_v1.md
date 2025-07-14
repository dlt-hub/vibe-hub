# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Monday.com API using the `tap-monday` connector. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.monday.com/v2`
- This is the base URL for all API requests.

**Authentication:**
- **Type:** `api_key`
- **Location:** `header`
- **Parameter Name:** `Authorization`
- **Format:** `Bearer {token}`
- The API requires an authentication token to be included in the headers of each request.

## 2. Available Endpoints

### Workspaces Endpoint

**Endpoint Details:**
- **Path:** `/workspaces`
- **HTTP Method:** `POST` (GraphQL query)
- **Query Parameters:** None required
- **Response Data Structure:** JSON object containing workspace details

**Pagination Configuration:**
- **Type:** `single_page`
- This endpoint does not support pagination as it retrieves all workspaces in a single request.

**Data Extraction:**
- **JSONPath:** `$.data.boards[*].workspace`
- **Primary Key:** `id`

### Boards Endpoint

**Endpoint Details:**
- **Path:** `/boards`
- **HTTP Method:** `POST` (GraphQL query)
- **Query Parameters:** `page`, `board_limit`
- **Response Data Structure:** JSON object containing board details

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `page`, `board_limit`
- **Default Page Size:** 10 (configurable via `board_limit`)
- **Next Page Determination:** If the number of boards returned equals `board_limit`, increment the page number.

**Data Extraction:**
- **JSONPath:** `$.data.boards[*]`
- **Primary Key:** `id`

### Board Views Endpoint

**Endpoint Details:**
- **Path:** `/board_views`
- **HTTP Method:** `POST` (GraphQL query)
- **Query Parameters:** `board_id`
- **Response Data Structure:** JSON object containing board view details

**Pagination Configuration:**
- **Type:** `single_page`
- This endpoint retrieves all views for a given board in a single request.

**Data Extraction:**
- **JSONPath:** `$.data.boards[0].views[*]`
- **Primary Key:** `id`

## 3. Incremental Data Loading

### Boards Stream

**Incremental Support:**
- **Query Parameter:** `updated_at`
- **Date Field:** `updated_at`
- **Format:** ISO 8601
- **Initial Value:** Use the earliest possible date to ensure all records are fetched initially.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Board Views:** Depends on `Boards` endpoint to provide `board_id`.
- **Groups and Columns:** Also depend on `Boards` endpoint for `board_id`.

**Processing Order:**
1. Fetch `Boards`
2. Fetch `Board Views`, `Groups`, and `Columns` using `board_id` from `Boards`

## 5. API Behavior & Limits

**Rate Limiting:**
- The API does not explicitly document rate limits, but it is recommended to implement exponential backoff for retries.

**Special Requirements:**
- **Headers:** Must include `Content-Type: application/json` and `Authorization: Bearer {token}`
- **Error Handling:** Implement retries for 5xx server errors and handle 4xx client errors gracefully.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.monday.com/v2",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "workspaces",
        "path": "/workspaces",
        "method": "POST",
        "data_selector": "$.data.boards[*].workspace",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "boards",
        "path": "/boards",
        "method": "POST",
        "data_selector": "$.data.boards[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "board_limit",
            "offset_param": "page",
            "limit": 10
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "board_views",
        "path": "/board_views",
        "method": "POST",
        "data_selector": "$.data.boards[0].views[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "board_views",
        "depends_on": "boards",
        "param_mapping": {"board_id": "id"}
    },
    {
        "endpoint": "groups",
        "depends_on": "boards",
        "param_mapping": {"board_id": "id"}
    },
    {
        "endpoint": "columns",
        "depends_on": "boards",
        "param_mapping": {"board_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for Monday.com API, ensuring efficient data extraction and handling of dependencies between different API resources.