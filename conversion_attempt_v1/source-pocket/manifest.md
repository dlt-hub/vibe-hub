# Overview

This document provides a configuration guide for integrating with a third-party content management API using dltHub's REST API source. The integration allows for retrieving and managing content items with various filtering and sorting options. The API supports authentication via API keys and offers pagination and error handling mechanisms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3",
        "auth": {
            "type": "api_key",
            "name": "consumer_key",
            "api_key": dlt.secrets["consumer_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "item_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "content_items",
            "endpoint": {
                "path": "/get",
                "method": "POST",
                "params": {
                    "access_token": dlt.secrets["access_token"],
                    "state": "unread",
                    "favorite": "0",
                    "tag": "",
                    "contentType": "article",
                    "sort": "newest",
                    "detailType": "complete",
                    "search": "",
                    "domain": "",
                    "since": "2022-10-20 14:14:14"
                },
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Secrets**: 
  - `consumer_key`: Your application's consumer key.
  - `access_token`: The user's access token.

## Resources

- **Resource Name**: content_items
- **Endpoint Path**: `/get`
- **HTTP Method**: POST
- **Pagination Strategy**: Offset
  - **Limit**: 10
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status code 403
  - Maximum retries: 3
  - Backoff factor: 2
- **Known HTTP Response Codes**: 
  - 401: Fail
  - 403: Retry

## Schema

The schema for the content items includes fields such as:

- `item_id`: Unique identifier for the item.
- `resolved_id`: Resolved identifier for the item.
- `given_url`: URL provided for the item.
- `given_title`: Title provided for the item.
- `favorite`: Indicates if the item is marked as a favorite.
- `status`: Status of the item.
- `time_added`: Time when the item was added.
- `time_updated`: Time when the item was updated.
- `time_read`: Time when the item was read.
- `time_favorited`: Time when the item was favorited.
- `sort_id`: Sorting identifier.
- `resolved_title`: Resolved title for the item.
- `resolved_url`: Resolved URL for the item.
- `excerpt`: Short summary of the content.
- `is_article`: Indicates if the item is an article.
- `is_index`: Indicates if the item is an index.
- `has_image`: Indicates if the item has an image.
- `has_video`: Indicates if the item has a video.
- `word_count`: Number of words in the content.
- `lang`: Language of the content.
- `time_to_read`: Estimated time to read the content.
- `top_image_url`: Top image URL.
- `tags`: Tags associated with the content.
- `authors`: Authors of the content.
- `image`: Main image associated with the content.
- `images`: Collection of images related to the content.
- `videos`: Videos related to the content.
- `domain_metadata`: Metadata related to the domain of the content.
- `listen_duration_estimate`: Estimated time to listen to the content.