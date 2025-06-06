# Impact REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Impact API. It outlines the necessary parameters and configurations required to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.impact.com/{api_catalog}/{account_sid}/`
- **Version Path**: The API version is typically included in the endpoint paths, such as `v2`.

### Authentication
- **Type**: `basic`
- **Credentials**:
  - **Account SID**: Provided in the configuration file.
  - **Auth Token**: Provided in the configuration file.
- **Header Format**: Basic Authentication is used, where the `account_sid` and `auth_token` are combined and encoded in the request headers.

## 2. Available Endpoints

### Endpoint Details

#### Example Endpoint: `Ads`
- **Path**: `/Ads`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: None
- **Response Data Structure**: JSON object with a key `Ads` containing an array of ad records.

#### Pagination Configuration
- **Type**: `single_page` (No pagination required for this endpoint)
- **Parameter Names**: Not applicable
- **Default/Maximum Page Sizes**: Not applicable

#### Data Extraction
- **JSONPath**: `Ads`
- **Primary Key**: `id`
- **Key Fields**: `id` uniquely identifies each ad record.

### Additional Endpoints
- **Campaigns**: `/Campaigns`
- **Actions**: `/Actions`
- **Invoices**: `/Invoices`
- Each endpoint has specific configurations for pagination, data extraction, and incremental loading as detailed in the `STREAMS` dictionary.

## 3. Incremental Data Loading

### Incremental Support

#### Example Endpoint: `api_submissions`
- **Incremental Parameter**: `submission_date`
- **Date Field**: `submission_date`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: The `start_date` specified in the configuration file.

## 4. Endpoint Dependencies

### Resource Relationships

#### Example Dependency: `Campaigns` and `Actions`
- **Parent Endpoint**: `Campaigns`
- **Child Endpoint**: `Actions`
- **Mapping**: `CampaignId` in `Actions` is mapped to `id` in `Campaigns`.
- **Processing Order**: `Campaigns` must be processed before `Actions`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Up to 1000 requests per hour.
- **Burst Limits**: Handled using exponential backoff and rate limiting in the client implementation.

### Special Requirements
- **Custom Headers**: `User-Agent` is required and specified in the configuration.
- **Response Format**: JSON
- **Error Handling**: Specific exceptions are raised based on HTTP status codes, with retries for server errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.impact.com/{api_catalog}/{account_sid}/",
    "auth": {
        "type": "basic",
        "credentials": {
            "account_sid": "<account_sid>",
            "auth_token": "<auth_token>"
        }
    }
}

ENDPOINTS = [
    {
        "name": "ads",
        "path": "/Ads",
        "method": "GET",
        "data_selector": "Ads",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "api_submissions",
        "path": "/APISubmissions",
        "method": "GET",
        "data_selector": "APISubmission",
        "primary_key": "batch_id",
        "incremental": {
            "cursor_path": "submission_date",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "actions",
        "depends_on": "campaigns",
        "param_mapping": {"CampaignId": "id"}
    }
]
```

This configuration guide is designed to help you set up a dlt data pipeline for the Impact API, ensuring efficient data extraction and processing. Adjust the configurations as needed based on specific API requirements and data needs.

---
*dlt REST API Source Configuration Guide*