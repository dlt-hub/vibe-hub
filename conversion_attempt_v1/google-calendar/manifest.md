# Overview

This document provides a configuration example for integrating with a third-party calendar API using dltHub's REST API source format. The integration supports various resources such as colors, settings, calendar lists, calendars, and events, each with specific endpoints and pagination strategies.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/calendar/v3/",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "refresh_token": dlt.secrets["client_refresh_token"],
            "token_url": "https://oauth2.example.com/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "colors",
            "endpoint": {
                "path": "/colors",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "settings",
            "endpoint": {
                "path": "/users/me/settings",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "calendarlist",
            "endpoint": {
                "path": "/users/me/calendarList",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "calendars",
            "endpoint": {
                "path": "/users/me/calendarList",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/calendars/{calendar_id}/events",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `client_id`
- **Client Secret**: `client_secret`
- **Refresh Token**: `client_refresh_token`
- **Token URL**: `https://oauth2.example.com/token`

# Resources

### Colors
- **Endpoint Path**: `/colors`
- **Data Selector**: `data`
- **Pagination**: Single page

### Settings
- **Endpoint Path**: `/users/me/settings`
- **Data Selector**: `items`
- **Pagination**: Cursor-based with `pageToken`

### Calendar List
- **Endpoint Path**: `/users/me/calendarList`
- **Data Selector**: `items`
- **Pagination**: Cursor-based with `pageToken`

### Calendars
- **Endpoint Path**: `/users/me/calendarList`
- **Data Selector**: `items`
- **Pagination**: Cursor-based with `pageToken`

### Events
- **Endpoint Path**: `/calendars/{calendar_id}/events`
- **Data Selector**: `items`
- **Pagination**: Cursor-based with `pageToken`

# Error Handling

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

### Colors
- **Primary Key**: `calendar`, `event`
- **Fields**: Various color properties with background and foreground colors

### Settings
- **Primary Key**: `id`
- **Fields**: Includes `etag`, `id`, `kind`, `value`

### Calendar List
- **Primary Key**: `id`
- **Fields**: Includes `description`, `accessRole`, `backgroundColor`, `colorId`, etc.

### Calendars
- **Primary Key**: `id`
- **Fields**: Similar to Calendar List

### Events
- **Primary Key**: `id`
- **Fields**: Includes `description`, `attendees`, `conferenceData`, `created`, `creator`, etc.

This configuration provides a comprehensive setup for accessing and managing calendar data through a REST API, using dltHub's source format.