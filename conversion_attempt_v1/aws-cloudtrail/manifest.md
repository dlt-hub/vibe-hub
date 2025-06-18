# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports incremental synchronization based on event timestamps and utilizes a cursor-based pagination strategy.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "custom",
            "name": "aws_auth",
            "aws_key_id": dlt.secrets["aws_key_id"],
            "aws_secret_key": dlt.secrets["aws_secret_key"],
            "aws_region_name": dlt.secrets["aws_region_name"]
        }
    },
    "resource_defaults": {
        "primary_key": "event_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "management_events",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "NextToken",
                    "cursor_path": "NextToken"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Custom
- **Secrets**:
  - `aws_key_id`: Your AWS Access Key ID
  - `aws_secret_key`: Your AWS Secret Access Key
  - `aws_region_name`: The AWS region to connect to

## Resources

- **Resource Name**: management_events
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `NextToken`
  - **Cursor Path**: `NextToken`
- **Data Selector**: Extracts data from the response using a specified path

## Error Handling

- **Retry Logic**:
  - Retries on HTTP status code 400
  - Maximum of 3 retries
  - Exponential backoff with a factor of 2

## Schema

The schema for the `management_events` resource includes the following fields:

- `access_key_id`: Nullable string
- `cloud_trail_event`: Nullable string
- `event_id`: Nullable string
- `event_name`: Nullable string
- `event_source`: Nullable string
- `event_time`: Nullable number
- `read_only`: Nullable string
- `resources`: Nullable array of objects with properties:
  - `resource_name`: Nullable string
  - `resource_type`: Nullable string
- `username`: Nullable string

This configuration allows for the extraction and synchronization of event data from a third-party analytics API, with robust error handling and pagination support.