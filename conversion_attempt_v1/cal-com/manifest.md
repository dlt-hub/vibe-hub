# Overview

This document provides a configuration guide for integrating with a third-party scheduling API. The integration allows for seamless data synchronization of events, attendees, and booking details, facilitating analysis and integration into downstream systems such as data warehouses or CRMs. This setup streamlines automated reporting and insights for time management and booking analytics.

# Configuration Example

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "event_types",
            "endpoint": {
                "path": "/v1/event-types",
                "data_selector": "data.eventTypeGroups.*.eventTypes.*",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "my_profile",
            "endpoint": {
                "path": "/v1/me",
                "data_selector": "data"
            }
        },
        {
            "name": "schedules",
            "endpoint": {
                "path": "/v1/schedules",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "calendars",
            "endpoint": {
                "path": "/v1/calendars",
                "data_selector": "data.connectedCalendars.*.calendars.*"
            }
        },
        {
            "name": "bookings",
            "endpoint": {
                "path": "/v1/bookings",
                "data_selector": "data.*",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "conferencing",
            "endpoint": {
                "path": "/v1/conferencing",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_key` stored in `dlt.secrets`.

# Resources

- **Event Types**
  - **Endpoint Path**: `/v1/event-types`
  - **Data Selector**: `data.eventTypeGroups.*.eventTypes.*`
  - **Pagination**: Offset with `limit` of 100 and `offset_param` as `skip`

- **My Profile**
  - **Endpoint Path**: `/v1/me`
  - **Data Selector**: `data`

- **Schedules**
  - **Endpoint Path**: `/v1/schedules`
  - **Data Selector**: `data`
  - **Pagination**: Offset with `limit` of 100 and `offset_param` as `skip`

- **Calendars**
  - **Endpoint Path**: `/v1/calendars`
  - **Data Selector**: `data.connectedCalendars.*.calendars.*`

- **Bookings**
  - **Endpoint Path**: `/v1/bookings`
  - **Data Selector**: `data.*`
  - **Pagination**: Offset with `limit` of 100 and `offset_param` as `skip`

- **Conferencing**
  - **Endpoint Path**: `/v1/conferencing`
  - **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Event Types**: Includes fields like `description`, `metadata`, `afterEventBuffer`, `assignAllTeamMembers`, etc.
- **My Profile**: Includes fields like `defaultScheduleId`, `email`, `id`, `timeFormat`, etc.
- **Schedules**: Includes fields like `availability`, `id`, `isDefault`, `name`, etc.
- **Calendars**: Includes fields like `credentialId`, `email`, `externalId`, `integration`, etc.
- **Bookings**: Includes fields like `description`, `absentHost`, `attendees`, `bookingFieldsResponses`, etc.
- **Conferencing**: Includes fields like `type`, `appId`, `id`, `invalid`, etc.

This configuration ensures a clean and efficient integration with the scheduling API, providing robust data synchronization capabilities.