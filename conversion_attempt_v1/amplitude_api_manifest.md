# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data retrieval from various resources, such as annotations, cohorts, session lengths, active users, and events. The API supports basic authentication and handles error responses with retry logic.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["secret_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "annotations",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "cohorts",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "cohorts",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "average_session_length",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "active_users",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "events_list",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Your API key for authentication.
  - `secret_key`: Your secret key for authentication.

# Resources

### Annotations
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Single page

### Cohorts
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `cohorts`
- **Primary Key**: `id`
- **Pagination**: Single page

### Average Session Length
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `date`
- **Pagination**: Single page

### Active Users
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `date`
- **Pagination**: Single page

### Events List
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Single page

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 400, 403, 504
  - Maximum retries: 3
  - Backoff factor: 2

- **Known HTTP Response Codes**:
  - 400: Configuration error due to large data size.
  - 403: Access denied due to invalid credentials or permissions.
  - 404: No data available for the requested time range.
  - 504: Timeout due to large data volume.

# Schema

### Annotations
- **Fields**:
  - `date`: Date of annotation (string, nullable)
  - `details`: Additional details (string, nullable)
  - `id`: Unique identifier (integer, nullable)
  - `label`: Annotation label (string, nullable)

### Cohorts
- **Fields**:
  - `appId`: Application ID (integer, nullable)
  - `archived`: Archived status (boolean, nullable)
  - `definition`: Cohort definition (object, nullable)
  - `description`: Cohort description (string, nullable)
  - `id`: Unique identifier (string, nullable)
  - `name`: Cohort name (string, nullable)

### Average Session Length
- **Fields**:
  - `date`: Session date (string, nullable)
  - `length`: Session length in seconds (number, nullable)

### Active Users
- **Fields**:
  - `date`: Date of active user data (string, nullable)
  - `statistics`: Active user statistics (object, nullable)

### Events List
- **Fields**:
  - `id`: Event ID (number, required)
  - `name`: Event name (string, nullable)
  - `totals`: Event totals (number, nullable)

This configuration guide provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.