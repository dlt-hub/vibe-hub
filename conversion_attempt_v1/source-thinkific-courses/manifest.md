# Overview

This document provides a configuration example for integrating with a third-party educational platform's API to retrieve course-related information. The integration supports fetching data about courses, course chapters, and content within chapters using a REST API. The API requires authentication via an API key and supports pagination for data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/public/v1/",
        "auth": {
            "type": "api_key",
            "name": "X-Auth-API-Key",
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
            "name": "courses",
            "endpoint": {
                "path": "/courses",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "course_chapters",
            "endpoint": {
                "path": "/courses/{course_id}/chapters",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 250,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "contents",
            "endpoint": {
                "path": "/chapters/{chapter_id}/contents",
                "data_selector": "items",
                "paginator": {
                    "type": "offset",
                    "limit": 250,
                    "page_param": "page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `X-Auth-API-Key`
- **Secret Key**: `api_key`
- **Location**: Header

# Resources

### Courses
- **Endpoint Path**: `/courses`
- **HTTP Method**: GET
- **Pagination Strategy**: Page Number
  - **Limit**: 250
  - **Page Parameter**: `page`
  - **Start From Page**: 1
- **Data Selector**: `items`

### Course Chapters
- **Endpoint Path**: `/courses/{course_id}/chapters`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 250
  - **Page Parameter**: `page`
- **Data Selector**: `items`

### Contents
- **Endpoint Path**: `/chapters/{chapter_id}/contents`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 250
  - **Page Parameter**: `page`
- **Data Selector**: `items`

# Error Handling

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

### Courses
- **Fields**:
  - `description`: string or null
  - `administrator_user_ids`: array of numbers or null
  - `banner_image_url`: string or null
  - `chapter_ids`: array of numbers or null
  - `contact_information`: string or null
  - `course_card_image_url`: string or null
  - `id`: number or null
  - `instructor_id`: number or null
  - `name`: string or null
  - `product_id`: number or null
  - `reviews_enabled`: boolean or null
  - `slug`: string or null
  - `user_id`: number or null

### Course Chapters
- **Fields**:
  - `content_ids`: array of numbers or null
  - `duration_in_seconds`: number or null
  - `id`: number or null
  - `name`: string or null
  - `position`: number or null

### Contents
- **Fields**:
  - `chapter_id`: number or null
  - `contentable_type`: string or null
  - `free`: boolean or null
  - `id`: number or null
  - `name`: string or null
  - `position`: number or null
  - `take_url`: string or null

This configuration provides a structured approach to accessing and synchronizing data from a third-party educational platform's API, ensuring efficient data retrieval and integration.