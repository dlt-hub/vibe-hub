# Overview

This document provides a configuration setup for integrating with a third-party recruitment API. The API allows for the retrieval of various resources related to recruitment processes, such as candidates, job applications, and departments. The integration supports data synchronization through a RESTful API, utilizing pagination and authentication mechanisms.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "api",
            "api_key": dlt.secrets["api"],
            "location": "header"
        },
        "headers": {
            "X-Api-Version": dlt.secrets["x_api_version"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "candidates",
            "endpoint": {
                "path": "/candidates",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/custom-fields",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/departments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "jobs",
            "endpoint": {
                "path": "/jobs",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "job_applications",
            "endpoint": {
                "path": "/job-applications",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "job_offers",
            "endpoint": {
                "path": "/job-offers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "todos",
            "endpoint": {
                "path": "/todos",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "team_memberships",
            "endpoint": {
                "path": "/team-memberships",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "stages",
            "endpoint": {
                "path": "/stages",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/roles",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "regions",
            "endpoint": {
                "path": "/regions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "referrals",
            "endpoint": {
                "path": "/referrals",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "questions",
            "endpoint": {
                "path": "/questions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/notes",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "nps_responses",
            "endpoint": {
                "path": "/nps-responses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 30,
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Secrets**: 
  - `api`: The API key for authentication.
  - `x_api_version`: The version of the API to be used.

# Resources

Each resource corresponds to a specific endpoint in the API. The resources are configured with pagination to handle large datasets.

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Derived from the original paths.
- **Pagination Strategy**: Page number-based pagination with parameters for page number and page size.
- **Data Selector**: The field path to extract data from the API response.

# Error Handling

- **Retry Logic**: Implement retry mechanisms for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests) and 500-series server errors.
- **Backoff Strategy**: Exponential backoff for retries.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is `id`, ensuring uniqueness and consistency across data records.