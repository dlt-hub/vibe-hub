# Pivotal Tracker REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Pivotal Tracker REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://www.pivotaltracker.com/services/v5/`
- **Version Path**: `/v5/`

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `X-TrackerToken`
- **Token Format**: Direct API token in the header

## 2. Available Endpoints

### Accounts Endpoint
- **Path**: `/accounts`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array under `data`

### Projects Endpoint
- **Path**: `/projects`
- **HTTP Method**: `GET`
- **Required Query Parameters**: Optional `account_ids`
- **Response Data Structure**: JSON array under `data`

### Project Memberships Endpoint
- **Path**: `/projects/{project_id}/memberships`
- **HTTP Method**: `GET`
- **Required Query Parameters**: Optional parameters like `role`, `email`, etc.
- **Response Data Structure**: JSON array under `data`

### Labels Endpoint
- **Path**: `/projects/{project_id}/labels`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array under `data`

### Stories Endpoint
- **Path**: `/projects/{project_id}/stories`
- **HTTP Method**: `GET`
- **Required Query Parameters**: Optional parameters like `with_label`, `with_story_type`, etc.
- **Response Data Structure**: JSON array under `data`

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**: `offset`, `limit`
- **Default/Maximum Page Sizes**: Not explicitly defined, but typically handled by `limit` parameter
- **Next Page Determination**: Based on `offset` and `returned` fields in `pagination` object

## 3. Incremental Data Loading

### Incremental Support for Stories
- **Query Parameter**: `updated_after`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Values**: Current timestamp for first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Projects and Project Memberships**: Project memberships require project IDs from the Projects endpoint.
- **Projects and Labels**: Labels require project IDs from the Projects endpoint.
- **Projects and Stories**: Stories require project IDs from the Projects endpoint.

### Mapping Identifiers
- **Project ID**: Used to map between Projects and dependent endpoints like Memberships, Labels, and Stories.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 300 requests per minute
- **Burst Limits**: Handled using `backoff` library with Fibonacci strategy

### Special Requirements
- **Custom Headers**: `User-Agent` and `X-TrackerToken` are required
- **Response Format Considerations**: JSON format with data arrays under `data` key
- **Error Handling Patterns**: Use of `backoff` for retrying on non-fatal errors
- **Data Type Specifications**: Ensure correct handling of JSON data types as per schema

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://www.pivotaltracker.com/services/v5/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "X-TrackerToken"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/accounts",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset"
        }
    },
    {
        "name": "projects",
        "path": "/projects",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset"
        }
    },
    {
        "name": "project_memberships",
        "path": "/projects/{project_id}/memberships",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset"
        }
    },
    {
        "name": "labels",
        "path": "/projects/{project_id}/labels",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset"
        }
    },
    {
        "name": "stories",
        "path": "/projects/{project_id}/stories",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "project_memberships",
        "depends_on": "projects",
        "param_mapping": {"project_id": "id"}
    },
    {
        "endpoint": "labels",
        "depends_on": "projects",
        "param_mapping": {"project_id": "id"}
    },
    {
        "endpoint": "stories",
        "depends_on": "projects",
        "param_mapping": {"project_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Pivotal Tracker REST API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*