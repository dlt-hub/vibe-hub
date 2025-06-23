# Overview

This document provides a configuration example for integrating with a third-party API that provides a list of font resources. The integration is designed to be vendor-neutral and follows the dltHub REST API source format. The API supports fetching font data with various attributes such as version, category, family, and file variants.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "family",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "fonts",
            "endpoint": {
                "path": "/v1/fonts?sort=SORT_UNDEFINED&prettyPrint=true&alt=json",
                "data_selector": "items",
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
- **Secrets or Config Values**: 
  - `api_key`: The API key required to access the API, stored securely in dlt secrets.

## Resources

- **Resource Name**: `fonts`
- **Endpoint Path and Method**: 
  - Path: `/v1/fonts?sort=SORT_UNDEFINED&prettyPrint=true&alt=json`
  - Method: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `items`

## Error Handling

- **Retry/Backoff Logic**: Not specified in the configuration.
- **Known HTTP Response Codes**: Not specified in the configuration.
- **Fallback/Handling Behavior**: Not specified in the configuration.

## Schema

The schema for the font data includes the following fields:

- **items**: An array of font objects, each containing:
  - **version**: The version of the font data.
  - **category**: The category the font belongs to (e.g., 'sans-serif', 'serif').
  - **family**: The name of the font family (e.g., 'Roboto', 'Open Sans').
  - **files**: An object containing URLs to different file variants of the font, such as `italic` and `regular`.
  - **kind**: The resource kind, typically set to 'webfonts#webfont'.
  - **lastModified**: The timestamp of the last modification date of the font.
  - **subsets**: A list of language subsets supported by the font.
  - **variants**: A list of different styles and weights available for the font.

This configuration provides a structured way to access and manage font data from a third-party API using dltHub's REST API source format.