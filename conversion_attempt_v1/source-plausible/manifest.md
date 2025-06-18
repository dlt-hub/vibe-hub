# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for the retrieval of timeseries data over a specified period, including metrics such as visitors, pageviews, bounce rate, visit duration, and visits.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "date",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "timeseries_data",
            "endpoint": {
                "path": "/v1/resource",
                "params": {
                    "site_id": dlt.config["site_id"],
                    "period": "custom",
                    "metrics": "visitors,pageviews,bounce_rate,visit_duration,visits",
                    "date": f"{dlt.config.get('start_date', '2019-01-01')},{dlt.utils.today_utc()}"
                },
                "data_selector": "results",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

- **Resource Name**: timeseries_data
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `results`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

The schema for the timeseries data includes the following fields:

- **bounce_rate**: Integer or null, representing the bounce rate percentage.
- **date**: String, the date in ISO 8601 format.
- **pageviews**: Integer, the number of pageview events.
- **visit_duration**: Integer or null, visit duration in seconds.
- **visitors**: Integer, the number of unique visitors.
- **visits**: Integer, the number of visits/sessions.

All fields are described in a neutral manner, ensuring compatibility with various analytics APIs.