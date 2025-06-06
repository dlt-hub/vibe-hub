# Dynamics REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with a REST API source. The configuration is structured to ensure seamless data extraction, transformation, and loading processes.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{domain}.dynamics.com/api/data/v9.2/`
- **Version Path**: `/v9.2/` is included in the base URL to specify the API version.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://login.microsoftonline.com/common/oauth2/token`
- **Required Scopes**: Not explicitly mentioned, but typically includes access to Dynamics resources.
- **Parameters**:
  - `client_id`: Your application's client ID.
  - `client_secret`: Your application's client secret.
  - `redirect_uri`: The redirect URI configured in your application.
  - `refresh_token`: The refresh token obtained from the OAuth2 flow.
  - `resource`: `https://{domain}.dynamics.com`

## 2. Available Endpoints

### Endpoint Details

#### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None specified.
- **Response Data Structure**: Typically includes user details such as `id`, `name`, `email`.

#### Pick Lists Endpoint
- **Path**: `/stringmaps`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None specified.
- **Response Data Structure**: Includes pick list values.

### Pagination Configuration
- **Type**: `json_link`
- **Parameter Names**: `@odata.nextLink` is used to determine the next page.
- **Default/Maximum Page Sizes**: Not explicitly defined, but typically managed by the API.

### Data Extraction
- **JSONPath**: Use `value` to locate the data array in responses.
- **Primary Key**: `id` for user records.
- **Key Fields**: `id`, `name`, `email` for users.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since` for incremental data fetching.
- **Date/Timestamp Field**: `modifiedon` is used to track updates.
- **Expected Format**: ISO 8601 date format.
- **Recommended Initial Values**: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: `user_posts` endpoint depends on `users`.
- **Mapping Identifiers**: Use `user_id` from `users` to map to `user_posts`.
- **Processing Order**: Fetch `users` first, then `user_posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined, but typical Dynamics API limits apply.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff.

### Special Requirements
- **Custom Headers**: None specified beyond standard OAuth2 headers.
- **Response Format Considerations**: JSON format.
- **Error Handling Patterns**: Handle `ODataError` for large responses by splitting requests.
- **Data Type Specifications**: Follow JSON schema as defined in the `discover.py`.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{domain}.dynamics.com/api/data/v9.2/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://login.microsoftonline.com/common/oauth2/token",
        "client_id": "{client_id}",
        "client_secret": "{client_secret}",
        "redirect_uri": "{redirect_uri}",
        "refresh_token": "{refresh_token}",
        "resource": "https://{domain}.dynamics.com"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "value",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_link": "@odata.nextLink"
        },
        "incremental": {
            "cursor_path": "modifiedon",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "pick_lists",
        "path": "/stringmaps",
        "method": "GET",
        "data_selector": "value"
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

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for a REST API source, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*