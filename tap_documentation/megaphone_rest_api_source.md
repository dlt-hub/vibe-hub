# Megaphone REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Megaphone API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://cms.megaphone.fm/api/`
- **Version Path**: Not explicitly versioned in the URL, but specific paths are used for different resources.

### Authentication
- **Type**: `api_key`
- **Location**: Header
- **Parameter Name**: `Authorization`
- **Format**: `Token token={auth_token}`

## 2. Available Endpoints

### Networks
- **Path**: `/organizations/{organization_id}/networks`
- **HTTP Method**: GET
- **Response Data Structure**: JSON array of network objects
- **Pagination**: Not explicitly defined, assumed single page

### Podcasts
- **Path**: `/networks/{network_id}/podcasts`
- **HTTP Method**: GET
- **Response Data Structure**: JSON array of podcast objects
- **Pagination**: Not explicitly defined, assumed single page

### Episodes
- **Path**: `/podcasts/{podcast_id}/episodes`
- **HTTP Method**: GET
- **Response Data Structure**: JSON array of episode objects
- **Pagination**: Not explicitly defined, assumed single page

### Campaigns
- **Path**: `/organizations/{organization_id}/campaigns`
- **HTTP Method**: GET
- **Response Data Structure**: JSON array of campaign objects
- **Pagination**: Not explicitly defined, assumed single page

### Campaign Orders
- **Path**: `/campaigns/{campaign_id}/orders`
- **HTTP Method**: GET
- **Response Data Structure**: JSON array of order objects
- **Pagination**: Not explicitly defined, assumed single page

### Promo Orders
- **Path**: `/promos`
- **HTTP Method**: GET
- **Response Data Structure**: JSON array of promo order objects
- **Pagination**: Not explicitly defined, assumed single page

## 3. Incremental Data Loading

- **Incremental Support**: Not explicitly defined in the provided code. Typically, this would involve using a date or timestamp field to fetch only new or updated records.
- **Date/Timestamp Field**: Not specified
- **Expected Format**: ISO 8601 or Unix timestamps are common
- **Recommended Initial Values**: Not specified

## 4. Endpoint Dependencies

### Resource Relationships
- **Podcasts** depend on **Networks**: Requires `network_id` from Networks
- **Episodes** depend on **Podcasts**: Requires `podcast_id` from Podcasts
- **Campaign Orders** depend on **Campaigns**: Requires `campaign_id` from Campaigns
- **Promo Order Advertisements** depend on **Promo Orders**: Requires `promo_id` from Promo Orders

### Mapping Identifiers
- **Networks to Podcasts**: `network_id`
- **Podcasts to Episodes**: `podcast_id`
- **Campaigns to Campaign Orders**: `campaign_id`
- **Promo Orders to Promo Order Advertisements**: `promo_id`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined in the provided code. Check API documentation for specific limits.
- **Burst Limits and Recommended Delays**: Implemented a 1-second delay in `EpisodesStream`.

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration.
- **Response Format Considerations**: JSON responses are expected.
- **Error Handling Patterns**: Custom error handling for 403 errors related to access permissions.
- **Data Type Specifications**: JSON data with specific schemas for each stream.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://cms.megaphone.fm/api/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Token token={auth_token}"
    }
}

ENDPOINTS = [
    {
        "name": "networks",
        "path": "/organizations/{organization_id}/networks",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "podcasts",
        "path": "/networks/{network_id}/podcasts",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "episodes",
        "path": "/podcasts/{podcast_id}/episodes",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "podcasts",
        "depends_on": "networks",
        "param_mapping": {"network_id": "id"}
    },
    {
        "endpoint": "episodes",
        "depends_on": "podcasts",
        "param_mapping": {"podcast_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Megaphone API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*