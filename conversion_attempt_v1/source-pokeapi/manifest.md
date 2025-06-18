# Overview

This document provides a configuration example for integrating with a third-party API that provides data about various entities. The integration is designed to be vendor-neutral and follows the dltHub REST API source format. The API allows querying specific entities by name and returns detailed information about them.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "none"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "entity_data",
            "endpoint": {
                "path": "/v1/{entity_name}",
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

- Type: None
- No authentication is required for accessing this API.

## Resources

- **Resource Name**: entity_data
- **Endpoint Path**: `/v1/{entity_name}`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: data

## Error Handling

- The API does not specify any particular error handling or retry logic. It is recommended to implement standard HTTP error handling practices, such as retries for 5xx server errors.

## Schema

The schema for the data returned by the API includes various fields, such as:

- **id**: Integer, primary key
- **name**: String, name of the entity
- **abilities**: Array of objects, each containing:
  - **ability**: Object with properties `name` (string) and `url` (string)
  - **is_hidden**: Boolean
  - **slot**: Integer
- **base_experience**: Integer
- **forms**: Array of objects, each containing:
  - **name**: String
  - **url**: String
- **height**: Integer
- **weight**: Integer
- **types**: Array of objects, each containing:
  - **type**: Object with properties `name` (string) and `url` (string)
  - **slot**: Integer

The schema is designed to be flexible, allowing for additional properties and null values where applicable.