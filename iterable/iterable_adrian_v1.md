# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Iterable REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.iterable.com/api/`
- **Version**: No specific version path is required as the base URL includes the necessary API path.

### Authentication
- **Type**: `api_key`
- **Location**: `query`
- **Parameter Name**: `api_key`
- **Format**: The API key is passed as a query parameter in each request.

## 2. Available Endpoints

### Endpoint Details

#### Users
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**: None required for basic retrieval.
- **Response Data Structure**: JSON object with user details.

#### Lists
- **Path**: `/lists`
- **Method**: `GET`
- **Query Parameters**: None required.
- **Response Data Structure**: JSON object containing a list of lists.

#### Campaigns
- **Path**: `/campaigns`
- **Method**: `GET`
- **Query Parameters**: None required.
- **Response Data Structure**: JSON object with campaign details.

### Pagination Configuration

#### General Pagination
- **Type**: `single_page` (for most endpoints as they return all data in one response)
- **Parameter Names**: Not applicable for single-page responses.
- **Default/Maximum Page Sizes**: Not applicable.

### Data Extraction
- **JSONPath**: Typically, data is located under keys like `"lists"`, `"campaigns"`, etc.
- **Primary Key**: Unique identifiers such as `id` for lists and campaigns.

## 3. Incremental Data Loading

### Incremental Support

#### Campaigns
- **Query Parameter**: `updatedAt`
- **Date/Timestamp Field**: `updatedAt`
- **Format**: ISO 8601 date format
- **Recommended Initial Value**: Use the `start_date` from the configuration to fetch data from a specific point in time.

## 4. Endpoint Dependencies

### Resource Relationships

#### List Users
- **Depends On**: Lists
- **Mapping**: Use `listId` from the lists endpoint to fetch users associated with each list.
- **Processing Order**: Fetch lists first, then list users.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly documented, but implement exponential backoff on HTTP 429 responses.
- **Burst Limits**: Handle using backoff strategies.

### Special Requirements
- **Custom Headers**: None required beyond standard headers.
- **Response Format Considerations**: JSON responses.
- **Error Handling Patterns**: Implement retry logic with exponential backoff for HTTP errors, particularly 429 (Too Many Requests).

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.iterable.com/api/",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "api_key"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "users",
        "primary_key": "userId",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updatedAt",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "lists",
        "path": "/lists",
        "method": "GET",
        "data_selector": "lists",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "campaigns",
        "path": "/campaigns",
        "method": "GET",
        "data_selector": "campaigns",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updatedAt",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "list_users",
        "depends_on": "lists",
        "param_mapping": {"listId": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline with the Iterable REST API, ensuring efficient data extraction and integration.