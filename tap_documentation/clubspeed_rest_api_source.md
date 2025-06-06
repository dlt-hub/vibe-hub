# Clubspeed REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Clubspeed API. It outlines the necessary parameters and configurations required to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{subdomain}.clubspeedtiming.com/api/index.php/`
- **Version Path**: The API does not explicitly use versioning in the URL, but it uses different API versions internally (e.g., V1, V2).

### Authentication
- **Type**: `api_key`
- **Location**: `query`
- **Parameter Name**: `key`
- **Format**: The API key is appended as a query parameter in the URL.

## 2. Available Endpoints

### Endpoint Details

Below are the details for each endpoint available in the Clubspeed API:

#### 1. Booking
- **Path**: `/booking.json`
- **HTTP Method**: `GET`
- **Query Parameters**: `key`, `page`, `limit`, `filter`, `order`
- **Response Data Structure**: JSON object with a key `bookings` containing an array of booking records.

#### 2. Customers
- **Path**: `/customers.json`
- **HTTP Method**: `GET`
- **Query Parameters**: `key`, `page`, `limit`, `filter`, `order`
- **Response Data Structure**: JSON object with customer details.

### Pagination Configuration

- **Type**: `page_number`
- **Parameter Names**: `page`, `limit`
- **Default/Maximum Page Sizes**: Default is 100 records per page.
- **Next Page Determination**: Increment the `page` parameter until no more records are returned.

### Data Extraction

- **JSONPath**: Use the key specific to each endpoint (e.g., `bookings`, `customers`) to locate the data array.
- **Primary Key**: Each record has a unique identifier (e.g., `onlineBookingsId` for bookings).

## 3. Incremental Data Loading

### Incremental Support

- **Query Parameter**: `filter`
- **Date/Timestamp Field**: Varies by endpoint (e.g., `createdDate` for `check_details`).
- **Expected Format**: ISO 8601 date format.
- **Recommended Initial Values**: Use the earliest possible date or a sensible default based on business requirements.

## 4. Endpoint Dependencies

### Resource Relationships

- **Dependencies**: Some endpoints may require data from others to establish relationships (e.g., `event_heat_details` may depend on `events`).
- **Mapping Identifiers**: Use unique identifiers provided in the response to map relationships.
- **Processing Order**: Ensure parent resources are processed before child resources.

## 5. API Behavior & Limits

### Rate Limiting

- **Requests per Second/Minute/Hour**: Not explicitly documented, but it's recommended to implement retry logic with exponential backoff.
- **Burst Limits**: Implement delays between requests to avoid hitting rate limits.

### Special Requirements

- **Custom Headers**: None required beyond standard HTTP headers.
- **Response Format Considerations**: JSON format.
- **Error Handling Patterns**: Handle HTTP 500 errors gracefully and implement retry logic.
- **Data Type Specifications**: Ensure correct data types are used as per the API documentation.

## Output Format

Below is an example configuration for the Clubspeed API using the dlt framework:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{subdomain}.clubspeedtiming.com/api/index.php/",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "key"
    }
}

ENDPOINTS = [
    {
        "name": "booking",
        "path": "/booking.json",
        "method": "GET",
        "data_selector": "bookings",
        "primary_key": "onlineBookingsId",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "createdDate",
            "param_name": "filter",
            "format": "iso"
        }
    },
    {
        "name": "customers",
        "path": "/customers.json",
        "method": "GET",
        "data_selector": "customers",
        "primary_key": "customerId",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "lastVisited",
            "param_name": "filter",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "event_heat_details",
        "depends_on": "events",
        "param_mapping": {"eventId": "id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Clubspeed API, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*