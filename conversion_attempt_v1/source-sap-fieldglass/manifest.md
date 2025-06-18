# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for the retrieval of data related to worker information and associated details. The API supports data synchronization through a RESTful endpoint, utilizing API key-based authentication.

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
        "primary_key": "Person ID",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "worker_data",
            "endpoint": {
                "path": "/v1/active_worker_download",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header. The key is stored securely using `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: worker_data
- **Endpoint Path**: `/v1/active_worker_download`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors. The configuration can include retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use an exponential backoff strategy with a configurable backoff factor.

# Schema

The schema defines the structure of the data retrieved from the API. The fields are described with their types and constraints. Here are some of the key fields:

- **Bill Rate**: Nullable string. Description: Worker’s Bill Rate associated with the Rate Category/UOM.
- **Business Unit Code**: Nullable string. Max length: 100. Description: Business Unit Code.
- **Currency**: Nullable string. Max length: 100. Description: ISO currency designation (e.g., USD).
- **End Date**: Nullable string. Description: Work order end date.
- **First Name**: Nullable string. Max length: 100. Description: First name of the worker.
- **Job Seeker ID**: Nullable string. Max length: 14. Description: 14-character alphanumeric Job Seeker ID.
- **Person ID**: Nullable string. Max length: 24. Description: Unique identifier for the worker as a person.
- **Status**: Nullable string. Max length: 50. Description: Status of the worker.
- **Worker Email**: Nullable string. Max length: 100. Description: Worker’s e-mail address.

The schema supports additional properties and custom fields, allowing for flexibility in data representation.