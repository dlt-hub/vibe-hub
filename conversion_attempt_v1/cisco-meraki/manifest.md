# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and the data is retrieved using GET requests.

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
            "name": "datacenters",
            "endpoint": {
                "path": "/v1/organizations/{org_id}/datacenters",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "organization_saml",
            "endpoint": {
                "path": "/v1/organizations/{org_id}/saml",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.Link.split(',').trim().selectattr('contains', 'rel=next').regex_search('endingBefore=([^&]+)').first",
                    "stop_condition": "not headers.Link.string.regex_search('rel=next')"
                }
            }
        },
        {
            "name": "organization_admins",
            "endpoint": {
                "path": "/v1/organizations/{org_id}/admins",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.Link.split(',').trim().selectattr('contains', 'rel=next').regex_search('endingBefore=([^&]+)').first",
                    "stop_condition": "not headers.Link.string.regex_search('rel=next')"
                }
            }
        },
        {
            "name": "organization_devices",
            "endpoint": {
                "path": "/v1/organizations/{org_id}/wirelessController/overview/byDevice",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "organization_networks",
            "endpoint": {
                "path": "/v1/organizations/{org_id}/networks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "organization_apiRequests",
            "endpoint": {
                "path": "/v1/organizations/{org_id}/apiRequests",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.Link.split(',').trim().selectattr('contains', 'rel=next').regex_search('endingBefore=([^&]+)').first",
                    "stop_condition": "not headers.Link.string.regex_search('rel=next')"
                }
            }
        },
        {
            "name": "organization_network_settings",
            "endpoint": {
                "path": "/v1/networks/{net_id}/settings",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.Link.split(',').trim().selectattr('contains', 'rel=next').regex_search('endingBefore=([^&]+)').first",
                    "stop_condition": "not headers.Link.string.regex_search('rel=next')"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is defined with a unique name, endpoint path, and pagination strategy. The data selector is set to "data" for all resources.

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource`
- **Pagination Strategy**: Cursor-based with a stop condition

# Error Handling

- **Retry Logic**: Not explicitly defined in the YAML, but can be configured in dltHub with retry_on_status_codes and backoff_factor.
- **Known HTTP Response Codes**: Not specified in the YAML.

# Schema

The schema for each resource is defined with required fields and properties. Field names are generalized to be vendor-neutral.

- **Fields**: Abstracted from the original YAML
- **Types**: Consistent with the YAML schema definitions
- **Nullability**: As specified in the YAML

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.