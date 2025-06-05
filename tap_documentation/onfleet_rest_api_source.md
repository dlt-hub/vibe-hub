# Onfleet REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Onfleet REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://onfleet.com/api/v2/`

**Authentication:**
- Type: `basic`
- Location: `header`
- Parameter Name: `Authorization`
- Format: `Basic {api_key}`

## 2. Available Endpoints

### Administrators Endpoint

**Endpoint Details:**
- Path: `/admins`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: List of administrator objects

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: Root level (list of administrators)
- Primary Key: `id`

### Hubs Endpoint

**Endpoint Details:**
- Path: `/hubs`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: List of hub objects

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: Root level (list of hubs)
- Primary Key: `id`

### Organizations Endpoint

**Endpoint Details:**
- Path: `/organization`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: Organization object

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: Root level (organization object)
- Primary Key: `id`

### Tasks Endpoint

**Endpoint Details:**
- Path: `/tasks/all`
- HTTP Method: `GET`
- Required Query Parameters: `from` (timestamp), `lastId` (optional for pagination)
- Response Data Structure: List of task objects

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `lastId`
- Next Page Determination: Use `lastId` from the response

**Data Extraction:**
- JSONPath: `tasks`
- Primary Key: `id`

### Teams Endpoint

**Endpoint Details:**
- Path: `/teams`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: List of team objects

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: Root level (list of teams)
- Primary Key: `id`

### Workers Endpoint

**Endpoint Details:**
- Path: `/workers`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: List of worker objects

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: Root level (list of workers)
- Primary Key: `id`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `from`
- Date/Timestamp Field: `timeLastModified`
- Expected Format: Unix timestamp (milliseconds)
- Recommended Initial Value: Configured `start_date` in Unix timestamp format

## 4. Endpoint Dependencies

**Resource Relationships:**
- No explicit dependencies between endpoints. Each endpoint can be queried independently.

## 5. API Behavior & Limits

**Rate Limiting:**
- Maximum Requests: 20 requests per second
- Recommended Delay: Implement backoff strategy if rate limit is approached

**Special Requirements:**
- Custom Headers: `User-Agent` must be specified
- Response Format: JSON
- Error Handling: Implement retry logic for transient errors

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://onfleet.com/api/v2/",
    "auth": {
        "type": "basic",
        "location": "header",
        "name": "Authorization",
        "format": "Basic {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "administrators",
        "path": "/admins",
        "method": "GET",
        "data_selector": None,
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "timeLastModified",
            "param_name": "from",
            "format": "unix"
        }
    },
    {
        "name": "tasks",
        "path": "/tasks/all",
        "method": "GET",
        "data_selector": "tasks",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "lastId"
        },
        "incremental": {
            "cursor_path": "timeLastModified",
            "param_name": "from",
            "format": "unix"
        }
    }
    # Additional endpoints can be added similarly
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters to set up a dlt data pipeline for the Onfleet API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*