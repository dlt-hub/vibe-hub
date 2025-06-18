# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various resources such as users, groups, and conversations, with OAuth2 authentication and cursor-based pagination.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://login.example.com/oauth2/token",
            "grant_type": "client_credentials",
            "scope": "https://api.example.com/.default"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/resource/users",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('@odata.nextLink', {}) }}",
                    "stop_condition": "{{ not response.get('@odata.nextLink', {}) }}"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v1/resource/groups",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('@odata.nextLink', {}) }}",
                    "stop_condition": "{{ not response.get('@odata.nextLink', {}) }}"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: `https://login.example.com/oauth2/token`
- **Grant Type**: `client_credentials`
- **Scope**: `https://api.example.com/.default`

## Resources

### Users

- **Endpoint Path**: `/v1/resource/users`
- **Data Selector**: `value`
- **Pagination Strategy**: Cursor-based
  - **Cursor Value**: `{{ response.get('@odata.nextLink', {}) }}`
  - **Stop Condition**: `{{ not response.get('@odata.nextLink', {}) }}`

### Groups

- **Endpoint Path**: `/v1/resource/groups`
- **Data Selector**: `value`
- **Pagination Strategy**: Cursor-based
  - **Cursor Value**: `{{ response.get('@odata.nextLink', {}) }}`
  - **Stop Condition**: `{{ not response.get('@odata.nextLink', {}) }}`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **Known HTTP Response Codes**: 404, 403, 503
- **Fallback Behavior**: Ignore specific error messages and continue processing

## Schema

- **Fields**: Abstracted to neutral names such as `customer_id`, `user_key`
- **Types**: Include string, integer, boolean, and date-time formats
- **Nullability**: Fields can be nullable as specified in the schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format. It supports OAuth2 authentication, cursor-based pagination, and error handling for robust data extraction.