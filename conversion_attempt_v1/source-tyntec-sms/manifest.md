# Overview

This document provides a configuration example for integrating with a third-party messaging API using the dltHub REST API source. The integration allows for the retrieval of various resources such as SMS messages, contacts, phone numbers, and provisioning requests. The API supports authentication via API key and uses GET requests to access different endpoints.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "requestId",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "sms",
            "endpoint": {
                "path": "/messaging/v1/sms",
                "params": {
                    "to": "{{ config['to'] }}",
                    "from": "{{ config['from'] }}",
                    "message": "{{ config['message'] or ''}}"
                },
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/messaging/v1/messages/{{ stream_slice.requestId }}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/byon/contacts/v1",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "phones",
            "endpoint": {
                "path": "/byon/phonebook/v1/numbers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "registrations",
            "endpoint": {
                "path": "/byon/provisioning/v1",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication, stored securely in dlt secrets.
  - Injected into the request header.

# Resources

- **Resource Name**: `sms`, `messages`, `contacts`, `phones`, `registrations`
- **Endpoint Path and Method**: 
  - `/messaging/v1/sms`
  - `/messaging/v1/messages/{{ stream_slice.requestId }}`
  - `/byon/contacts/v1`
  - `/byon/phonebook/v1/numbers`
  - `/byon/provisioning/v1`
  - All use the GET method.
- **Pagination Strategy**: Single page (no pagination).
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML, but can be configured in dltHub if needed.
- **Known HTTP Response Codes**: Not specified, but typical codes like 429, 500, 502, 503, 504 can be handled with retries.
- **Fallback/Handling Behavior**: Generalized error handling can be implemented using dltHub's error handler configuration.

# Schema

- **Fields**: 
  - `requestId`, `doneDate`, `errorCode`, `errorReason`, `from`, `href`, `mccmnc`, `overallPrice`, `parts`, `priceEffective`, `reference`, `sentDate`, `size`, `status`, `submitDate`, `to`, `ttid`
- **Types**: String, integer, array, object, with nullability.
- **Use Neutral Field Names**: Field names are generalized and do not include any proprietary identifiers.