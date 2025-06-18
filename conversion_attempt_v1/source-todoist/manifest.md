# Overview

This document provides a configuration guide for integrating with a third-party task management API using dltHub's REST API source format. The integration allows for the extraction of task and project data, supporting synchronization of these resources into your data pipeline.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
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
                "path": "/v1/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Stored securely in `dlt.secrets` under the key `api_token`.

## Resources

### Tasks

- **Resource Name**: tasks
- **Endpoint Path**: `/v1/tasks`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Projects

- **Resource Name**: projects
- **Endpoint Path**: `/v1/projects`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Tasks

- **description**: Additional details or notes about the task
- **assignee_id**: The unique identifier of the user assigned to the task
- **assigner_id**: The unique identifier of the user who assigned the task
- **comment_count**: The count of comments on the task
- **content**: The title or description of the task
- **created_at**: The date and time when the task was created
- **creator_id**: The unique identifier of the user who created the task
- **due**: The due date and time of the task
- **duration**: The estimated duration or time required to complete the task
- **id**: The unique identifier of the task
- **is_completed**: Indicates whether the task is completed or not (true/false)
- **labels**: List of labels associated with the task
- **order**: The position or order of the task within a project or section
- **parent_id**: The unique identifier of the parent task if this task is a subtask
- **priority**: The priority level of the task (e.g., high, medium, low)
- **project_id**: The unique identifier of the project to which the task belongs
- **section_id**: The unique identifier of the section within a project where the task is located
- **url**: The URL link to view the task details

### Projects

- **color**: The color associated with the project
- **comment_count**: The number of comments on the project
- **id**: The unique identifier for the project
- **is_favorite**: Indicates if the project is marked as favorite
- **is_inbox_project**: Specifies if the project is the inbox project
- **is_shared**: Indicates if the project is shared with others
- **is_team_inbox**: Specifies if the project is a team inbox
- **name**: The name or title of the project
- **order**: The order or priority of the project in the list
- **parent_id**: The ID of the parent project if this is a subproject
- **url**: The URL for accessing the project
- **view_style**: The style or layout for viewing the project

This configuration provides a clean and vendor-neutral setup for integrating with a task management API, ensuring seamless data extraction and synchronization.