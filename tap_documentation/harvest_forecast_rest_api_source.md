# Harvest Forecast REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Harvest Forecast API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.forecastapp.com/`
- Ensure to use the correct base URL for all API requests.

**Authentication:**
- Type: `oauth2`
- Token URL: `https://id.getharvest.com/api/v2/oauth2/token`
- Required Scopes: Not specified, but ensure to have appropriate permissions for data access.
- Parameters:
  - `client_id`: Your application's client ID.
  - `client_secret`: Your application's client secret.
  - `refresh_token`: The refresh token for obtaining a new access token.
- Headers:
  - `Authorization`: `Bearer {access_token}`
  - `Forecast-Account-ID`: Your Harvest account ID.

## 2. Available Endpoints

The Harvest Forecast API provides several endpoints for data extraction. Below are the details for each:

### Assignments Endpoint

**Endpoint Details:**
- Path: `/assignments`
- HTTP Method: `GET`
- Required Query Parameters: `start_date`, `end_date`
- Response Data Structure: JSON array under the key `assignments`

**Pagination Configuration:**
- Type: `single_page` (No explicit pagination; data is fetched in date ranges)
- Default Page Size: Not applicable

**Data Extraction:**
- JSONPath: `assignments`
- Primary Key: `id`

### Clients Endpoint

**Endpoint Details:**
- Path: `/clients`
- HTTP Method: `GET`
- Response Data Structure: JSON array under the key `clients`

**Pagination Configuration:**
- Type: `single_page`

**Data Extraction:**
- JSONPath: `clients`
- Primary Key: `id`

### Milestones Endpoint

**Endpoint Details:**
- Path: `/milestones`
- HTTP Method: `GET`
- Response Data Structure: JSON array under the key `milestones`

**Pagination Configuration:**
- Type: `single_page`

**Data Extraction:**
- JSONPath: `milestones`
- Primary Key: `id`

### People Endpoint

**Endpoint Details:**
- Path: `/people`
- HTTP Method: `GET`
- Response Data Structure: JSON array under the key `people`

**Pagination Configuration:**
- Type: `single_page`

**Data Extraction:**
- JSONPath: `people`
- Primary Key: `id`

### Projects Endpoint

**Endpoint Details:**
- Path: `/projects`
- HTTP Method: `GET`
- Response Data Structure: JSON array under the key `projects`

**Pagination Configuration:**
- Type: `single_page`

**Data Extraction:**
- JSONPath: `projects`
- Primary Key: `id`

### Roles Endpoint

**Endpoint Details:**
- Path: `/roles`
- HTTP Method: `GET`
- Response Data Structure: JSON array under the key `roles`

**Pagination Configuration:**
- Type: `single_page`

**Data Extraction:**
- JSONPath: `roles`
- Primary Key: `id`

## 3. Incremental Data Loading

**Incremental Support:**
- Enabled via `start_date` and `end_date` query parameters.
- Date Field: `updated_at`
- Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Values: Use the `start_date` from the configuration for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No explicit dependencies between endpoints are defined in the configuration. However, ensure to handle any logical dependencies in your data processing logic.

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests are rate-limited to 100 requests per 15 minutes.
- Implement exponential backoff for handling rate limit errors.

**Special Requirements:**
- Custom Headers: `User-Agent` should be set in requests.
- Response Format: JSON
- Error Handling: Implement retry logic with exponential backoff for transient errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.forecastapp.com/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://id.getharvest.com/api/v2/oauth2/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>",
        "headers": {
            "Authorization": "Bearer {access_token}",
            "Forecast-Account-ID": "<your_account_id>"
        }
    }
}

ENDPOINTS = [
    {
        "name": "assignments",
        "path": "/assignments",
        "method": "GET",
        "data_selector": "assignments",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "start_date",
            "format": "iso"
        }
    },
    # Additional endpoints follow the same structure
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters to set up a dlt data pipeline for the Harvest Forecast API, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*