# Overview

This document provides a configuration guide for integrating with a third-party API using the dltHub REST API source. The integration allows for the extraction and synchronization of various resources such as assignments, checklist items, circles, custom fields, metrics, people, projects, and roles. The API supports data retrieval through a RESTful interface, utilizing API key-based authentication.

# Configuration Example

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
            "name": "assignments",
            "endpoint": {
                "path": "/v3/assignments",
                "data_selector": "assignments",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "checklist_items",
            "endpoint": {
                "path": "/v3/checklist_items",
                "data_selector": "checklist_items",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "circles",
            "endpoint": {
                "path": "/v3/circles",
                "data_selector": "circles",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v3/custom_fields",
                "data_selector": "custom_fields",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v3/metrics",
                "data_selector": "metrics",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "people",
            "endpoint": {
                "path": "/v3/people",
                "data_selector": "people",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v3/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v3/roles",
                "data_selector": "roles",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

The API uses API key-based authentication. The API key should be included in the request headers. The configuration requires the following parameters:

- `type`: "api_key"
- `name`: "api_key"
- `api_key`: Retrieved from `dlt.secrets["api_key"]`
- `location`: "header"

# Resources

Each resource corresponds to a specific endpoint and data structure. The following resources are available:

- **Assignments**
  - Endpoint Path: `/v3/assignments`
  - Data Selector: `assignments`
  - Primary Key: `id`
  - Pagination: Single page

- **Checklist Items**
  - Endpoint Path: `/v3/checklist_items`
  - Data Selector: `checklist_items`
  - Primary Key: `id`
  - Pagination: Single page

- **Circles**
  - Endpoint Path: `/v3/circles`
  - Data Selector: `circles`
  - Primary Key: `id`
  - Pagination: Single page

- **Custom Fields**
  - Endpoint Path: `/v3/custom_fields`
  - Data Selector: `custom_fields`
  - Primary Key: `id`
  - Pagination: Single page

- **Metrics**
  - Endpoint Path: `/v3/metrics`
  - Data Selector: `metrics`
  - Primary Key: `id`
  - Pagination: Single page

- **People**
  - Endpoint Path: `/v3/people`
  - Data Selector: `people`
  - Primary Key: `id`
  - Pagination: Single page

- **Projects**
  - Endpoint Path: `/v3/projects`
  - Data Selector: `projects`
  - Primary Key: `id`
  - Pagination: Single page

- **Roles**
  - Endpoint Path: `/v3/roles`
  - Data Selector: `roles`
  - Primary Key: `id`
  - Pagination: Single page

# Error Handling

The configuration does not specify custom error handling logic. It is recommended to implement retry logic for common HTTP status codes such as 429 (Too Many Requests) and 5xx (Server Errors) to ensure robust data extraction.

# Schema

The schema for each resource is defined with fields, types, and descriptions. The field names are neutral and generalized. For example:

- **Assignments**
  - `id`: Integer, unique identifier
  - `election`: String, election status
  - `exclude_from_meetings`: Boolean, exclusion flag
  - `focus`: String, focus of the assignment
  - `links`: Object, related links

- **Checklist Items**
  - `id`: Integer, unique identifier
  - `description`: String, description of the item
  - `frequency`: String, completion frequency
  - `global`: Boolean, global flag
  - `links`: Object, related links

- **Circles**
  - `id`: Integer, unique identifier
  - `name`: String, name of the circle
  - `organization_id`: Integer, organization identifier
  - `short_name`: String, abbreviation
  - `strategy`: String, strategy or purpose
  - `links`: Object, related links

- **Custom Fields**
  - `id`: Integer, unique identifier
  - `field_name`: String, name of the custom field
  - `field_value`: String, value of the custom field
  - `links`: Object, related links

- **Metrics**
  - `id`: Integer, unique identifier
  - `description`: String, description of the metric
  - `frequency`: String, recording frequency
  - `global`: Boolean, global flag
  - `links`: Object, related links

- **People**
  - `id`: Integer, unique identifier
  - `name`: String, name of the person
  - `email`: String, email address
  - `external_id`: Integer, external identifier
  - `links`: Object, related links

- **Projects**
  - `id`: Integer, unique identifier
  - `type`: String, category of the project
  - `description`: String, project description
  - `status`: String, current status
  - `links`: Object, related links

- **Roles**
  - `id`: Integer, unique identifier
  - `name`: String, name of the role
  - `purpose`: String, purpose of the role
  - `is_core`: Boolean, core role flag
  - `links`: Object, related links

This configuration provides a comprehensive setup for integrating with the API, ensuring data is retrieved and managed effectively.