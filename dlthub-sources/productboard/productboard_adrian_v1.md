# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Productboard REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.productboard.com`
- **Version Path**: `/v1/`

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {token}`

### Required Headers
- **X-Version**: `1` (default API version)
- **User-Agent**: Customizable via configuration
- **Productboard-Partner-Id**: Optional, customizable via configuration

## 2. Available Endpoints

### Companies
- **Path**: `/companies`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `json_link` with `$.links.next`

### Components
- **Path**: `/components`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `json_link` with `$.links.next`

### Features
- **Path**: `/features`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `json_link` with `$.links.next`

### Notes
- **Path**: `/notes`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `cursor` with `pageCursor` parameter
- **Incremental**: `updatedAt` field with `updatedFrom` parameter

### Users
- **Path**: `/users`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `json_link` with `$.links.next`

## 3. Incremental Data Loading

### Supported Endpoints
- **Notes**: Uses `updatedAt` field for incremental loading.
  - **Parameter**: `updatedFrom`
  - **Format**: ISO 8601 date format (e.g., `YYYY-MM-DD`)

### Recommended Initial Values
- **Start Date**: Configurable via `start_date` parameter in the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Notes** may depend on other endpoints for context, but no explicit dependencies are defined in the current configuration.

## 5. API Behavior & Limits

### Rate Limiting
- **General Limits**: Not explicitly defined; consult Productboard API documentation for specific rate limits.
- **Recommended Delays**: Implement retry logic with exponential backoff for handling rate limits.

### Special Requirements
- **Custom Headers**: `X-Version`, `User-Agent`, `Productboard-Partner-Id`
- **Response Format**: JSON
- **Error Handling**: Implement error handling for HTTP errors and API-specific error codes.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.productboard.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "companies",
        "path": "/companies",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_page_token_jsonpath": "$.links.next"
        }
    },
    {
        "name": "notes",
        "path": "/notes",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "pageCursor"
        },
        "incremental": {
            "cursor_path": "updatedAt",
            "param_name": "updatedFrom",
            "format": "iso"
        }
    }
    # Add other endpoints similarly
]

DEPENDENCIES = [
    # Define any endpoint dependencies if applicable
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Productboard API, ensuring efficient data extraction and integration.