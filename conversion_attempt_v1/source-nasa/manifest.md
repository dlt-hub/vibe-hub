# Overview

This document provides a configuration guide for integrating with a third-party API that offers access to a collection of daily images and related metadata. The API supports retrieving data based on date ranges and includes options for incremental synchronization using date-based cursors.

## Configuration Example

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "date",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "daily_images",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is required for accessing the API and should be stored securely. It is passed as a query parameter in requests.

## Resources

- **Resource Name**: `daily_images`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page` (no pagination)
- **Data Selector**: The data is extracted from the root of the response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors such as network issues or server unavailability.
- **HTTP Response Codes**: Handle common HTTP status codes like 429 (Too Many Requests) and 500 (Internal Server Error) with appropriate retry mechanisms.

## Schema

The schema defines the structure of the data returned by the API. The fields include:

- **concept_tags**: Nullable boolean indicating the presence of concept tags.
- **concepts**: Nullable object or string containing concept information.
- **copyright**: Nullable string for copyright information.
- **date**: Nullable string formatted as a date (`%Y-%m-%d`).
- **explanation**: Nullable string providing a description.
- **hdurl**: Nullable string formatted as a URI for high-definition images.
- **media_type**: Nullable string indicating the type of media (e.g., image, video).
- **resource**: Nullable object containing additional resource information.
- **service_version**: Nullable string indicating the version of the service.
- **thumbnail_url**: Nullable string formatted as a URI for thumbnail images.
- **title**: Nullable string for the title of the image.
- **url**: Nullable string formatted as a URI for the image.

This configuration provides a structured approach to accessing and synchronizing data from the API, ensuring that all necessary parameters and schema definitions are included for successful integration.