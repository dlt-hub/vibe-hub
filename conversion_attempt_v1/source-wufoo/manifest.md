# Overview

This document provides a configuration example for integrating with a third-party form and report management API using the dltHub REST API source. The integration allows for the extraction of form entries, metadata, user information, and reports, facilitating seamless data synchronization with your data warehouse or analytical tools.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "forms",
            "endpoint": {
                "path": "/forms.json",
                "data_selector": "Forms"
            }
        },
        {
            "name": "form_comments",
            "endpoint": {
                "path": "/forms/{form_identifier}/comments.json",
                "data_selector": "Comments",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "pageStart"
                }
            }
        },
        {
            "name": "form_fields",
            "endpoint": {
                "path": "/forms/{form_identifier}/fields.json",
                "data_selector": "Fields"
            }
        },
        {
            "name": "form_entries",
            "endpoint": {
                "path": "/forms/{form_identifier}/entries.json",
                "data_selector": "Entries",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "pageStart"
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/reports.json",
                "data_selector": "Reports"
            }
        },
        {
            "name": "report_entries",
            "endpoint": {
                "path": "/reports/{report_identifier}/entries.json",
                "data_selector": "Entries"
            }
        },
        {
            "name": "report_fields",
            "endpoint": {
                "path": "/reports/{report_identifier}/fields.json",
                "data_selector": "Fields"
            }
        },
        {
            "name": "report_widgets",
            "endpoint": {
                "path": "/reports/{report_identifier}/widgets.json",
                "data_selector": "Widgets"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users.json",
                "data_selector": "Users"
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Username**: API Key (stored securely in `dlt.secrets["api_key"]`)

## Resources

### Forms
- **Endpoint Path**: `/forms.json`
- **Data Selector**: `Forms`
- **Primary Key**: `Hash`

### Form Comments
- **Endpoint Path**: `/forms/{form_identifier}/comments.json`
- **Data Selector**: `Comments`
- **Primary Key**: `CommentId`
- **Pagination**: Offset-based with `pageStart` parameter

### Form Fields
- **Endpoint Path**: `/forms/{form_identifier}/fields.json`
- **Data Selector**: `Fields`

### Form Entries
- **Endpoint Path**: `/forms/{form_identifier}/entries.json`
- **Data Selector**: `Entries`
- **Primary Key**: `EntryId`
- **Pagination**: Offset-based with `pageStart` parameter

### Reports
- **Endpoint Path**: `/reports.json`
- **Data Selector**: `Reports`
- **Primary Key**: `Hash`

### Report Entries
- **Endpoint Path**: `/reports/{report_identifier}/entries.json`
- **Data Selector**: `Entries`

### Report Fields
- **Endpoint Path**: `/reports/{report_identifier}/fields.json`
- **Data Selector**: `Fields`

### Report Widgets
- **Endpoint Path**: `/reports/{report_identifier}/widgets.json`
- **Data Selector**: `Widgets`
- **Primary Key**: `Hash`

### Users
- **Endpoint Path**: `/users.json`
- **Data Selector**: `Users`
- **Primary Key**: `Hash`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with fields and types, ensuring data consistency and integrity. Field names are generalized to maintain neutrality. For example, `Hash` is used as a primary key in several resources.