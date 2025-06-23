# Overview

This document provides a configuration example for integrating with a third-party form management API using dltHub's REST API source. The API allows users to retrieve form metadata and responses, supporting data collection and analysis tasks. The integration is designed to handle OAuth2 authentication and supports pagination for retrieving large datasets.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://oauth2.example.com/token",
            "refresh_token": dlt.secrets["client_refresh_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "forms",
            "endpoint": {
                "path": "/forms/{form_id}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "form_responses",
            "endpoint": {
                "path": "/forms/{form_id}/responses",
                "data_selector": "responses",
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

## Authentication

- **Type**: OAuth2
- **Required Secrets**:
  - `client_id`: The client ID for OAuth2 authentication.
  - `client_secret`: The client secret for OAuth2 authentication.
  - `client_refresh_token`: The refresh token for obtaining new access tokens.

## Resources

### Forms

- **Resource Name**: forms
- **Endpoint Path**: `/forms/{form_id}`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`

### Form Responses

- **Resource Name**: form_responses
- **Endpoint Path**: `/forms/{form_id}/responses`
- **HTTP Method**: GET
- **Pagination Strategy**: cursor
  - **Cursor Parameter**: `pageToken`
  - **Cursor Path**: `nextPageToken`
- **Data Selector**: `responses`

## Error Handling

- **Retry Logic**:
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Forms

- **Fields**:
  - `formId`: string
  - `info`: object (nullable)
    - `description`: string (nullable)
    - `documentTitle`: string (nullable)
    - `title`: string (nullable)
  - `items`: array (nullable)
    - `itemId`: string (nullable)
    - `questionItem`: object (nullable)
      - `question`: object (nullable)
        - `choiceQuestion`: object (nullable)
          - `type`: string (nullable)
          - `options`: array (nullable)
            - `value`: string (nullable)
        - `questionId`: string (nullable)
        - `required`: boolean (nullable)
        - `textQuestion`: object (nullable)
          - `paragraph`: boolean (nullable)
  - `responderUri`: string (nullable)
  - `revisionId`: string (nullable)
  - `settings`: object (nullable)

### Form Responses

- **Fields**:
  - `answers`: object (nullable)
    - `questionId`: string (nullable)
    - `textAnswers`: object (nullable)
      - `answers`: array (nullable)
        - `value`: string (nullable)
  - `createTime`: string (nullable)
  - `lastSubmittedTime`: string (nullable)
  - `responseId`: string

This configuration provides a clean, vendor-neutral setup for integrating with a form management API using dltHub's REST API source, ensuring seamless data retrieval and processing.