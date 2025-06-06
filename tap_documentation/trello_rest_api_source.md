# Trello REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Trello

This document provides a comprehensive guide to configuring a dlt data pipeline for extracting data from the Trello REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.trello.com/1/`

**Authentication:**
- **Type:** `api_key`
- **Parameters:**
  - `developer_api_key`: Required, provided in the header or query parameter.
  - `access_token`: Required, provided in the header or query parameter.
- **Format:** `key={developer_api_key}&token={access_token}`

## 2. Available Endpoints

### Endpoint: Members
- **Path:** `/members/me`
- **Method:** `GET`
- **Response Data Structure:** JSON object with member details.
- **Data Extraction:** JSONPath `$.id` for member ID.

### Endpoint: Boards
- **Path:** `/members/{id}/boards`
- **Method:** `GET`
- **Response Data Structure:** JSON array of board objects.
- **Data Extraction:** JSONPath `$.[]` for board objects.
- **Primary Key:** `id`

### Endpoint: Actions
- **Path:** `/boards/{idBoard}/actions`
- **Method:** `GET`
- **Response Data Structure:** JSON array of action objects.
- **Data Extraction:** JSONPath `$.[]` for action objects.
- **Primary Key:** `id`
- **Pagination:** Incremental loading using `date` field.

### Endpoint: Cards
- **Path:** `/boards/{idBoard}/cards/all`
- **Method:** `GET`
- **Response Data Structure:** JSON array of card objects.
- **Data Extraction:** JSONPath `$.[]` for card objects.
- **Primary Key:** `id`

### Endpoint: Checklists
- **Path:** `/boards/{idBoard}/checklists`
- **Method:** `GET`
- **Response Data Structure:** JSON array of checklist objects.
- **Data Extraction:** JSONPath `$.[]` for checklist objects.
- **Primary Key:** `id`

### Pagination Configuration
- **Type:** `offset`
- **Parameters:**
  - `limit`: Default is 1000.
  - `before`: Used for pagination with `id` as cursor.
- **Next Page Determination:** Use the `id` of the last record as the `before` parameter for the next request.

## 3. Incremental Data Loading

**Incremental Support:**
- **Parameter:** `since`
- **Field:** `date` for actions, `dateLastActivity` for cards.
- **Format:** ISO 8601 date format.
- **Initial Value:** Use the earliest date of interest for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Boards** depend on **Members**: `/members/{id}/boards`
- **Actions, Cards, Checklists** depend on **Boards**: `/boards/{idBoard}/...`
- **Mapping:** Use `id` from parent to replace placeholders in child paths.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests:** Trello API rate limits are not explicitly documented, but it's recommended to keep requests under 100 per 10 seconds.
- **Burst Limits:** Implement a delay of 0.1 seconds between requests to avoid hitting limits.

**Special Requirements:**
- **Headers:** Include `Authorization` with `key` and `token`.
- **Response Format:** JSON.
- **Error Handling:** Handle HTTP 429 for rate limits, retry with exponential backoff.
- **Data Type Specifications:** Ensure correct data types as per schema definitions.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.trello.com/1/",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "Authorization",
        "format": "key={developer_api_key}&token={access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "members",
        "path": "/members/me",
        "method": "GET",
        "data_selector": "$.id",
        "primary_key": "id"
    },
    {
        "name": "boards",
        "path": "/members/{id}/boards",
        "method": "GET",
        "data_selector": "$.[]",
        "primary_key": "id"
    },
    {
        "name": "actions",
        "path": "/boards/{idBoard}/actions",
        "method": "GET",
        "data_selector": "$.[]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "before",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "cards",
        "path": "/boards/{idBoard}/cards/all",
        "method": "GET",
        "data_selector": "$.[]",
        "primary_key": "id"
    },
    {
        "name": "checklists",
        "path": "/boards/{idBoard}/checklists",
        "method": "GET",
        "data_selector": "$.[]",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "boards",
        "depends_on": "members",
        "param_mapping": {"id": "id"}
    },
    {
        "endpoint": "actions",
        "depends_on": "boards",
        "param_mapping": {"idBoard": "id"}
    },
    {
        "endpoint": "cards",
        "depends_on": "boards",
        "param_mapping": {"idBoard": "id"}
    },
    {
        "endpoint": "checklists",
        "depends_on": "boards",
        "param_mapping": {"idBoard": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the Trello API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases or additional API features.

---
*dlt REST API Source Configuration Guide*