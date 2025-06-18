# Overview

This document provides a configuration guide for integrating with a third-party identity and access management API using dltHub's REST API source. The integration allows for secure and automated data synchronization of user, group, and directory information, facilitating streamlined identity workflows and up-to-date access control within applications.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1.0",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://login.example.com/oauth2/v2.0/token",
            "scopes": ["https://api.example.com/.default"]
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
                "path": "/users",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/groups",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        },
        {
            "name": "applications",
            "endpoint": {
                "path": "/applications",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        },
        {
            "name": "user_owned_deleted_items",
            "endpoint": {
                "path": "/directory/deletedItems/getUserOwnedObjects",
                "method": "POST",
                "data_selector": "value",
                "body": {
                    "userId": dlt.secrets["user_id"]
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        },
        {
            "name": "directoryroles",
            "endpoint": {
                "path": "/directoryroles",
                "data_selector": "value"
            }
        },
        {
            "name": "directoryroletemplates",
            "endpoint": {
                "path": "/directoryRoleTemplates",
                "data_selector": "value"
            }
        },
        {
            "name": "directoryaudits",
            "endpoint": {
                "path": "/auditLogs/directoryaudits",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        },
        {
            "name": "serviceprincipals",
            "endpoint": {
                "path": "/servicePrincipals",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        },
        {
            "name": "identityproviders",
            "endpoint": {
                "path": "/identity/identityProviders/availableProviderTypes",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        },
        {
            "name": "adminconsentrequestpolicy",
            "endpoint": {
                "path": "/policies/adminConsentRequestPolicy",
                "data_selector": "value",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "$skiptoken",
                    "cursor_path": "@odata.nextLink",
                    "stop_condition": "response.get('@odata.nextLink') is None"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://login.example.com/oauth2/v2.0/token`
- **Scopes**: `["https://api.example.com/.default"]`

# Resources

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Derived from the original paths
- **HTTP Method**: Default is GET, with POST for specific resources
- **Pagination Strategy**: Cursor-based using `$skiptoken` and `@odata.nextLink`
- **Data Selector**: `value`

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500)
- **Backoff Strategy**: Exponential backoff with a configurable factor
- **Timeouts**: Generalized handling for request timeouts

# Schema

- **Fields**: Derived from the original schema definitions
- **Types**: Generalized to standard data types
- **Nullability**: Preserved as per the original schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party identity and access management API using dltHub's REST API source. All parameters and patterns are directly sourced from the provided YAML configuration, ensuring a faithful and compliant transformation.