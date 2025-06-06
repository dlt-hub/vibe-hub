# Outreach REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Outreach API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.outreach.io/api/v2/`
- This URL includes the version path `/v2/` as required by the Outreach API.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://api.outreach.io/oauth/token`
- **Required Scopes**: Not explicitly mentioned, but OAuth2 typically requires scopes for specific API access.
- **Parameters**:
  - `client_id`: Your application's client ID.
  - `client_secret`: Your application's client secret.
  - `redirect_uri`: The URI to redirect to after authentication.
  - `refresh_token`: The token used to obtain a new access token.
- **Token Format**: `Bearer {token}`

## 2. Available Endpoints

### Endpoint Details

Each endpoint provides specific data and supports incremental data loading where applicable.

#### Example: Accounts Endpoint
- **Path**: `/accounts`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `filter[updatedAt]`, `sort`
- **Response Data Structure**: JSON with a `data` array containing account records.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**:
  - `page[size]`: Controls the number of records per page.
  - `links.next`: Used to determine the next page from the response.
- **Default/Maximum Page Sizes**: Default is 250 records per page.

### Data Extraction
- **JSONPath**: `data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `attributes`, `relationships`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `filter[updatedAt]`
- **Date/Timestamp Field**: `updatedAt`
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Values**: Use the `start_date` from the configuration to fetch records updated since that date.

## 4. Endpoint Dependencies

### Resource Relationships
- **Example**: The `calls` endpoint depends on `callDispositionId`, `callPurposeId`, etc., which are foreign keys referencing other endpoints.
- **Mapping Identifiers**: Use the `id` field from parent resources to map to child resources.
- **Processing Order**: Ensure parent resources are processed before child resources to maintain data integrity.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Hour**: 10,000 requests per hour
- **Burst Limits**: Handle rate limits using the `x-ratelimit-reset` header to determine when to resume requests.

### Special Requirements
- **Custom Headers**: `Authorization: Bearer {token}`
- **Response Format Considerations**: JSON responses with `data`, `attributes`, and `relationships`.
- **Error Handling Patterns**: Implement retries with exponential backoff for 5xx errors and rate limit errors.

## Output Format

Below is an example configuration for the Outreach API using dlt:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.outreach.io/api/v2/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.outreach.io/oauth/token",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret",
        "redirect_uri": "your_redirect_uri",
        "refresh_token": "your_refresh_token",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/accounts",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "page[size]",
            "next_page_path": "links.next",
            "limit": 250
        },
        "incremental": {
            "cursor_path": "updatedAt",
            "param_name": "filter[updatedAt]",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "calls",
        "depends_on": "call_dispositions",
        "param_mapping": {"callDispositionId": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Outreach API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*