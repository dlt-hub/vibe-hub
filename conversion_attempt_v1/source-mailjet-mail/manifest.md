# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, each with specific endpoints and pagination strategies.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/REST",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["api_key_secret"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "contacts_list",
            "endpoint": {
                "path": "/contactslist",
                "data_selector": "Data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contact",
                "data_selector": "Data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "stats_api_lifetime_message",
            "endpoint": {
                "path": "/statcounters?CounterSource=APIKey&CounterResolution=Lifetime&CounterTiming=Message",
                "data_selector": "Data"
            }
        },
        {
            "name": "campaign",
            "endpoint": {
                "path": "/campaign",
                "data_selector": "Data"
            }
        },
        {
            "name": "message",
            "endpoint": {
                "path": "/message?ShowSubject=true",
                "data_selector": "Data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "list_recipient",
            "endpoint": {
                "path": "/listrecipient",
                "data_selector": "Data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Your API Key
  - `api_key_secret`: Your API Secret Key

## Resources

### Contacts List
- **Endpoint Path**: `/contactslist`
- **Pagination**: Offset-based with `limit` of 100 and `offset` parameter
- **Data Selector**: `Data`

### Contacts
- **Endpoint Path**: `/contact`
- **Pagination**: Offset-based with `limit` of 100 and `offset` parameter
- **Data Selector**: `Data`

### Stats API Lifetime Message
- **Endpoint Path**: `/statcounters?CounterSource=APIKey&CounterResolution=Lifetime&CounterTiming=Message`
- **Data Selector**: `Data`

### Campaign
- **Endpoint Path**: `/campaign`
- **Data Selector**: `Data`

### Message
- **Endpoint Path**: `/message?ShowSubject=true`
- **Pagination**: Offset-based with `limit` of 100 and `offset` parameter
- **Data Selector**: `Data`

### List Recipient
- **Endpoint Path**: `/listrecipient`
- **Pagination**: Offset-based with `limit` of 100 and `offset` parameter
- **Data Selector**: `Data`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields such as `ID`, `Name`, `CreatedAt`, and others, depending on the resource. All fields are generalized and follow a neutral naming convention. The schema supports additional properties to accommodate any extra fields returned by the API.