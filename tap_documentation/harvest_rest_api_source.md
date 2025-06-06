# Harvest REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Harvest API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.harvestapp.com/v2/`

**Authentication:**
- Type: `oauth2`
- Token URL: `https://id.getharvest.com/api/v2/oauth2/token`
- Required Scopes: Not explicitly mentioned, but typically includes access to resources like `time_entries`, `projects`, etc.
- Parameters:
  - `client_id`: Your Harvest application client ID
  - `client_secret`: Your Harvest application client secret
  - `refresh_token`: Token used to obtain a new access token
  - `user_agent`: Custom user agent string for requests

## 2. Available Endpoints

### Users Endpoint

**Endpoint Details:**
- Path: `/users`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `updated_since` for incremental loading
- Response Data Structure: JSON array of user objects

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names: `page`
- Default Page Size: Not specified, but typically 100
- Next Page Determination: `next_page` field in response

**Data Extraction:**
- JSONPath: `data`
- Primary Key: `id`
- Key Fields: `id`, `updated_at`

### Time Entries Endpoint

**Endpoint Details:**
- Path: `/time_entries`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `updated_since` for incremental loading
- Response Data Structure: JSON array of time entry objects

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names: `page`
- Default Page Size: Not specified
- Next Page Determination: `next_page` field in response

**Data Extraction:**
- JSONPath: `data`
- Primary Key: `id`
- Key Fields: `id`, `updated_at`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `updated_since`
- Date/Timestamp Field: `updated_at`
- Expected Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Value: Use the `start_date` from configuration

## 4. Endpoint Dependencies

**Resource Relationships:**
- `user_projects` depends on `users`
- `project_tasks` depends on `projects` and `tasks`
- `time_entries` may reference `users`, `projects`, `tasks`, etc.

**Mapping Identifiers:**
- Use `id` fields to map between parent and child resources

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests: Limited to 100 requests per 15 seconds
- Burst Limits: Implement exponential backoff for retries

**Special Requirements:**
- Required Headers: 
  - `Authorization: Bearer {access_token}`
  - `Harvest-Account-Id: {account_id}`
  - `User-Agent: {user_agent}`
- Response Format: JSON
- Error Handling: Implement retries with backoff for 5xx errors and handle 4xx errors gracefully

## Example Configuration

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.harvestapp.com/v2/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://id.getharvest.com/api/v2/oauth2/token",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret",
        "refresh_token": "your_refresh_token",
        "user_agent": "your_user_agent"
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
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_since",
            "format": "iso"
        }
    },
    {
        "name": "time_entries",
        "path": "/time_entries",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
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
        "endpoint": "user_projects",
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    },
    {
        "endpoint": "project_tasks",
        "depends_on": ["projects", "tasks"],
        "param_mapping": {"project_id": "id", "task_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Harvest API using a dlt data pipeline. Adjust the configuration as needed based on specific project requirements and API changes.

---
*dlt REST API Source Configuration Guide*