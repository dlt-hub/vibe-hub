# Overview

This document provides a configuration guide for integrating with a third-party dataset API. The integration allows for importing datasets and their respective splits, utilizing a REST API. The configuration supports pagination and includes error handling mechanisms to ensure robust data retrieval.

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
        "primary_key": "key",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "rows",
            "endpoint": {
                "path": "/v1/resource?dataset={{ config['dataset_name'] }}&config={{ stream_partition.key.split('::')[1] }}&split={{ stream_partition.key.split('::')[2] }}",
                "data_selector": "rows",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "splits",
            "endpoint": {
                "path": "/v1/resource?dataset={{ config['dataset_name'] }}",
                "data_selector": "splits"
            }
        }
    ]
})
```

## Authentication

- **Type**: None
- This API does not require authentication for accessing the datasets.

## Resources

### Rows

- **Endpoint Path**: `/v1/resource?dataset={{ config['dataset_name'] }}&config={{ stream_partition.key.split('::')[1] }}&split={{ stream_partition.key.split('::')[2] }}`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 100
  - **Offset Parameter**: `offset`
- **Data Selector**: `rows`

### Splits

- **Endpoint Path**: `/v1/resource?dataset={{ config['dataset_name'] }}`
- **HTTP Method**: GET
- **Data Selector**: `splits`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Rows

- **Fields**:
  - `config`: string or null
  - `row`: object or null
    - `messages`: array or null
      - `content`: string or null
      - `role`: string or null
    - `tokens`: number or null
  - `row_idx`: number or null
  - `truncated_cells`: array or null

### Splits

- **Fields**:
  - `config`: string or null
  - `dataset`: string or null
  - `key`: string or null
  - `split`: string or null

This configuration provides a robust framework for accessing and importing datasets from a third-party API, with support for pagination and error handling to ensure reliable data integration.