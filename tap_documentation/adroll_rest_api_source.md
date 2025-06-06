# Adroll REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the AdRoll REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://services.adroll.com/api/v1/`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://services.adroll.com/auth/token`
- **Required Scopes**: Not explicitly mentioned, but ensure the OAuth2 token has access to the necessary endpoints.
- **Parameters**:
  - `client_id`: Your application's client ID.
  - `client_secret`: Your application's client secret.
  - `access_token`: The OAuth2 access token.
  - `refresh_token`: The OAuth2 refresh token.

## 2. Available Endpoints

### Endpoint Details

#### Advertisables
- **Path**: `/organization/get_advertisables`
- **Method**: `GET`
- **Response Data Structure**: JSON object with a `results` array containing advertisable entities.

#### Ads
- **Path**: `/advertisable/get_ads`
- **Method**: `GET`
- **Required Parameters**: `advertisable` (ID of the advertisable entity)
- **Response Data Structure**: JSON object with a `results` array containing ad entities.

#### Ad Reports
- **Path**: `/report/ad`
- **Method**: `GET`
- **Required Parameters**: `advertisable`, `data_format`, `start_date`, `end_date`
- **Response Data Structure**: JSON object with a `results` array containing report data.

### Pagination Configuration
- **Type**: Not explicitly defined in the code, but endpoints like `advertisable/get_ads` may require handling large datasets.
- **Parameter Names**: Not specified, but typical parameters include `limit` and `offset`.
- **Default/Maximum Page Sizes**: Not specified.

### Data Extraction
- **JSONPath**: Use `results` to locate the data array in responses.
- **Primary Key**: Typically `eid` for entities like ads, advertisables, etc.

## 3. Incremental Data Loading

### Incremental Support
- **Ad Reports**: Supports incremental loading using the `date` field.
- **Query Parameter**: `start_date` and `end_date` for date range filtering.
- **Date Format**: `MM-DD-YYYY` for requests, `YYYY-MM-DDTHH:MM:SS.ssssssZ` for responses.
- **Initial Values**: Use the `start_date` from the configuration for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Ads, Segments, Campaigns, AdGroups**: Depend on `Advertisables` to provide the `advertisable` parameter.
- **Mapping**: Use `advertisable_eid` from `Advertisables` to query dependent endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly mentioned, but implement backoff strategies for handling rate limits.
- **Burst Limits**: Use `backoff` library for retrying requests on HTTP errors.

### Special Requirements
- **Custom Headers**: None specified, but ensure OAuth2 tokens are included in headers.
- **Response Format Considerations**: JSON responses with `results` arrays.
- **Error Handling Patterns**: Use `backoff` for retrying on HTTP errors.
- **Data Type Specifications**: Ensure date fields are correctly formatted and parsed.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://services.adroll.com/api/v1/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://services.adroll.com/auth/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "access_token": "<your_access_token>",
        "refresh_token": "<your_refresh_token>"
    }
}

ENDPOINTS = [
    {
        "name": "advertisables",
        "path": "/organization/get_advertisables",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "eid"
    },
    {
        "name": "ads",
        "path": "/advertisable/get_ads",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "eid",
        "dependencies": {
            "advertisable": "advertisables.eid"
        }
    },
    {
        "name": "ad_reports",
        "path": "/report/ad",
        "method": "GET",
        "data_selector": "results",
        "primary_key": ["eid", "date"],
        "incremental": {
            "cursor_path": "date",
            "param_name": ["start_date", "end_date"],
            "format": "MM-DD-YYYY"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "ads",
        "depends_on": "advertisables",
        "param_mapping": {"advertisable": "eid"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the AdRoll API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*