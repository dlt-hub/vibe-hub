# Exacttarget REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source for the ExactTarget API, which is part of Salesforce Marketing Cloud. The configuration is designed to facilitate data extraction and integration into a data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/`
- **Version Path**: `/v1/`
- **Complete URL Example**: `https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v1/`

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v1/requestToken`
- **Required Scopes**: Not explicitly required, but ensure the API user has sufficient permissions.
- **Parameter Names**:
  - **Client ID**: `client_id`
  - **Client Secret**: `client_secret`
  - **Tenant Subdomain**: `tenant_subdomain` (for S10+ accounts)

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - **Required**: None
  - **Optional**: `limit`, `offset`
- **Response Data Structure**: JSON array under the key `data`

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**:
  - **Limit**: `limit`
  - **Offset**: `offset`
- **Default/Maximum Page Sizes**: Typically 1000, but can be adjusted based on API limits.
- **Next Page Determination**: Increment `offset` by `limit` until no more data is returned.

### Data Extraction
- **JSONPath**: `$.data[*]`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Values**: Use the current date minus a reasonable buffer (e.g., 30 days) for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: 
  - **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` from `users` to `user_posts`
- **Processing Order**: Fetch `users` first, then `user_posts` using `user_id` from `users`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Typically 10 requests per second.
- **Burst Limits**: May allow short bursts above the limit, but sustained requests should adhere to the limit.
- **Recommended Delays**: Implement exponential backoff for retries.

### Special Requirements
- **Custom Headers**: None required beyond standard OAuth2 headers.
- **Response Format Considerations**: Ensure JSON parsing handles nested structures.
- **Error Handling Patterns**: Implement retries with exponential backoff for transient errors.
- **Data Type Specifications**: Ensure correct data type handling, especially for dates and numbers.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v1/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v1/requestToken",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret",
        "tenant_subdomain": "your_tenant_subdomain"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "user_posts", 
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate the ExactTarget API into a dlt data pipeline, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*