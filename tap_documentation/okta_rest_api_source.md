# Okta REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Okta REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{yourOktaDomain}/api/v1/`
- **Version Path**: `/api/v1/` is the common prefix for all endpoints.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `SSWS {api_key}`

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array of user objects

### Groups Endpoint
- **Path**: `/groups`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array of group objects

### Applications Endpoint
- **Path**: `/apps`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array of application objects

### Pagination Configuration
- **Type**: `header_link`
- **Next Page Determination**: Use the `Link` header with `rel="next"` to find the next page URL.
- **Default/Maximum Page Sizes**: Determined by Okta API defaults; typically 200 records per page.

### Data Extraction
- **JSONPath**: Directly iterate over the JSON array in the response.
- **Primary Key**: `id` for all entities (users, groups, applications).

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Values**: Use the current date minus a reasonable buffer (e.g., 30 days) for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Users and Groups**: Use `/groups/{groupId}/users` to fetch users in a group.
- **Applications and Users**: Use `/apps/{appId}/users` to fetch users assigned to an application.
- **Applications and Groups**: Use `/apps/{appId}/groups` to fetch groups assigned to an application.

### Required Processing Order
1. Fetch `users`.
2. Fetch `groups`.
3. Fetch `applications`.
4. Fetch `group_users` using group IDs.
5. Fetch `application_users` using application IDs.
6. Fetch `application_groups` using application IDs.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Typically 1000 requests per minute for Okta API.
- **Burst Limits**: Handle bursts by implementing a delay (e.g., 2 seconds) between requests.

### Special Requirements
- **Custom Headers**: `accept: application/json`, `content-type: application/json`
- **Response Format Considerations**: Ensure JSON parsing handles arrays and nested objects.
- **Error Handling Patterns**: Implement retry logic for transient errors (e.g., HTTP 429 Too Many Requests).
- **Data Type Specifications**: Ensure correct handling of date and time fields, especially for incremental updates.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{yourOktaDomain}/api/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "SSWS {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "header_link",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "groups",
        "path": "/groups",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "header_link",
            "limit": 200
        }
    },
    {
        "name": "applications",
        "path": "/apps",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "header_link",
            "limit": 200
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "group_users",
        "depends_on": "groups",
        "param_mapping": {"group_id": "id"}
    },
    {
        "endpoint": "application_users",
        "depends_on": "applications",
        "param_mapping": {"app_id": "id"}
    },
    {
        "endpoint": "application_groups",
        "depends_on": "applications",
        "param_mapping": {"app_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the Okta REST API using a dlt data pipeline. Adjust the base URL and authentication details according to your specific Okta domain and API key.

---
*dlt REST API Source Configuration Guide*