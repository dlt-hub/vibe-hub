# dlt REST API Source Configuration Documentation for Auth0

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Auth0 REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{domain}/api/v2/`
  - Replace `{domain}` with your Auth0 tenant domain.

### Authentication
- **Type**: `oauth2`
- **OAuth2 Configuration**:
  - **Token URL**: `https://{domain}/oauth/token`
  - **Required Scopes**: Not specified, but ensure your client has the necessary permissions for the endpoints you intend to access.
  - **OAuth Request Body**:
    ```json
    {
      "grant_type": "client_credentials",
      "client_id": "<your_client_id>",
      "client_secret": "<your_client_secret>",
      "audience": "https://{domain}/api/v2/"
    }
    ```

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `user_id`
- **Pagination**: 
  - **Type**: `single_page` (due to Auth0's limitation of 1000 users per request)
  - **Workaround**: Use the `/jobs/users-exports` endpoint to create a user export job for larger datasets.

### Clients Endpoint
- **Path**: `/clients`
- **HTTP Method**: `GET`
- **Primary Key**: `client_id`
- **Pagination**: `single_page`

### Logs Endpoint
- **Path**: `/logs`
- **HTTP Method**: `GET`
- **Primary Key**: `log_id`
- **Pagination**:
  - **Type**: `cursor`
  - **Cursor Parameter**: `from`
  - **Limit Parameter**: `take`
  - **Default Page Size**: 100

## 3. Incremental Data Loading

### Users Stream
- **Incremental Support**: Not directly supported due to export job requirement.
- **Date/Timestamp Field**: `updated_at`
- **Format**: ISO 8601

### Logs Stream
- **Incremental Support**: Yes
- **Cursor Parameter**: `from`
- **Date/Timestamp Field**: `log_id`
- **Format**: String (log ID)

## 4. Endpoint Dependencies

### Resource Relationships
- **Users and Logs**: No direct dependencies, but logs may reference user IDs.
- **Clients**: Standalone, no dependencies.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly documented, but typical rate limits apply.
- **Burst Limits**: Implement exponential backoff with a maximum of 8 retries.

### Special Requirements
- **Custom Headers**: None specified beyond standard OAuth2 headers.
- **Response Format**: JSON
- **Error Handling**: Handle HTTP 400 errors gracefully, especially for logs pagination.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{domain}/api/v2/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://{domain}/oauth/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "audience": "https://{domain}/api/v2/"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "user_id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "clients",
        "path": "/clients",
        "method": "GET",
        "primary_key": "client_id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "logs",
        "path": "/logs",
        "method": "GET",
        "primary_key": "log_id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "from",
            "limit_param": "take",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "log_id",
            "param_name": "from",
            "format": "string"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline with the Auth0 REST API. Adjust the configuration as needed based on your specific use case and API access requirements.