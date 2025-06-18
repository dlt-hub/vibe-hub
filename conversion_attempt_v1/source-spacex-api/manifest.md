# Overview

This document provides a configuration example for integrating with a third-party space exploration API using the dltHub REST API source format. The API provides access to various resources related to space missions, vehicles, and other related data. The integration supports fetching data from multiple endpoints, each representing a different resource.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v4/",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "launches",
            "endpoint": {
                "path": "/launches/latest",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "capsules",
            "endpoint": {
                "path": "/capsules",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "company",
            "endpoint": {
                "path": "/company",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "crew",
            "endpoint": {
                "path": "/crew",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "cores",
            "endpoint": {
                "path": "/cores",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "dragons",
            "endpoint": {
                "path": "/dragons",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "landpads",
            "endpoint": {
                "path": "/landpads",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "payloads",
            "endpoint": {
                "path": "/payloads",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "history",
            "endpoint": {
                "path": "/history",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "rockets",
            "endpoint": {
                "path": "/rockets",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "roadster",
            "endpoint": {
                "path": "/roadster",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "ships",
            "endpoint": {
                "path": "/ships",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "starlink",
            "endpoint": {
                "path": "/starlink",
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

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

Each resource corresponds to a specific endpoint in the API. The resources are configured with the following parameters:

- **Resource Name**: A descriptive name for the resource.
- **Endpoint Path**: The path to the resource endpoint.
- **HTTP Method**: Default is `GET`.
- **Data Selector**: Specifies the path to the data in the response.
- **Pagination Strategy**: All resources use a `single_page` pagination strategy, indicating that the data is retrieved in a single request.

## Error Handling

The configuration does not specify custom error handling logic. By default, the dltHub REST API source will handle common HTTP errors and retries.

## Schema

The schema for each resource is defined in a neutral format, with fields and types specified according to the JSON schema standard. The schema includes various data types such as strings, numbers, booleans, and arrays. Each field is described with a neutral name, avoiding any proprietary or branded terms.