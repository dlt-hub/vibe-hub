# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API's utilization endpoints, allowing for efficient data retrieval and management.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "StartTime",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "utilizations",
            "endpoint": {
                "path": "/v1/standalone/utilizations",
                "data_selector": "Records",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "pageNum",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "bucket_utilizations",
            "endpoint": {
                "path": "/v1/standalone/utilizations/bucket",
                "data_selector": "Records",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "pageNum",
                    "limit_param": "pageSize"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`. It is injected into the request header under the name "Authorization".

## Resources

### Utilizations

- **Endpoint Path**: `/v1/standalone/utilizations`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number-based pagination with parameters `pageNum` and `pageSize`.
- **Data Selector**: `Records`

### Bucket Utilizations

- **Endpoint Path**: `/v1/standalone/utilizations/bucket`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number-based pagination with parameters `pageNum` and `pageSize`.
- **Data Selector**: `Records`

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors, such as network issues or rate limiting.
- **HTTP Status Codes**: Configure retries for common HTTP error codes like 429 (Too Many Requests) and 5xx server errors.
- **Backoff Strategy**: Use exponential backoff to manage retry attempts.

## Schema

### Utilizations

- **Fields**:
  - `AcctNum`: number or null
  - `EndTime`: string or null
  - `StartTime`: string
  - `CreateTime`: string or null
  - `AcctPlanNum`: number or null
  - `DeleteBytes`: number or null
  - `NumAPICalls`: number or null
  - `NumGETCalls`: number or null
  - `NumPUTCalls`: number or null
  - `UploadBytes`: number or null
  - `NumHEADCalls`: number or null
  - `NumLISTCalls`: number or null
  - `DownloadBytes`: number or null
  - `NumDELETECalls`: number or null
  - `UtilizationNum`: number or null
  - `StorageReadBytes`: number or null
  - `StorageWroteBytes`: number or null
  - `NumBillableObjects`: number or null
  - `RawStorageSizeBytes`: number or null
  - `RegionalUtilizations`: object or null

### Bucket Utilizations

- **Fields**:
  - `Bucket`: string or null
  - `Region`: string or null
  - `AcctNum`: number or null
  - `EndTime`: string or null
  - `BucketNum`: number or null
  - `StartTime`: string
  - `CreateTime`: string or null
  - `AcctPlanNum`: number or null
  - `DeleteBytes`: number or null
  - `NumAPICalls`: number or null
  - `NumGETCalls`: number or null
  - `NumPUTCalls`: number or null
  - `UploadBytes`: number or null
  - `NumHEADCalls`: number or null
  - `NumLISTCalls`: number or null
  - `DownloadBytes`: number or null
  - `NumDELETECalls`: number or null
  - `StorageReadBytes`: number or null
  - `StorageWroteBytes`: number or null
  - `NumBillableObjects`: number or null
  - `RawStorageSizeBytes`: number or null
  - `BucketUtilizationNum`: number or null

This configuration ensures a clean and efficient integration with the third-party analytics API, adhering to dltHub's REST API source format.