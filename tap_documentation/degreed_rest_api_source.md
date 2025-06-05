# Degreed REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Degreed API. It includes all necessary parameters and settings to establish a data pipeline using the Degreed API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.degreed.com/api/v2`
- This URL includes the version path and serves as the root for all API requests.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://degreed.com/oauth/token`
- **Scopes**: `users:read`, `completions:read`, `pathways:read`
- **OAuth Request Body**:
  - `grant_type`: `client_credentials`
  - `client_id`: Provided by the user
  - `client_secret`: Provided by the user

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array located at `$.data[*]`
- **Primary Key**: `id`

### Completions Endpoint
- **Path**: `/completions`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `filter[start_date]`: Start date for data retrieval
  - `filter[end_date]`: End date for data retrieval
- **Response Data Structure**: JSON array located at `$.data[*]`
- **Primary Key**: `id`

### Pagination Configuration
- **Type**: `json_link`
- **Next Page Token JSONPath**: `$.links.next`
- **Limit Parameter**: `limit`
- **Default Limit**: 1000

## 3. Incremental Data Loading

### Users Endpoint
- **Incremental Support**: Not explicitly defined

### Completions Endpoint
- **Incremental Support**: Yes
- **Cursor Path**: `completed-at`
- **Parameter Name**: `filter[start_date]`
- **Date Format**: `YYYY-MM-DD`
- **Recommended Initial Value**: Two days before the current date

## 4. Endpoint Dependencies

### Resource Relationships
- **Completions Endpoint**: May depend on user data for context, but no explicit dependencies are defined in the current configuration.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Not explicitly defined in the provided code
- **Burst Limits**: Not explicitly defined

### Special Requirements
- **Headers**: 
  - `Accept`: `application/json`
  - `Authorization`: `Bearer {token}`
- **Response Format**: JSON
- **Error Handling**: Raise exceptions on HTTP errors

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
            "type": "json_link",
            "next_page_token_jsonpath": "$.links.next",
            "limit_param": "limit",
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
            "type": "json_link",
            "next_page_token_jsonpath": "$.links.next",
            "limit_param": "limit",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "completed-at",
            "param_name": "filter[start_date]",
            "format": "YYYY-MM-DD"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Degreed API, ensuring efficient data extraction and incremental updates.

---
*dlt REST API Source Configuration Guide*