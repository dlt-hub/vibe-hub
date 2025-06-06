# Uservoice REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the UserVoice REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

**Base URL:**
- `https://{subdomain}.uservoice.com/api/v2/`
- Replace `{subdomain}` with your specific UserVoice subdomain.

**Authentication:**
- Type: `oauth2`
- Token URL: `https://{subdomain}.uservoice.com/api/v2/oauth/token`
- Required Scopes: Not specified, but ensure your API key has the necessary permissions.
- Parameters:
  - `client_id`: Your API key
  - `client_secret`: Your API secret
  - `grant_type`: `client_credentials`

## 2. Available Endpoints

### Users Endpoint

**Endpoint Details:**
- Path: `/api/v2/admin/users`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `updated_after`, `updated_before`, `cursor`, `per_page`
- Response Data Structure: JSON object with a `users` array

**Pagination Configuration:**
- Type: `cursor`
- Parameter Name: `cursor`
- Default Page Size: 100
- How Next Page is Determined: Use the `cursor` value from the `pagination` object in the response.

**Data Extraction:**
- JSONPath: `users`
- Primary Key: `id`
- Key Fields: `id`, `updated_at`

### Suggestions Endpoint

**Endpoint Details:**
- Path: `/api/v2/admin/suggestions`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `updated_after`, `updated_before`, `cursor`, `per_page`
- Response Data Structure: JSON object with a `suggestions` array

**Pagination Configuration:**
- Type: `cursor`
- Parameter Name: `cursor`
- Default Page Size: 100
- How Next Page is Determined: Use the `cursor` value from the `pagination` object in the response.

**Data Extraction:**
- JSONPath: `suggestions`
- Primary Key: `id`
- Key Fields: `id`, `updated_at`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `updated_after`
- Date/Timestamp Field: `updated_at`
- Expected Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Value: Use the `start_date` from the configuration file.

## 4. Endpoint Dependencies

**Resource Relationships:**
- Some endpoints may require data from others, such as `user_id` from the Users endpoint for related data.
- Ensure to map identifiers correctly between parent and child resources.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API may return a `429` status code for rate limits.
- Recommended Delay: Use the `Retry-After` header value or default to 5 seconds.

**Special Requirements:**
- Custom Headers: `Authorization: Bearer {access_token}`
- Response Format: JSON
- Error Handling: Retry on `401` and `429` status codes, with reauthorization on `401`.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{subdomain}.uservoice.com/api/v2/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://{subdomain}.uservoice.com/api/v2/oauth/token",
        "client_id": "{api_key}",
        "client_secret": "{api_secret}",
        "grant_type": "client_credentials"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/api/v2/admin/users",
        "method": "GET",
        "data_selector": "users",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "cursor",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    },
    {
        "name": "suggestions",
        "path": "/api/v2/admin/suggestions",
        "method": "GET",
        "data_selector": "suggestions",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "cursor",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    # Define any endpoint dependencies here
]
```

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline with the UserVoice API. Adjust the configuration as needed based on your specific use case and API access requirements.

---
*dlt REST API Source Configuration Guide*