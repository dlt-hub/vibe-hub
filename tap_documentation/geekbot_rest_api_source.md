# Geekbot REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Geekbot

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Geekbot REST API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- `https://api.geekbot.com`
- All endpoints are prefixed with `/v1/`

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `Authorization`
- Format: `Bearer {api_key}`

## 2. Available Endpoints

### Teams Endpoint

**Endpoint Details:**
- Path: `/v1/teams`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array located at `$.users[*]`

**Pagination Configuration:**
- Type: `single_page` (No pagination required)

**Data Extraction:**
- JSONPath: `$.users[*]`
- Primary Key: `id`

### Reports Endpoint

**Endpoint Details:**
- Path: `/v1/reports`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array

**Pagination Configuration:**
- Type: `offset`
- Limit Parameter: `limit`
- Default Limit: `100`
- Offset Parameter: `offset`

**Data Extraction:**
- JSONPath: `$[*]`
- Primary Key: `id`

### StandUps Endpoint

**Endpoint Details:**
- Path: `/v1/standups`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array

**Pagination Configuration:**
- Type: `single_page` (No pagination required)

**Data Extraction:**
- JSONPath: `$[*]`
- Primary Key: `id`

## 3. Incremental Data Loading

**Incremental Support:**
- Not explicitly supported in the current configuration. However, the `Reports` endpoint can be configured for incremental loading using a timestamp field if available in the response.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No explicit dependencies between endpoints are defined. However, data from the `Teams` endpoint could be used to enrich data from other endpoints if necessary.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not documented. It is recommended to implement a retry mechanism with exponential backoff to handle potential rate limiting.

**Special Requirements:**
- Custom Headers: `User-Agent` header is required and should be set to `{tap_name}/{plugin_version}`.
- Response Format: JSON
- Error Handling: Implement error handling for common HTTP errors (e.g., 429 for rate limiting, 500 for server errors).

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.geekbot.com",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "teams",
        "path": "/v1/teams",
        "method": "GET",
        "data_selector": "$.users[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "reports",
        "path": "/v1/reports",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        }
    },
    {
        "name": "standups",
        "path": "/v1/standups",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Geekbot API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*