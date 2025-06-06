# Clarabridge REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Clarabridge API, focusing on the necessary parameters to build an effective data pipeline.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.engagor.com`
- **Version Path**: Not explicitly versioned in the URL

### Authentication
- **Type**: `api_key`
- **Location**: `query`
- **Parameter Name**: `access_token`
- **Format**: Directly included in query parameters

## 2. Available Endpoints

### Mentions Endpoint
- **Path**: `/inbox/mentions`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `access_token`: API key for authentication
  - `date_from`: Start date for data retrieval
  - `sort`: Sorting order, e.g., `timestamps.action_last_date:asc`
  - `filter`: Filter criteria, e.g., `status:alldone`
  - `limit`: Maximum number of records per request
- **Response Data Structure**: JSON with a key `data` containing the records

### Cases Endpoint
- **Path**: `/inbox/cases`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - Same as the Mentions endpoint
- **Response Data Structure**: JSON with a key `data` containing the records

### Pagination Configuration
- **Type**: `json_link`
- **Parameter Names**:
  - `limit`: Maximum number of records per page (default: 100)
- **Next Page Determination**: Use `paging.next_url` from the response to fetch the next page
- **Default/Maximum Page Sizes**: 100 records per page

### Data Extraction
- **JSONPath**: `data` for both endpoints
- **Key Fields**:
  - Mentions: `unique_id`
  - Cases: `case__unique_id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `date_from`
- **Date/Timestamp Field**: `actions[0].date.added` for both endpoints
- **Expected Format**: Unix timestamps
- **Recommended Initial Values**: Use a date 90 days prior to the current date for the first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Mentions and Cases**: No direct dependencies between these endpoints
- **Mapping Identifiers**: Not applicable as each endpoint operates independently

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Hour**: Not explicitly defined, but rate limits are enforced
- **Burst Limits**: Rate limit errors may occur, requiring handling of `429` or custom rate limit errors
- **Recommended Delays**: Use `x-ratelimit-reset` header to determine wait time before retrying

### Special Requirements
- **Custom Headers**: None required beyond standard authentication
- **Response Format Considerations**: JSON responses with nested structures
- **Error Handling Patterns**: Handle `429` and `400` errors related to rate limits, retry with exponential backoff
- **Data Type Specifications**: Ensure date fields are converted from Unix timestamps to UTC

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.engagor.com",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "access_token"
    }
}

ENDPOINTS = [
    {
        "name": "mentions",
        "path": "/inbox/mentions",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "unique_id",
        "pagination": {
            "type": "json_link",
            "limit_param": "limit",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "actions[0].date.added",
            "param_name": "date_from",
            "format": "unix"
        }
    },
    {
        "name": "cases",
        "path": "/inbox/cases",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "case__unique_id",
        "pagination": {
            "type": "json_link",
            "limit_param": "limit",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "actions[0].date.added",
            "param_name": "date_from",
            "format": "unix"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and considerations for integrating with the Clarabridge API using dlt, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*