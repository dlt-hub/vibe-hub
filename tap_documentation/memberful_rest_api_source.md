# Memberful REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Memberful REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://vox.memberful.com/api/graphql`
- This is the endpoint for all GraphQL queries to the Memberful API.

### Authentication
- **Type**: `bearer`
- **Token Format**: The API requires a Bearer token for authentication.
- **Header Name**: `Authorization`
- **Token Retrieval**: The token should be provided in the configuration as `api_key`.

## 2. Available Endpoints

### Activities Endpoint
- **Path**: `/activities`
- **HTTP Method**: `POST`
- **Query Parameters**: `afterCursor` for pagination
- **Response Data Structure**: JSON with `pageInfo` and `edges` containing `node` data

### Members Endpoint
- **Path**: `/members`
- **HTTP Method**: `POST`
- **Query Parameters**: `afterCursor` for pagination
- **Response Data Structure**: JSON with `pageInfo` and `edges` containing `node` data

### Orders Endpoint
- **Path**: `/orders`
- **HTTP Method**: `POST`
- **Query Parameters**: `afterCursor` for pagination
- **Response Data Structure**: JSON with `pageInfo` and `edges` containing `node` data

### Plans Endpoint
- **Path**: `/plans`
- **HTTP Method**: `POST`
- **Query Parameters**: None for pagination
- **Response Data Structure**: JSON with a list of plans

### Subscriptions Endpoint
- **Path**: `/subscriptions`
- **HTTP Method**: `POST`
- **Query Parameters**: `afterCursor` for pagination
- **Response Data Structure**: JSON with `pageInfo` and `edges` containing `node` data

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `afterCursor`
- **Next Page Determination**: Use `pageInfo.endCursor` from the response to fetch the next page.
- **Default/Maximum Page Sizes**: Not explicitly defined; depends on API response.

### Data Extraction
- **JSONPath**: Use `$.data.<stream_name>.edges[*].node` to locate the data array.
- **Primary Key**: Each stream has a unique identifier, typically `id`.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `afterCursor` for pagination
- **Date/Timestamp Field**: `createdAt` for `activities`, `orders`, and `subscriptions`
- **Format**: Unix timestamps
- **Initial Values**: Use the `start_date` configuration parameter for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: 
  - `Orders` and `Subscriptions` depend on `Members` for member IDs.
  - `Activities` may relate to `Members` through member IDs.
- **Mapping Identifiers**: Use `member.id` from `Members` in related endpoints.
- **Processing Order**: Fetch `Members` first, followed by dependent streams.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined; monitor API responses for rate limit headers.
- **Burst Limits**: Implement retry logic with exponential backoff.

### Special Requirements
- **Custom Headers**: `User-Agent` can be set via configuration.
- **Response Format**: JSON
- **Error Handling**: Implement error handling for GraphQL errors and HTTP status codes.
- **Data Type Specifications**: Ensure correct data types as defined in the schema for each stream.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://vox.memberful.com/api/graphql",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "activities",
        "path": "/activities",
        "method": "POST",
        "data_selector": "$.data.activities.edges[*].node",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "afterCursor",
            "cursor_path": "$.data.activities.pageInfo.endCursor"
        },
        "incremental": {
            "cursor_path": "createdAt",
            "param_name": "afterCursor",
            "format": "unix"
        }
    },
    # Additional endpoints follow the same structure...
]

DEPENDENCIES = [
    {
        "endpoint": "orders",
        "depends_on": "members",
        "param_mapping": {"member_id": "id"}
    },
    # Additional dependencies...
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Memberful API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*