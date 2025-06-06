# Degreed REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Degreed REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.degreed.com/api/v2`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://degreed.com/oauth/token`
- **Scopes**: `users:read`, `completions:read`, `pathways:read`
- **Request Body Parameters**:
  - `grant_type`: `client_credentials`
  - `client_id`: Provided by the user
  - `client_secret`: Provided by the user

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array located at `$.data[*]`

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `next`
- **Default/Maximum Page Size**: 1000

#### Data Extraction
- **JSONPath**: `$.data[*]`
- **Primary Key**: `id`

### Completions Endpoint
- **Path**: `/completions`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `filter[start_date]`: Start date for data retrieval
  - `filter[end_date]`: End date for data retrieval
- **Response Data Structure**: JSON array located at `$.data[*]`

#### Pagination Configuration
- **Type**: `cursor`
- **Next Page Token JSONPath**: `$.links.next`
- **Default/Maximum Page Size**: 1000

#### Data Extraction
- **JSONPath**: `$.data[*]`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Users Endpoint
- **Incremental Support**: Not explicitly supported

### Completions Endpoint
- **Incremental Support**: Yes
- **Query Parameter**: `filter[start_date]`, `filter[end_date]`
- **Date Field**: `completed-at`
- **Date Format**: ISO 8601 (e.g., `YYYY-MM-DD`)
- **Recommended Initial Values**: Use `start_date` and `end_date` from the configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Completions** depend on **Users** for user-specific data.
- **Mapping**: Use `user_id` from the Users endpoint to filter data in the Completions endpoint.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Not explicitly documented; monitor `X-RateLimit-Remaining` header for limits.
- **Burst Limits**: Implement exponential backoff for handling rate limits.

### Special Requirements
- **Headers**: 
  - `Authorization`: `Bearer {token}`
  - `Content-Type`: `application/x-www-form-urlencoded` for token requests
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for HTTP 429 and 5xx errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.degreed.com/api/v2",
    "auth": {
        "type": "oauth2",
        "token_url": "https://degreed.com/oauth/token",
        "scopes": ["users:read", "completions:read", "pathways:read"],
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "next",
            "limit": 1000
        }
    },
    {
        "name": "completions",
        "path": "/completions",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "next_page_token_jsonpath": "$.links.next",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "completed-at",
            "param_name": "filter[start_date]",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "completions",
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the Degreed API using a dlt data pipeline. Adjust the configuration as needed based on specific requirements and API updates.

---
*dlt REST API Source Configuration Guide*