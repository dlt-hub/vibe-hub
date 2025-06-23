# Overview

This document provides a configuration example for integrating with a third-party book information API using the dltHub REST API source format. The API allows querying a collection of books with various filters such as author birth year, copyright status, language, and more. The integration supports pagination and data extraction from a specified field path.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "books",
            "endpoint": {
                "path": "/v1/resource",
                "params": {
                    "author_year_start": "{{ config['author_year_start'] }}",
                    "author_year_end": "{{ config['author_year_end'] }}",
                    "copyright": "{{ config['copyright'] }}",
                    "languages": "{{ config['languages'] }}",
                    "search": "{{ config['search'] }}",
                    "sort": "{{ config['sort'] }}",
                    "topic": "{{ config['topic'] }}"
                },
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 32
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

- **Resource Name**: `books`
- **Endpoint Path and Method**: `/v1/resource`, HTTP method: GET
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `page_size`, default page size is 32.
- **Data Selector**: Extract data from the `results` field.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**:
  - `authors`: Array of objects with properties `name`, `birth_year`, and `death_year`.
  - `bookshelves`: Array of strings.
  - `copyright`: Boolean or null.
  - `download_count`: Integer.
  - `formats`: Object with various format types as string properties.
  - `id`: Integer.
  - `languages`: Array of strings.
  - `media_type`: String.
  - `subjects`: Array of strings.
  - `title`: String.
  - `translators`: Array of objects with properties `name`, `birth_year`, and `death_year`.

This configuration provides a clean and generalized setup for accessing book data from a third-party API, abstracting away any vendor-specific details.