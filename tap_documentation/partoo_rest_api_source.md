# Partoo REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Partoo REST API. It covers client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.partoo.co/v2`
- This is the root URL for all API requests, including the version path.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `x-APIKey`
- The API key must be included in the request headers for authentication.

## 2. Available Endpoints

### Businesses Endpoint
- **Path**: `/business/search`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Response Data Structure**: JSON array located at `$.businesses[*]`

### Groups Endpoint
- **Path**: `/groups`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Response Data Structure**: JSON array located at `$.groups[*]`

### Reviews Endpoint
- **Path**: `/reviews`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Response Data Structure**: JSON array located at `$.reviews[*]`

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `page`
- **How Next Page is Determined**: The `page` parameter is incremented until `current_page` equals `max_page` in the response.
- **Default/Maximum Page Sizes**: Not explicitly defined; follow API documentation for limits.

## 3. Incremental Data Loading

### Reviews Stream
- **Incremental Support**: Enabled
- **Query Parameter**: `update_date__gte`
- **Date/Timestamp Field**: `update_date`
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Values**: Use the `start_date` configuration parameter to specify the initial sync date.

## 4. Endpoint Dependencies

### Resource Relationships
- **Reviews Endpoint**: May depend on `businesses` or `groups` for context, but no explicit dependencies are defined in the current configuration.
- **Mapping Identifiers**: Not applicable in the current setup.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not specified; consult Partoo API documentation for rate limits.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff to handle rate limits gracefully.

### Special Requirements
- **Custom Headers**: Include `User-Agent` if specified in the configuration.
- **Response Format Considerations**: Ensure JSON parsing is robust to handle any variations in response structure.
- **Error Handling Patterns**: Implement error handling to manage HTTP errors and retry logic.
- **Data Type Specifications**: Ensure data types in the schema match the API response data types.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.partoo.co/v2",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "x-APIKey"
    }
}

ENDPOINTS = [
    {
        "name": "businesses",
        "path": "/business/search",
        "method": "GET",
        "data_selector": "businesses",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    },
    {
        "name": "groups",
        "path": "/groups",
        "method": "GET",
        "data_selector": "groups",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    },
    {
        "name": "reviews",
        "path": "/reviews",
        "method": "GET",
        "data_selector": "reviews",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "update_date",
            "param_name": "update_date__gte",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Partoo API, ensuring efficient data extraction and incremental updates.

---
*dlt REST API Source Configuration Guide*