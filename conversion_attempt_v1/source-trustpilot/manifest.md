# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports fetching data from various resources, such as business units and reviews, using both API key and OAuth 2.0 authentication methods. The configuration includes error handling and pagination strategies to ensure robust data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
        "auth": {
            "type": "api_key",  # or "oauth2"
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
            "name": "configured_business_units",
            "endpoint": {
                "path": "/business-units/find",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [400, 403, 404],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "private_reviews",
            "endpoint": {
                "path": "/private/business-units/{business_unit_id}/reviews",
                "data_selector": "reviews",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "business_units",
            "endpoint": {
                "path": "/business-units/all",
                "data_selector": "businessUnits",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 1000
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key` or `oauth2`
- **Secrets**:
  - `api_key`: Used for API key authentication.
  - `client_id`, `client_secret`, `refresh_token`: Used for OAuth 2.0 authentication.

# Resources

## Configured Business Units

- **Endpoint Path**: `/business-units/find`
- **HTTP Method**: GET
- **Data Selector**: `data`
- **Error Handling**: Retries on status codes 400, 403, 404 with a maximum of 3 retries and a backoff factor of 2.

## Private Reviews

- **Endpoint Path**: `/private/business-units/{business_unit_id}/reviews`
- **HTTP Method**: GET
- **Data Selector**: `reviews`
- **Pagination Strategy**: Page number pagination with parameters `page` and `perPage`, limit set to 100.

## Business Units

- **Endpoint Path**: `/business-units/all`
- **HTTP Method**: GET
- **Data Selector**: `businessUnits`
- **Pagination Strategy**: Page number pagination with parameters `page` and `perPage`, limit set to 1000.

# Error Handling

- **Retry Logic**: Retries on HTTP status codes 400, 403, 404.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Configured Business Units

- **Fields**:
  - `id`: string or null
  - `displayName`: string or null
  - `name`: object or null
  - `websiteUrl`: string or null
  - `country`: string or null
  - `numberOfReviews`: object or null
  - `status`: string or null
  - `score`: object or null
  - `badFit`: boolean or null

## Private Reviews

- **Fields**:
  - `id`: string or null
  - `consumer`: object or null
  - `businessUnit`: object or null
  - `stars`: integer or null
  - `title`: string or null
  - `text`: string or null
  - `language`: string or null
  - `location`: object or null
  - `createdAt`: string or null (date-time)
  - `updatedAt`: string or null (date-time)
  - `experiencedAt`: string or null (date-time)
  - `referralEmail`: string or null
  - `referenceId`: string or null
  - `source`: string or null
  - `companyReply`: object or null
  - `tags`: array or null
  - `findReviewer`: object or null
  - `isVerified`: boolean or null
  - `numberOfLikes`: integer
  - `status`: string or null
  - `reportData`: object or null
  - `complianceLabels`: array or null
  - `countsTowardsTrustScore`: boolean or null
  - `countsTowardsLocationTrustScore`: boolean or null
  - `invitation`: object or null
  - `businessUnitHistory`: array or null
  - `reviewVerificationLevel`: string or null

## Business Units

- **Fields**:
  - `id`: string or null
  - `displayName`: string or null
  - `name`: object
  - `websiteUrl`: string or null
  - `country`: string or null
  - `numberOfReviews`: object or null
  - `status`: string or null
  - `score`: object or null
  - `badFit`: boolean or null

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source format, ensuring a clean and vendor-neutral approach.