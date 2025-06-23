# Overview

This document provides a configuration example for integrating with a third-party employee management API using the dltHub REST API source. The API allows for the retrieval of various resources such as locations, employees, departments, and more. The integration supports data synchronization through a RESTful interface, utilizing bearer token authentication for secure access.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "locations",
            "endpoint": {
                "path": "/api/v1/resource/Company",
                "data_selector": "data"
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/api/v1/supervise/employee",
                "data_selector": "data"
            }
        },
        {
            "name": "award_lists",
            "endpoint": {
                "path": "/api/v1/payroll/listAwardsLibrary",
                "data_selector": "data"
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/api/v1/resource/OperationalUnit",
                "data_selector": "data"
            }
        },
        {
            "name": "timesheets",
            "endpoint": {
                "path": "/api/v1/my/timesheets",
                "data_selector": "data"
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/api/v1/my/tasks",
                "data_selector": "data"
            }
        },
        {
            "name": "news_feed",
            "endpoint": {
                "path": "/api/v1/my/memo",
                "data_selector": "data"
            }
        },
        {
            "name": "addresses",
            "endpoint": {
                "path": "/api/v1/resource/Address",
                "data_selector": "data"
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/api/v1/resource/Category",
                "data_selector": "data"
            }
        },
        {
            "name": "comments",
            "endpoint": {
                "path": "/api/v1/resource/Comment",
                "data_selector": "data"
            }
        },
        {
            "name": "company_periods",
            "endpoint": {
                "path": "/api/v1/resource/CompanyPeriod",
                "data_selector": "data"
            }
        },
        {
            "name": "employee_agreements",
            "endpoint": {
                "path": "/api/v1/resource/EmployeeAgreement",
                "data_selector": "data"
            }
        },
        {
            "name": "employee",
            "endpoint": {
                "path": "/api/v1/resource/Employee",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

The API uses bearer token authentication. The token should be stored securely and accessed via `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Each resource has a specific endpoint path for data retrieval.
- **HTTP Method**: All endpoints use the GET method.
- **Data Selector**: The data is extracted from the response using the specified data selector.

# Error Handling

The configuration does not specify error handling, but it is recommended to implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504. A typical error handler might include a maximum number of retries and a backoff factor.

# Schema

The schema for each resource is defined in a generalized manner, with fields such as `id`, `name`, and other relevant attributes. The schema is designed to be flexible, allowing for additional properties as needed.