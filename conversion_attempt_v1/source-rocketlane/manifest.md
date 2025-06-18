# Overview

This document provides a configuration guide for integrating with a third-party project management API. The integration allows for seamless data synchronization of project, task, user, and other related data into various data warehouses or analytics platforms. This ensures real-time access to operational insights, enhancing project visibility and performance tracking across tools.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "tasks",
            "endpoint": {
                "path": "/api/1.0/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/api/1.0/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/api/1.0/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "fields",
            "endpoint": {
                "path": "/api/1.0/fields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "time-offs",
            "endpoint": {
                "path": "/api/1.0/time-offs",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "spaces",
            "endpoint": {
                "path": "/api/1.0/spaces",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "phases",
            "endpoint": {
                "path": "/api/1.0/phases",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "time-entries",
            "endpoint": {
                "path": "/api/1.0/time-entries",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        },
        {
            "name": "space-documents",
            "endpoint": {
                "path": "/api/1.0/space-documents",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "cursor_path": "response.pagination.nextPageToken",
                    "stop_condition": "response.pagination.hasMore is false"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

### Tasks
- **Endpoint Path**: `/api/1.0/tasks`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Users
- **Endpoint Path**: `/api/1.0/users`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Projects
- **Endpoint Path**: `/api/1.0/projects`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Fields
- **Endpoint Path**: `/api/1.0/fields`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Time-offs
- **Endpoint Path**: `/api/1.0/time-offs`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Spaces
- **Endpoint Path**: `/api/1.0/spaces`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Phases
- **Endpoint Path**: `/api/1.0/phases`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Time-entries
- **Endpoint Path**: `/api/1.0/time-entries`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

### Space-documents
- **Endpoint Path**: `/api/1.0/space-documents`
- **Pagination Strategy**: Cursor-based with `pageToken`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

- **Tasks**: Includes fields like `taskId`, `taskName`, `createdAt`, etc.
- **Users**: Includes fields like `userId`, `email`, `firstName`, etc.
- **Projects**: Includes fields like `projectId`, `projectName`, `startDate`, etc.
- **Fields**: Includes fields like `fieldId`, `fieldLabel`, `fieldType`, etc.
- **Time-offs**: Includes fields like `timeOffId`, `startDate`, `durationInMinutes`, etc.
- **Spaces**: Includes fields like `spaceId`, `spaceName`, `createdAt`, etc.
- **Phases**: Includes fields like `phaseId`, `phaseName`, `dueDate`, etc.
- **Time-entries**: Includes fields like `timeEntryId`, `date`, `minutes`, etc.
- **Space-documents**: Includes fields like `spaceDocumentId`, `spaceDocumentName`, `createdAt`, etc.

This configuration provides a comprehensive setup for integrating with the API, ensuring efficient data synchronization and management.