# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as cards, collections, dashboards, users, databases, and native query snippets. The configuration is designed to be vendor-neutral and focuses on extracting data from the API using a session-based authentication mechanism.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "session_token",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"],
            "header": "X-Session-Token",
            "session_token": dlt.secrets["session_token"],
            "session_token_response_key": "id",
            "login_url": "session",
            "validate_session_url": "user/current"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "cards",
            "endpoint": {
                "path": "/v1/cards",
                "data_selector": "data"
            }
        },
        {
            "name": "collections",
            "endpoint": {
                "path": "/v1/collections",
                "data_selector": "data"
            }
        },
        {
            "name": "dashboards",
            "endpoint": {
                "path": "/v1/dashboards",
                "data_selector": "data"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data"
            }
        },
        {
            "name": "databases",
            "endpoint": {
                "path": "/v1/databases",
                "data_selector": "data"
            }
        },
        {
            "name": "native_query_snippets",
            "endpoint": {
                "path": "/v1/native-query-snippets",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

The integration uses session-based authentication. The following secrets or configuration values are required:

- `username`: The username for authentication.
- `password`: The password for authentication.
- `session_token`: A session token obtained from the API.

# Resources

Each resource is configured with a specific endpoint path and data selector:

- **Cards**: `/v1/cards`
- **Collections**: `/v1/collections`
- **Dashboards**: `/v1/dashboards`
- **Users**: `/v1/users`
- **Databases**: `/v1/databases`
- **Native Query Snippets**: `/v1/native-query-snippets`

# Error Handling

The configuration includes basic error handling with retry logic for common HTTP status codes. The retry mechanism is designed to handle temporary issues by retrying the request with exponential backoff.

# Schema

The schema for each resource is defined with neutral field names and types. The fields are designed to be flexible and accommodate various data types, including nullability. The schema is consistent with the input YAML and uses generalized field names.