# Autodesk Bim 360 REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Autodesk BIM 360

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Autodesk BIM 360 REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://developer.api.autodesk.com/`
- **Version Path**: `/v1/` for most endpoints, specific paths for others (e.g., `/hq/v1/`, `/project/v1/`)

### Authentication
- **Type**: `oauth2`
- **OAuth2 Details**:
  - **Token URL**: `https://developer.api.autodesk.com/authentication/v1/authenticate`
  - **Scopes**: `data:read`, `account:read`
  - **Token Refresh URL**: `https://developer.api.autodesk.com/authentication/v1/refreshtoken`
- **Headers**:
  - **Authorization**: `Bearer {access_token}`

## 2. Available Endpoints

### Endpoint: Business Units
- **Path**: `/hq/v1/accounts/{account_id}/business_units_structure`
- **HTTP Method**: `GET`
- **Authentication**: `app`
- **Response Data Structure**: JSON array under `business_units`

### Endpoint: Projects
- **Path**: `/hq/v1/accounts/{account_id}/projects`
- **HTTP Method**: `GET`
- **Authentication**: `app`
- **Query Parameters**: `sort=-updated_at`
- **Response Data Structure**: JSON array

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
- **Default Page Size**: 100

### Data Extraction
- **JSONPath**: `data` for most endpoints
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `updated_at`
- **Format**: ISO 8601
- **Initial Value**: Earliest possible date or `null` for full sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Parent Endpoint**: `hub_projects`
- **Child Endpoints**: `checklists`, `issues`, `folder_contents`
- **Mapping**:
  - **Project ID**: `project_id`
  - **Container ID**: `container_id`
  - **Root Folder ID**: `root_folder_id`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 300 per minute
- **Burst Limits**: Handle 429 status with retry-after logic

### Special Requirements
- **Custom Headers**: `User-Agent` if specified
- **Error Handling**: Retry on 5xx errors, handle 401/403 for specific endpoints

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://developer.api.autodesk.com/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://developer.api.autodesk.com/authentication/v1/authenticate",
        "refresh_token_url": "https://developer.api.autodesk.com/authentication/v1/refreshtoken",
        "scopes": ["data:read", "account:read"],
        "header": "Authorization",
        "format": "Bearer {access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "business_units",
        "path": "/hq/v1/accounts/{account_id}/business_units_structure",
        "method": "GET",
        "data_selector": "business_units",
        "primary_key": "id",
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
        "name": "projects",
        "path": "/hq/v1/accounts/{account_id}/projects",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
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
        "endpoint": "checklists",
        "depends_on": "hub_projects",
        "param_mapping": {"container_id": "id"}
    },
    {
        "endpoint": "issues",
        "depends_on": "hub_projects",
        "param_mapping": {"container_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Autodesk BIM 360 API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*