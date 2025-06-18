# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the extraction of athlete statistics and activity data through a RESTful interface. The API supports OAuth2 authentication and offers pagination and incremental sync capabilities.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "athlete_statistics",
            "endpoint": {
                "path": "/athletes/{athlete_id}/stats",
                "data_selector": "data"
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/athlete/activities",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 30
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`: The client ID for the API application.
  - `client_secret`: The client secret for the API application.
  - `refresh_token`: The refresh token with necessary permissions.
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

### Athlete Statistics

- **Resource Name**: athlete_statistics
- **Endpoint Path**: `/athletes/{athlete_id}/stats`
- **HTTP Method**: GET
- **Data Selector**: `data`

### Activities

- **Resource Name**: activities
- **Endpoint Path**: `/athlete/activities`
- **HTTP Method**: GET
- **Pagination Strategy**: Page Number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `per_page`
  - **Limit**: 30
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**:
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Athlete Statistics Schema

- **Fields**:
  - `all_ride_totals`: Object containing cumulative stats for all ride activities.
  - `all_run_totals`: Object containing cumulative stats for all run activities.
  - `all_swim_totals`: Object containing cumulative stats for all swim activities.
  - `biggest_climb_elevation_gain`: Number representing the elevation gain of the biggest climb.
  - `biggest_ride_distance`: Number representing the distance of the biggest ride.

### Activities Schema

- **Fields**:
  - `type`: String indicating the type of activity (e.g., ride, run).
  - `achievement_count`: Integer representing the total number of achievements.
  - `athlete`: Object containing details of the athlete.
  - `average_speed`: Number representing the average speed of the activity.
  - `distance`: Number representing the total distance covered.

This configuration provides a comprehensive setup for accessing and synchronizing data from a third-party analytics API using dltHub's REST API source. The setup includes authentication, resource configuration, pagination, error handling, and schema definitions.