# Helpshift REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Helpshift API. It includes detailed instructions on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.helpshift.com/v1/`
- **Analytics Base URL**: `https://analytics.helpshift.com/v1/`
- The base URL includes the version path and is used for all API requests.

### Authentication
- **Type**: `basic`
- **Details**: 
  - The API uses Basic Authentication with an API key.
  - The API key is passed as a username with an empty password.
  - Example: `Authorization: Basic {api_key}`

## 2. Available Endpoints

### Issues Endpoint
- **Path**: `/issues`
- **HTTP Method**: `GET`
- **Required Query Parameters**: 
  - `updated_since`: Start date for fetching issues.
- **Optional Query Parameters**: 
  - `updated_until`: End date for fetching issues.
  - `includes`: Additional fields to include in the response.
- **Response Data Structure**: JSON object with a key `issues` containing an array of issue records.

### Messages Endpoint
- **Path**: `/issues/{issue_id}/messages`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON object with a key `messages` containing an array of message records.

### Apps Endpoint
- **Path**: `/apps`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON object with a key `apps` containing an array of app records.

### Agents Endpoint
- **Path**: `/agents`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON object with a key `profiles` containing an array of agent records.

### Issue Analytics Endpoint
- **Path**: `/analytics/issue`
- **HTTP Method**: `GET`
- **Required Query Parameters**: 
  - `from`: Start date for analytics data.
  - `to`: End date for analytics data.
- **Response Data Structure**: JSON object with a key `results` containing an array of analytics records.

## 3. Pagination Configuration

### Issues Endpoint
- **Type**: `page_number`
- **Parameter Names**: 
  - `page`: Current page number.
  - `page-size`: Number of records per page.
- **Default/Maximum Page Sizes**: Default is 1000 records per page.

## 4. Incremental Data Loading

### Issues Endpoint
- **Incremental Support**: Enabled using the `updated_since` query parameter.
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: Epoch milliseconds
- **Recommended Initial Values**: Use the start date of the Helpshift service or a specific date for initial sync.

## 5. Endpoint Dependencies

### Resource Relationships
- **Messages Endpoint**: Depends on the `issues` endpoint. Requires `issue_id` from the issues data.
- **Issue Analytics Endpoint**: Can be synced independently or in relation to specific issues.

## 6. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 1500 requests
- **Burst Limits**: Implement a delay between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: JSON format with specific keys for data extraction.
- **Error Handling Patterns**: Handle HTTP 429 for rate limiting and retry after specified delay.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.helpshift.com/v1/",
    "auth": {
        "type": "basic",
        "name": "Authorization",
        "format": "Basic {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "issues",
        "path": "/issues",
        "method": "GET",
        "data_selector": "issues",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "page-size",
            "offset_param": "page",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_since",
            "format": "epoch_milliseconds"
        }
    },
    {
        "name": "messages",
        "path": "/issues/{issue_id}/messages",
        "method": "GET",
        "data_selector": "messages",
        "primary_key": "id"
    },
    {
        "name": "apps",
        "path": "/apps",
        "method": "GET",
        "data_selector": "apps",
        "primary_key": "id"
    },
    {
        "name": "agents",
        "path": "/agents",
        "method": "GET",
        "data_selector": "profiles",
        "primary_key": "id"
    },
    {
        "name": "issue_analytics",
        "path": "/analytics/issue",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "row_id",
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "from",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "messages", 
        "depends_on": "issues",
        "param_mapping": {"issue_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and instructions to set up a dlt data pipeline for the Helpshift API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*