# Mavenlink REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Mavenlink

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Mavenlink REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.mavenlink.com/api/v1/`
- **Version Path**: `/api/v1/`

### Authentication
- **Type**: `bearer`
- **Header Name**: `Authorization`
- **Token Format**: `Bearer {token}`
- **Parameter Name**: `token`

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users.json`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON object with key `users`
- **Primary Key**: `id`

#### Pagination Configuration
- **Type**: `page_number`
- **Parameters**:
  - `page`: Current page number
  - `per_page`: Number of records per page (default: 200)
- **Next Page Determination**: Increment `page` until `page` equals `meta.page_count`

#### Data Extraction
- **JSONPath**: `users`
- **Key Fields**: `id`

### Endpoint: Workspaces
- **Path**: `/workspaces.json`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON object with key `workspaces`
- **Primary Key**: `id`

#### Pagination Configuration
- **Type**: `page_number`
- **Parameters**:
  - `page`: Current page number
  - `per_page`: Number of records per page (default: 200)
- **Next Page Determination**: Increment `page` until `page` equals `meta.page_count`

#### Data Extraction
- **JSONPath**: `workspaces`
- **Key Fields**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `updated_since`
- **Date Field**: `updated_at`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the earliest date or a specific start date from configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Estimates Scenarios** must be synced before **Estimates Resources**.
- **Mapping**: `estimate_scenario_id` in Estimates Resources maps to `id` in Estimates Scenarios.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 60
- **Burst Limits**: 5 requests per second
- **Recommended Delay**: Implement exponential backoff on rate limit errors

### Special Requirements
- **Custom Headers**: `Content-Type: application/json`
- **Response Format**: JSON
- **Error Handling**: Retry on 5xx errors, log and skip on 4xx errors
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.mavenlink.com/api/v1/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users.json",
        "method": "GET",
        "data_selector": "users",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_since",
            "format": "iso"
        }
    },
    {
        "name": "workspaces",
        "path": "/workspaces.json",
        "method": "GET",
        "data_selector": "workspaces",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "estimate_scenario_resources",
        "depends_on": "estimate_scenarios",
        "param_mapping": {"estimate_scenario_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters to set up a dlt data pipeline for the Mavenlink API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*