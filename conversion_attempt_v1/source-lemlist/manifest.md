# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, including team data, campaigns, activities, and unsubscribes. The API uses an API key for authentication and supports pagination through request parameters.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "access_token",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "team_data",
            "endpoint": {
                "path": "/v1/team",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "campaigns_data",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "activities_data",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "unsubscribes_data",
            "endpoint": {
                "path": "/v1/unsubscribes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Injection Location**: The API key is injected into the query parameters of the request.

# Resources

### Team Data
- **Resource Name**: `team_data`
- **Endpoint Path**: `/v1/team`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Campaigns Data
- **Resource Name**: `campaigns_data`
- **Endpoint Path**: `/v1/campaigns`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Activities Data
- **Resource Name**: `activities_data`
- **Endpoint Path**: `/v1/activities`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Unsubscribes Data
- **Resource Name**: `unsubscribes_data`
- **Endpoint Path**: `/v1/unsubscribes`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3 retries with an exponential backoff factor of 2.

# Schema

The schema for each resource is defined with neutral field names and types. Below is an example schema for the `team_data` resource:

```json
{
  "type": "object",
  "$schema": "http://json-schema.org/draft-07/schema#",
  "additionalProperties": true,
  "properties": {
    "_id": {
      "type": ["null", "string"]
    },
    "_updatedAt": {
      "type": ["null", "string"],
      "format": "%Y-%m-%dT%H:%M:%S.%fZ"
    },
    "name": {
      "type": ["null", "string"]
    },
    "createdAt": {
      "type": ["null", "string"],
      "format": "%Y-%m-%dT%H:%M:%S.%fZ"
    }
  }
}
```

Each resource has a similar schema structure, with fields specific to the data it represents. The schemas are designed to be flexible, allowing for additional properties as needed.