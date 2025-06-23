# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and schema. The configuration includes authentication, pagination, error handling, and schema definitions for each resource.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_token"],
            "password": dlt.secrets["api_password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "activity_sets",
            "endpoint": {
                "path": "/v1/activity_sets",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 500,
                    "offset_param": "skip"
                }
            }
        }
        # Additional resources can be added here
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_token`: The API token for authentication.
  - `api_password`: The password associated with the API token.

## Resources

### Activity Sets

- **Resource Name**: activity_sets
- **Endpoint Path**: `/v1/activity_sets`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 500
  - **Offset Parameter**: `skip`
- **Data Selector**: `data`

### Contacts

- **Resource Name**: contacts
- **Endpoint Path**: `/v1/contacts`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 500
  - **Offset Parameter**: `skip`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**:
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2 seconds

## Schema

### Activity Sets Schema

- **Fields**:
  - `ACTIVITYSET_ID`: Integer, ID of the activity set
  - `NAME`: String, Name of the activity set
  - `OWNER_USER_ID`: Integer, ID of the user who owns the activity set
  - Additional fields as defined in the schema

### Contacts Schema

- **Fields**:
  - `CONTACT_ID`: Integer, Unique identifier for the contact
  - `FIRST_NAME`: String, First name of the contact
  - `LAST_NAME`: String, Last name of the contact
  - Additional fields as defined in the schema

## Final Verification Checklist

- All parameters are sourced from the YAML configuration.
- All brand-specific terms are generalized.
- Pagination strategies are mapped properly.
- Auth config is translated using dltHub format.
- No Airbyte language remains.
- No inferred/guessed logic is added.
- Schema is consistent with YAML and neutral.