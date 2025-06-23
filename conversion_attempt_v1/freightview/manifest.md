# Overview

This document provides a configuration example for integrating with a third-party logistics API using dltHub's REST API source format. The integration allows for the extraction and synchronization of shipping data, including quotes, tracking, and shipment reports, to facilitate efficient logistics management.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2.0",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/v2.0/auth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "shipments",
            "endpoint": {
                "path": "/shipments",
                "data_selector": "shipments",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "continuationToken",
                    "stop_condition": "response.get('continuationToken') is None"
                }
            }
        },
        {
            "name": "quotes",
            "endpoint": {
                "path": "/shipments/{{ stream_partition.shipmentId }}/quotes",
                "data_selector": "quotes"
            }
        },
        {
            "name": "tracking",
            "endpoint": {
                "path": "/shipments/{{ stream_partition.shipmentId }}/tracking",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/v2.0/auth/token`

## Resources

### Shipments
- **Endpoint Path**: `/shipments`
- **Pagination Strategy**: Cursor-based
  - **Cursor Path**: `continuationToken`
  - **Stop Condition**: `response.get('continuationToken') is None`
- **Data Selector**: `shipments`

### Quotes
- **Endpoint Path**: `/shipments/{{ stream_partition.shipmentId }}/quotes`
- **Data Selector**: `quotes`

### Tracking
- **Endpoint Path**: `/shipments/{{ stream_partition.shipmentId }}/tracking`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes with exponential backoff.
- **Known HTTP Response Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Quotes
- **Fields**:
  - `quoteId`: string
  - `amount`: number or null
  - `currency`: string or null
  - `createdDate`: string or null
  - Additional fields with types as specified in the schema

### Tracking
- **Fields**:
  - `createdDate`: string
  - `eventDate`: string or null
  - `eventType`: string or null
  - Additional fields with types as specified in the schema

### Shipments
- **Fields**:
  - `shipmentId`: string
  - `status`: string or null
  - `createdDate`: string or null
  - Additional fields with types as specified in the schema

This configuration provides a clean, vendor-neutral setup for integrating with a logistics API using dltHub's REST API source format, ensuring seamless data extraction and synchronization.