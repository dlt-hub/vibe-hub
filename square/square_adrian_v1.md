# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Square API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://connect.squareup.com/v2/`
- **Version**: The API uses version 2, as indicated in the base URL.

### Authentication
- **Type**: `oauth2`
- **Token URL**: Not explicitly required as the Square API uses a refresh token to obtain an access token.
- **Required Scopes**: Not specified, but ensure the application has permissions for the required endpoints.
- **Access Token**: Obtained using the `refresh_token`, `client_id`, and `client_secret`.

## 2. Available Endpoints

### Endpoint: Items
- **Path**: `/catalog/list`
- **Method**: `POST`
- **Query Parameters**: None required for basic listing.
- **Response Data Structure**: JSON with a key `objects` containing the list of items.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `cursor`
- **Determination**: The next page is determined by the `cursor` returned in the response.
- **Page Size**: Default is 100, can be adjusted using the `limit` parameter.

### Data Extraction
- **JSONPath**: `$.objects`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `begin_time`
- **Date Field**: `updated_at`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the earliest date of interest or the current date minus a reasonable period (e.g., one month).

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Some endpoints like `orders` depend on `locations` for filtering.
- **Mapping**: Use `location_id` from the `locations` endpoint to filter `orders`.
- **Processing Order**: Fetch `locations` first, then use their IDs to fetch `orders`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: The Square API has a limit of 200 requests per minute.
- **Burst Limits**: Not explicitly stated, but adhere to the general rate limit.
- **Recommended Delays**: Implement exponential backoff for retries.

### Special Requirements
- **Headers**: Include `Authorization: Bearer {access_token}` in all requests.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for 5xx errors and handle 4xx errors gracefully.
- **Data Type Specifications**: Ensure date fields are handled in ISO 8601 format.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://connect.squareup.com/v2/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://connect.squareup.com/oauth2/token",
        "client_id": "{client_id}",
        "client_secret": "{client_secret}",
        "refresh_token": "{refresh_token}"
    }
}

ENDPOINTS = [
    {
        "name": "items",
        "path": "/catalog/list",
        "method": "POST",
        "data_selector": "objects",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "cursor",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "begin_time",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "orders",
        "depends_on": "locations",
        "param_mapping": {"location_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Square API, ensuring efficient data extraction and synchronization.