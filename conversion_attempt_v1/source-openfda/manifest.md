# Overview

This document provides a configuration guide for integrating with a third-party analytics API that offers access to various structured datasets, including adverse events, product labeling, and recall enforcement reports. The API supports fetching data from resources such as animal and veterinary adverse events, food adverse events, and more.

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "animal_veterinary_adverse_events",
            "endpoint": {
                "path": "/v1/animalandveterinary/event.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "tobacco_problems_reports",
            "endpoint": {
                "path": "/v1/tobacco/problem.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "food_adverse_events",
            "endpoint": {
                "path": "/v1/food/event.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "food_enforcement_reports",
            "endpoint": {
                "path": "/v1/food/enforcement.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "drug_adverse_events",
            "endpoint": {
                "path": "/v1/drug/event.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "drug_product_labelling",
            "endpoint": {
                "path": "/v1/drug/label.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "drug_ndc_library",
            "endpoint": {
                "path": "/v1/drug/ndc.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "drug_recall_enforcement_reports",
            "endpoint": {
                "path": "/v1/drug/enforcement.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "drugs",
            "endpoint": {
                "path": "/v1/drug/drugsfda.json",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "skip"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path and Method**: Each resource has a specific path and uses the GET method.
- **Pagination Strategy**: Offset-based pagination with parameters `limit` and `skip`.
- **Data Selector**: The field path to extract data is `results`.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.
- **Fallback/Handling Behavior**: Generalized error handling to ensure robust data fetching.

# Schema

- **Fields, Types, Nullability**: Use neutral field names and types as defined in the schema.
- **Example Fields**: `unique_aer_id_number`, `report_id`, `recall_number`, etc., are used as primary keys for different resources.