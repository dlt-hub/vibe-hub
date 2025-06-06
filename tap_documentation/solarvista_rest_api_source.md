# Solarvista REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Solarvista REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.solarvista.com/`
- **Version Path**: `/workflow/v4/` for workflow-related endpoints, `/datagateway/v3/` for data gateway, and `/calendar/v2/` for calendar-related endpoints.

### Authentication
- **Type**: `bearer`
- **Token Retrieval**: Access tokens are obtained via a POST request to `https://auth.solarvista.com/connect/token`.
- **Token Format**: `Bearer {token}`
- **Required Headers**: 
  - `Authorization`: Bearer token
  - `Content-Type`: `application/json`

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/datagateway/v3/{account}/datasources/ref/users/data/query`
- **Method**: `POST`
- **Query Parameters**: Supports `continuationToken` for pagination.
- **Response Structure**: JSON with `rows` containing `rowData`.

### Work Items Endpoint
- **Path**: `/workflow/v4/{account}/workItems/search`
- **Method**: `POST`
- **Query Parameters**: 
  - `lastModifiedAfter`: ISO date for incremental loading
  - `orderBy`: Field to order results
  - `continuationToken`: For pagination
- **Response Structure**: JSON with `items` and `continuationToken`.

### Appointments Endpoint
- **Path**: `/calendar/v2/{account}/appointments/search/users`
- **Method**: `POST`
- **Query Parameters**: 
  - `from`, `to`: ISO date range
  - `userIds`: List of user IDs
  - `continuationToken`: For pagination
- **Response Structure**: JSON with `appointments` and `continuationToken`.

## 3. Pagination Configuration

- **Type**: `cursor`
- **Parameter Name**: `continuationToken`
- **Default Page Size**: Not explicitly defined; determined by API response.
- **Next Page Determination**: Use `continuationToken` from the response to fetch subsequent pages.

## 4. Incremental Data Loading

- **Supported**: Yes
- **Query Parameter**: `lastModifiedAfter` for work items.
- **Date Field**: `lastModified` in responses.
- **Format**: ISO 8601 date format.
- **Initial Value**: Configurable via `start_date` in the configuration.

## 5. Endpoint Dependencies

- **Work Item History**: Requires `workItemId` from the work items endpoint.
- **Activity**: Requires `workItemId` from the work items endpoint.
- **Processing Order**: Fetch work items first, then use their IDs to fetch related history and activity data.

## 6. API Behavior & Limits

### Rate Limiting
- **Requests**: Not explicitly defined; handle with retries and backoff.
- **Burst Limits**: Implement retry logic with exponential backoff for 429 and 5xx errors.

### Special Requirements
- **Custom Headers**: `Authorization` with Bearer token.
- **Response Format**: JSON with nested structures; requires flattening for processing.
- **Error Handling**: Retry on 401 (token refresh), 429, and 5xx errors.

## Example Configuration

```python
BASE_CONFIG = {
    "base_url": "https://api.solarvista.com/",
    "auth": {
        "type": "bearer",
        "token_url": "https://auth.solarvista.com/connect/token",
        "client_id": "{clientId}",
        "client_secret": "{clientSecret}",
        "grant_type": "password",
        "username": "{username}",
        "password": "{password}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/datagateway/v3/{account}/datasources/ref/users/data/query",
        "method": "POST",
        "data_selector": "rows",
        "primary_key": "userId",
        "pagination": {
            "type": "cursor",
            "cursor_param": "continuationToken"
        }
    },
    {
        "name": "work_items",
        "path": "/workflow/v4/{account}/workItems/search",
        "method": "POST",
        "data_selector": "items",
        "primary_key": "workItemId",
        "pagination": {
            "type": "cursor",
            "cursor_param": "continuationToken"
        },
        "incremental": {
            "cursor_path": "lastModified",
            "param_name": "lastModifiedAfter",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "work_item_history",
        "depends_on": "work_items",
        "param_mapping": {"workItemId": "workItemId"}
    },
    {
        "endpoint": "activity",
        "depends_on": "work_items",
        "param_mapping": {"workItemId": "workItemId"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Solarvista API using a dlt data pipeline. Adjust the configuration as needed based on specific requirements and API updates.

---
*dlt REST API Source Configuration Guide*