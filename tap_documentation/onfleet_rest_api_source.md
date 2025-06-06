# Onfleet REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Onfleet API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://onfleet.com/api/v2/`
- This is the root URL for all API requests, including the version path.

### Authentication
- **Type**: `basic`
- **Details**:
  - **Location**: Header
  - **Header Name**: `Authorization`
  - **Format**: `Basic {api_key}` (Base64 encoded)
- **API Key**: Required for authentication. It should be provided in the configuration.

## 2. Available Endpoints

### Administrators
- **Path**: `/admins`
- **Method**: `GET`
- **Data Selector**: JSON array of administrators
- **Primary Key**: `id`
- **Pagination**: Not applicable (single page)
- **Incremental**: Supported via `timeLastModified`

### Hubs
- **Path**: `/hubs`
- **Method**: `GET`
- **Data Selector**: JSON array of hubs
- **Primary Key**: `id`
- **Pagination**: Not applicable (single page)
- **Incremental**: Not supported (full table)

### Organizations
- **Path**: `/organization`
- **Method**: `GET`
- **Data Selector**: JSON object of organization details
- **Primary Key**: `id`
- **Pagination**: Not applicable (single page)
- **Incremental**: Supported via `timeLastModified`

### Tasks
- **Path**: `/tasks/all`
- **Method**: `GET`
- **Data Selector**: JSON array of tasks
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `lastId`
- **Incremental**: Supported via `timeLastModified`

### Teams
- **Path**: `/teams`
- **Method**: `GET`
- **Data Selector**: JSON array of teams
- **Primary Key**: `id`
- **Pagination**: Not applicable (single page)
- **Incremental**: Supported via `timeLastModified`

### Workers
- **Path**: `/workers`
- **Method**: `GET`
- **Data Selector**: JSON array of workers
- **Primary Key**: `id`
- **Pagination**: Not applicable (single page)
- **Incremental**: Supported via `timeLastModified`

## 3. Incremental Data Loading

- **Incremental Support**: Enabled for endpoints with `timeLastModified` as the replication key.
- **Date/Time Field**: `timeLastModified`
- **Format**: ISO 8601 date-time strings
- **Initial Sync**: Use the `start_date` configuration parameter to set the initial sync point.

## 4. Endpoint Dependencies

- **Resource Relationships**: No explicit dependencies between endpoints.
- **Mapping Identifiers**: Each endpoint is independent, with unique identifiers for each resource type.

## 5. API Behavior & Limits

### Rate Limiting
- **Limit**: 20 requests per second
- **Burst Limits**: Implement a delay if the remaining quota is below the configured threshold (`quota_limit`).

### Special Requirements
- **Custom Headers**: `User-Agent` header is required.
- **Response Format**: JSON
- **Error Handling**: Implement retries with exponential backoff for request failures.
- **Data Type Specifications**: Ensure date-time fields are correctly parsed and converted.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://onfleet.com/api/v2/",
    "auth": {
        "type": "basic",
        "location": "header",
        "name": "Authorization",
        "format": "Basic {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "administrators",
        "path": "/admins",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "timeLastModified",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "tasks",
        "path": "/tasks/all",
        "method": "GET",
        "data_selector": "tasks",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "lastId"
        },
        "incremental": {
            "cursor_path": "timeLastModified",
            "param_name": "since",
            "format": "iso"
        }
    }
    // Add other endpoints similarly
]

DEPENDENCIES = []
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Onfleet API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*