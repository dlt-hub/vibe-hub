# dlt REST API Source Configuration Documentation for Asana

This document provides a comprehensive guide to configuring a dlt data pipeline for extracting data from the Asana REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://app.asana.com/api/1.0/`
- This is the base URL for all API requests to Asana.

**Authentication:**
- Type: `oauth2`
- Required Parameters:
  - `client_id`: Your Asana client ID.
  - `client_secret`: Your Asana client secret.
  - `redirect_uri`: The URI to redirect to after authentication.
  - `refresh_token`: The token used to refresh the access token.
  - `access_token`: (Optional) Direct access token if available.
- OAuth2 Flow:
  - Token URL: `https://app.asana.com/-/oauth_token`
  - Required Scopes: Not explicitly defined in the code, but typically includes access to user data and tasks.

## 2. Available Endpoints

### Users Endpoint
- **Path:** `/users`
- **Method:** `GET`
- **Query Parameters:**
  - `workspace`: Workspace ID to filter users.
  - `opt_fields`: Comma-separated list of fields to include.
- **Response Data Structure:** JSON object with a `data` array containing user details.

### Tasks Endpoint
- **Path:** `/tasks`
- **Method:** `GET`
- **Query Parameters:**
  - `project`: Project ID to filter tasks.
  - `opt_fields`: Comma-separated list of fields to include.
  - `modified_since`: ISO 8601 date-time string for incremental loading.
- **Response Data Structure:** JSON object with a `data` array containing task details.

### Projects Endpoint
- **Path:** `/projects`
- **Method:** `GET`
- **Query Parameters:**
  - `workspace`: Workspace ID to filter projects.
  - `opt_fields`: Comma-separated list of fields to include.
- **Response Data Structure:** JSON object with a `data` array containing project details.

### Pagination Configuration
- **Type:** `offset` (handled internally by Asana SDK)
- **Parameter Names:** Not explicitly required as pagination is managed by the SDK.
- **Default/Maximum Page Sizes:** Managed by the SDK, typically 100 records per page.

## 3. Incremental Data Loading

**Incremental Support:**
- **Tasks Endpoint:**
  - Query Parameter: `modified_since`
  - Date Field: `modified_at`
  - Format: ISO 8601 date-time string
  - Recommended Initial Value: Use the `start_date` from the configuration.

**Projects Endpoint:**
- Query Parameter: `modified_since`
- Date Field: `modified_at`
- Format: ISO 8601 date-time string

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Tasks** depend on **Projects**: Tasks are fetched based on project IDs.
- **Stories** depend on **Tasks**: Stories are fetched based on task IDs.
- **Subtasks** depend on **Tasks**: Subtasks are fetched based on parent task IDs.

**Mapping Identifiers:**
- Use `gid` (global ID) to map between resources.

## 5. API Behavior & Limits

**Rate Limiting:**
- Asana uses a leaky bucket algorithm for rate limiting.
- Handle `429` status code by implementing backoff and retry logic.

**Special Requirements:**
- Custom Headers: None specified beyond standard OAuth2 headers.
- Response Format: JSON
- Error Handling: Implement retries for `500` errors and handle `InvalidTokenError` by refreshing the token.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://app.asana.com/api/1.0/",
    "auth": {
        "type": "oauth2",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "redirect_uri": "<your_redirect_uri>",
        "refresh_token": "<your_refresh_token>"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "gid",
        "pagination": {
            "type": "offset"
        }
    },
    {
        "name": "tasks",
        "path": "/tasks",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "gid",
        "pagination": {
            "type": "offset"
        },
        "incremental": {
            "cursor_path": "modified_at",
            "param_name": "modified_since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "tasks",
        "depends_on": "projects",
        "param_mapping": {"project_id": "gid"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for Asana, ensuring efficient data extraction and handling of API constraints.