# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the retrieval of performance metrics and analysis results for specified URLs. The API supports various analysis strategies and categories, providing detailed insights into page performance.

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "performance_metrics",
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

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

# Resources

- **Resource Name**: `performance_metrics`
- **Endpoint Path and Method**: `/v1/resource`, HTTP method: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and timeouts.

# Schema

The schema defines the structure of the data retrieved from the API. It includes fields such as:

- `analysisUTCTimestamp`: The timestamp of when the analysis was performed in UTC.
- `captchaResult`: Result of captcha check during analysis.
- `id`: Unique identifier for the analyzed page.
- `lighthouseResult`: Object containing results from the analysis, including audits, categories, and environment details.
- `loadingExperience`: Object containing loading experience metrics, such as cumulative layout shift score and first contentful paint latency.
- `originLoadingExperience`: Metrics related to the origin site of the analyzed page.
- `strategy`: Strategy used for page analysis.

Each field is described with its type and a brief description of its purpose. The schema is designed to be flexible and accommodate additional properties as needed.