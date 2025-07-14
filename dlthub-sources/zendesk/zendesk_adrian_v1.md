# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Zendesk Chat API using REST API integration. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://www.zopim.com/api/v2/`
- Alternative Base URL: `https://{subdomain}.zendesk.com/api/v2/chat/` (if subdomain is provided and accessible)

**Authentication:**
- Type: `bearer`
- Header Name: `Authorization`
- Token Format: `Bearer {access_token}`
- Required Configuration: `access_token`, `subdomain` (optional)

## 2. Available Endpoints

### Account Endpoint
- **Path:** `/account`
- **HTTP Method:** GET
- **Response Data Structure:** JSON object with account details
- **Pagination:** Not applicable

### Agents Endpoint
- **Path:** `/agents`
- **HTTP Method:** GET
- **Query Parameters:** `since_id`, `limit`
- **Response Data Structure:** JSON array of agent objects
- **Pagination Configuration:**
  - Type: `offset`
  - Limit Parameter: `limit`
  - Offset Parameter: `since_id`
  - Default Limit: 100

### Bans Endpoint
- **Path:** `/bans`
- **HTTP Method:** GET
- **Query Parameters:** `since_id`, `limit`
- **Response Data Structure:** JSON array of ban objects
- **Pagination Configuration:**
  - Type: `offset`
  - Limit Parameter: `limit`
  - Offset Parameter: `since_id`
  - Default Limit: 100

### Chats Endpoint
- **Path:** `/chats`
- **HTTP Method:** GET
- **Query Parameters:** `q`, `ids`
- **Response Data Structure:** JSON array of chat objects
- **Pagination Configuration:**
  - Type: `json_link`
  - Next Page Determined by: `next_url` in response

## 3. Incremental Data Loading

### Agents Endpoint
- **Incremental Support:** Yes
- **Query Parameter:** `since_id`
- **Tracking Field:** `id`
- **Initial Value:** 0

### Chats Endpoint
- **Incremental Support:** Yes
- **Query Parameter:** `q` with date range
- **Tracking Field:** `end_timestamp` or `timestamp`
- **Date Format:** ISO 8601
- **Initial Value:** Configured `start_date`

## 4. Endpoint Dependencies

### Resource Relationships
- **Chats Endpoint:** Requires fetching chat details using IDs from search results.
- **Mapping:** Use `id` from search results to fetch detailed chat data.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second:** Subject to API's rate limits
- **Burst Limits:** Handle 429 status with exponential backoff

### Special Requirements
- **Custom Headers:** `User-Agent` (default: `tap-zendesk-chat`)
- **Error Handling:** Retry on 429 and 502 status codes
- **Data Type Specifications:** Ensure correct handling of date and ID fields

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://www.zopim.com/api/v2/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "agents",
        "path": "/agents",
        "method": "GET",
        "data_selector": "agents",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "since_id",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "id",
            "param_name": "since_id",
            "format": "integer"
        }
    },
    {
        "name": "chats",
        "path": "/chats",
        "method": "GET",
        "data_selector": "chats",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_page_path": "next_url"
        },
        "incremental": {
            "cursor_path": "end_timestamp",
            "param_name": "q",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "chats",
        "depends_on": "search_results",
        "param_mapping": {"id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for extracting data from the Zendesk Chat API. Adjust the configuration as needed based on specific requirements or API changes.