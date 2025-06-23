# Overview

This document provides a configuration example for integrating with a third-party educational platform's API. The integration allows for the synchronization of various educational resources such as courses, teachers, students, announcements, coursework, and student submissions. The API supports OAuth2 authentication and uses cursor-based pagination for data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://oauth2.example.com/token",
            "refresh_token": dlt.secrets["client_refresh_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "courses",
            "endpoint": {
                "path": "/v1/courses",
                "data_selector": "courses",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "teachers",
            "endpoint": {
                "path": "/v1/courses/{course}/teachers",
                "data_selector": "teachers",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "students",
            "endpoint": {
                "path": "/v1/courses/{course}/students",
                "data_selector": "students",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "announcements",
            "endpoint": {
                "path": "/v1/courses/{course}/announcements",
                "data_selector": "announcements",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "coursework",
            "endpoint": {
                "path": "/v1/courses/{course}/courseWork",
                "data_selector": "courseWork",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "studentsubmissions",
            "endpoint": {
                "path": "/v1/courses/{course}/courseWork/{coursework}/studentSubmissions",
                "data_selector": "studentSubmissions",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `client_id`
- **Client Secret**: `client_secret`
- **Token URL**: `https://oauth2.example.com/token`
- **Refresh Token**: `client_refresh_token`

# Resources

- **Courses**
  - **Endpoint Path**: `/v1/courses`
  - **Data Selector**: `courses`
  - **Pagination**: Cursor-based using `pageToken`

- **Teachers**
  - **Endpoint Path**: `/v1/courses/{course}/teachers`
  - **Data Selector**: `teachers`
  - **Pagination**: Cursor-based using `pageToken`

- **Students**
  - **Endpoint Path**: `/v1/courses/{course}/students`
  - **Data Selector**: `students`
  - **Pagination**: Cursor-based using `pageToken`

- **Announcements**
  - **Endpoint Path**: `/v1/courses/{course}/announcements`
  - **Data Selector**: `announcements`
  - **Pagination**: Cursor-based using `pageToken`

- **Coursework**
  - **Endpoint Path**: `/v1/courses/{course}/courseWork`
  - **Data Selector**: `courseWork`
  - **Pagination**: Cursor-based using `pageToken`

- **Student Submissions**
  - **Endpoint Path**: `/v1/courses/{course}/courseWork/{coursework}/studentSubmissions`
  - **Data Selector**: `studentSubmissions`
  - **Pagination**: Cursor-based using `pageToken`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Courses**: Includes fields like `id`, `name`, `ownerId`, etc.
- **Teachers**: Includes fields like `userId`, `profile`, etc.
- **Students**: Includes fields like `userId`, `profile`, etc.
- **Announcements**: Includes fields like `id`, `text`, `state`, etc.
- **Coursework**: Includes fields like `id`, `title`, `state`, etc.
- **Student Submissions**: Includes fields like `id`, `state`, `userId`, etc.

This configuration provides a comprehensive setup for integrating with the educational platform's API, ensuring efficient data synchronization and management.