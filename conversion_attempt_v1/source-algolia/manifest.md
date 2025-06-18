# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data retrieval operations, including fetching indices, querying indexes, retrieving available languages, accessing logs, and obtaining index settings. The API uses an API key for authentication and supports pagination and error handling mechanisms.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "indices",
            "endpoint": {
                "path": "/v1/indices",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "hitsPerPage",
                    "limit": 1
                }
            }
        },
        {
            "name": "indexes_query",
            "endpoint": {
                "path": "/v1/indexes/{indexName}/query",
                "data_selector": "hits",
                "method": "POST"
            }
        },
        {
            "name": "available_languages",
            "endpoint": {
                "path": "/v1/dictionaries/*/languages",
                "data_selector": []
            }
        },
        {
            "name": "logs",
            "endpoint": {
                "path": "/v1/logs",
                "data_selector": "logs"
            }
        },
        {
            "name": "indexes_settings",
            "endpoint": {
                "path": "/v1/indexes/{indexName}/settings",
                "data_selector": []
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication.
  - `application_id`: The application ID required for API requests.

# Resources

### Indices
- **Endpoint Path**: `/v1/indices`
- **Primary Key**: `name`
- **Pagination Strategy**: Page number with parameters `page` and `hitsPerPage`
- **Data Selector**: `items`

### Indexes Query
- **Endpoint Path**: `/v1/indexes/{indexName}/query`
- **HTTP Method**: POST
- **Data Selector**: `hits`

### Available Languages
- **Endpoint Path**: `/v1/dictionaries/*/languages`
- **Data Selector**: []

### Logs
- **Endpoint Path**: `/v1/logs`
- **Primary Key**: `sha1`
- **Data Selector**: `logs`

### Indexes Settings
- **Endpoint Path**: `/v1/indexes/{indexName}/settings`
- **Data Selector**: []

# Error Handling

- **Retry Logic**: 
  - Maximum retries: 3
  - Backoff strategy: Exponential with a factor of 2
- **HTTP Status Codes**: 
  - Retry on status code 429 (Rate limits hit)

# Schema

### Indices
- **Fields**: 
  - `createdAt`: string or null
  - `dataSize`: number or null
  - `entries`: number or null
  - `fileSize`: number or null
  - `lastBuildTimeS`: number or null
  - `name`: string
  - `numberOfPendingTasks`: number or null
  - `pendingTask`: boolean or null
  - `updatedAt`: string

### Indexes Query
- **Fields**: 
  - `type`: string or null
  - `_highlightResult`: object or null
  - `_rankingInfo`: object or null
  - `attribution`: string or null
  - `backdrop_path`: string or null
  - `bestSellingRank`: number or null
  - `cast`: array or null
  - `categories`: array or null
  - `crew`: array or null
  - `genres`: array or null
  - `id`: number or null
  - `keywords`: array or null
  - `name`: string or null
  - `objectID`: string or null
  - `original_language`: string or null
  - `original_title`: string or null
  - `overview`: string or null
  - `popularity`: number or null
  - `poster_path`: string or null
  - `release_date`: string or null
  - `salePrice`: number or null
  - `salePrice_range`: string or null
  - `shipping`: string or null
  - `shortDescription`: string or null
  - `title`: string or null
  - `url`: string or null
  - `vote_average`: number or null
  - `vote_count`: number or null

### Available Languages
- **Fields**: 
  - Language codes with properties for plurals and stopwords, each having `nbCustomEntries` as a number or null.

### Logs
- **Fields**: 
  - `answer`: string or null
  - `answer_code`: string or null
  - `index`: string or null
  - `inner_queries`: array or null
  - `ip`: string or null
  - `method`: string or null
  - `nb_api_calls`: string or null
  - `processing_time_ms`: string or null
  - `query_body`: string or null
  - `query_headers`: string or null
  - `query_nb_hits`: string or null
  - `query_params`: string or null
  - `sha1`: string
  - `timestamp`: string
  - `url`: string or null

### Indexes Settings
- **Fields**: 
  - `version`: number or null
  - `alternativesAsExact`: array or null
  - `attributesForFaceting`: array or null
  - `customRanking`: array or null
  - `exactOnSingleWordQuery`: string or null
  - `highlightPostTag`: string or null
  - `highlightPreTag`: string or null
  - `hitsPerPage`: number or null
  - `maxValuesPerFacet`: number or null
  - `minWordSizefor1Typo`: number or null
  - `minWordSizefor2Typos`: number or null
  - `paginationLimitedTo`: number or null
  - `queryType`: string or null
  - `ranking`: array or null
  - `removeWordsIfNoResults`: string or null
  - `searchableAttributes`: array or null
  - `separatorsToIndex`: string or null