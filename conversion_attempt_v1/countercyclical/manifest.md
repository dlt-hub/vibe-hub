# Overview

This document provides a configuration guide for integrating with a third-party financial intelligence API using dltHub's REST API source format. The API offers access to various resources such as investments, valuations, and memos, allowing users to retrieve and manage financial data efficiently.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
        "auth": {
            "type": "api_key",
            "name": "apiKey",
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
            "name": "investments",
            "endpoint": {
                "path": "/investments",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "valuations",
            "endpoint": {
                "path": "/valuations",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "memos",
            "endpoint": {
                "path": "/memos",
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
- **Configuration**: The API key is required and should be stored securely using `dlt.secrets["api_key"]`. It is injected into the request as a query parameter with the name `apiKey`.

## Resources

### Investments

- **Endpoint Path**: `/investments`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Valuations

- **Endpoint Path**: `/valuations`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Memos

- **Endpoint Path**: `/memos`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Investments

- **Fields**: Includes fields such as `type`, `description`, `address`, `ceo`, `cik`, `clearbit`, `country`, `createdAt`, `employees`, `exchange`, `figi`, `foundingDate`, `headquarters`, `id`, `industry`, `ipoDate`, `isArchived`, `isFavorite`, `isLocked`, `lei`, `marketType`, `name`, `sector`, `tags`, `tickerSymbol`, `updatedAt`, `visibility`, `website`.
- **Field Types**: String, number, boolean, object, array, with nullability.

### Valuations

- **Fields**: Includes fields such as `type`, `description`, `backgroundColor`, `createdAt`, `delineation`, `discountRate`, `emoji`, `endingQuarter`, `endingYear`, `growthMetric`, `growthRate`, `id`, `isFavorite`, `name`, `shareToken`, `startingQuarter`, `startingYear`, `status`, `tableData`, `terminalPeriod`, `terminalRate`, `updatedAt`.
- **Field Types**: String, number, boolean, array, with nullability.

### Memos

- **Fields**: Includes fields such as `type`, `archived`, `avatar`, `backgroundColor`, `banner`, `bannerVisible`, `body`, `createdAt`, `documentType`, `emoji`, `favorited`, `foregroundColor`, `icon`, `id`, `locked`, `publiclyVisible`, `sourcesVisible`, `title`, `tocVisible`, `updatedAt`, `views`.
- **Field Types**: String, number, boolean, with nullability.

This configuration guide provides a comprehensive overview of how to set up and use the API with dltHub's REST API source, ensuring a seamless integration experience.