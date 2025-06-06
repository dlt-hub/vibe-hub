# Clickup REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for ClickUp

This document provides a comprehensive guide to configuring a dlt data pipeline for the ClickUp REST API. It includes details on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.clickup.com/api/v2/`

**Authentication:**
- **Type:** `api_key`
- **Location:** `header`
- **Parameter Name:** `Authorization`
- **Format:** `Bearer {api_token}`

## 2. Available Endpoints

### Teams
- **Path:** `/team`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array located at `$.teams[*]`
- **Primary Key:** `id`

### Time Entries
- **Path:** `/team/{team_id}/time_entries`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array located at `$.data[*]`
- **Primary Key:** `id`
- **Parent Endpoint:** Teams

### Spaces
- **Path:** `/team/{team_id}/space`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON array located at `$.spaces[*]`
- **Primary Key:** `id`
- **Parent Endpoint:** Teams

### Pagination Configuration
- **Type:** `page_number`
- **Parameter Names:** `page`
- **Default Page Size:** 100
- **Next Page Determination:** If the number of records equals the page size, increment the page number.

## 3. Incremental Data Loading

### Tasks
- **Path:** `/team/{team_id}/task`
- **Incremental Support:** Yes
- **Query Parameter:** `date_updated_gt`
- **Date Field:** `date_updated`
- **Format:** ISO 8601
- **Recommended Initial Value:** Current date minus a reasonable buffer period (e.g., 30 days)

## 4. Endpoint Dependencies

### Resource Relationships
- **Spaces** depend on **Teams**: Requires `team_id` from Teams.
- **Folders** depend on **Spaces**: Requires `space_id` from Spaces.
- **Folder Lists** depend on **Folders**: Requires `folder_id` from Folders.
- **Folderless Lists** depend on **Spaces**: Requires `space_id` from Spaces.
- **Tasks** depend on **Teams**: Requires `team_id` from Teams.

### Processing Order
1. Teams
2. Spaces
3. Folders
4. Folder Lists
5. Folderless Lists
6. Tasks

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute:** Not explicitly defined, but handle 429 status codes by waiting for the `X-RateLimit-Reset` header.
- **Burst Limits:** Implement exponential backoff with a maximum wait time of 60 seconds.

### Special Requirements
- **Custom Headers:** `User-Agent` can be specified in the configuration.
- **Response Format:** JSON
- **Error Handling:** Retry on 5xx errors and 429 status codes. Fatal errors for 4xx status codes except 429.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.clickup.com/api/v2/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {api_token}"
    }
}

ENDPOINTS = [
    {
        "name": "teams",
        "path": "/team",
        "method": "GET",
        "data_selector": "teams",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit": 100
        }
    },
    {
        "name": "tasks",
        "path": "/team/{team_id}/task",
        "method": "GET",
        "data_selector": "tasks",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "date_updated",
            "param_name": "date_updated_gt",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "spaces",
        "depends_on": "teams",
        "param_mapping": {"team_id": "id"}
    },
    {
        "endpoint": "folders",
        "depends_on": "spaces",
        "param_mapping": {"space_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate the ClickUp API into a dlt data pipeline, ensuring comprehensive data extraction and management.

---
*dlt REST API Source Configuration Guide*