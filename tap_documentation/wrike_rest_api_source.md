# Wrike REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Wrike API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://www.wrike.com/api/v4/`
- **Version Path**: Included in the base URL as `v4`

### Authentication
- **Type**: `oauth2`
- **OAuth2 Configuration**:
  - **Token URL**: `https://login.wrike.com/oauth2/token`
  - **Required Scopes**: `wsReadOnly`, `dataExportFull`, `amReadOnlyGroup`
  - **Client ID**: Provided by the user
  - **Client Secret**: Provided by the user
  - **Refresh Token**: Provided by the user

## 2. Available Endpoints

### Endpoint Details

#### Users
- **Path**: `/contacts`
- **Method**: `GET`
- **Query Parameters**: `fields=["metadata","workScheduleId"]`
- **Response Data Structure**: JSON with a `data` array containing user details

#### Tasks
- **Path**: `/tasks`
- **Method**: `GET`
- **Query Parameters**: `fields=["authorIds","hasAttachments","attachmentCount","parentIds","superParentIds","sharedIds","responsibleIds","responsiblePlaceholderIds","description","briefDescription","recurrent","superTaskIds","subTaskIds","dependencyIds","metadata","customFields","effortAllocation","billingType"]`, `pageSize=1000`
- **Response Data Structure**: JSON with a `data` array containing task details

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**: `limit`, `offset`
- **Default/Maximum Page Sizes**: Typically 1000 for tasks
- **Next Page Determination**: Based on `nextPageToken` in the response

### Data Extraction
- **JSONPath**: `data`
- **Primary Key**: `id`
- **Key Fields**: `id` uniquely identifies each record

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updatedDate`
- **Date/Timestamp Field**: `updatedDate` in API responses
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Values**: Use the current date minus a reasonable buffer period for the first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **User Posts**: Requires data from the `users` endpoint
- **Mapping Identifiers**: `user_id` from `users` maps to `user_id` in `user_posts`
- **Processing Order**: Fetch `users` before `user_posts`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly documented, but typical REST APIs have limits
- **Burst Limits**: Implement exponential backoff for retries

### Special Requirements
- **Custom Headers**: `Authorization: Bearer {token}`
- **Response Format Considerations**: Ensure JSON parsing handles potential errors
- **Error Handling Patterns**: Retry on 5xx errors, refresh token on 401/403 errors
- **Data Type Specifications**: Ensure correct handling of base32 encoded IDs

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://www.wrike.com/api/v4/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://login.wrike.com/oauth2/token",
        "scopes": ["wsReadOnly", "dataExportFull", "amReadOnlyGroup"]
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/contacts",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "updatedDate",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "tasks",
        "path": "/tasks",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "updatedDate",
            "param_name": "since",
            "format": "iso"
        }
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

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Wrike API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*