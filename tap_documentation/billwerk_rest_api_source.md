# Billwerk REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Billwerk API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://<your-domain>.billwerk.com/api/v1/`
- Ensure to replace `<your-domain>` with your specific domain.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://<your-domain>.billwerk.com/oauth/token/`
- **Required Scopes**: Not specified, but ensure your client has the necessary permissions.
- **Parameters**:
  - **client_id**: Your OAuth client ID.
  - **client_secret**: Your OAuth client secret.
  - **grant_type**: `client_credentials`

## 2. Available Endpoints

### Endpoint Details

#### Contracts
- **Path**: `/contracts`
- **Method**: `GET`
- **Response Data Structure**: JSON array of contract objects.
- **Primary Key**: `Id`

#### Customers
- **Path**: `/customers`
- **Method**: `GET`
- **Response Data Structure**: JSON array of customer objects.
- **Primary Key**: `Id`

#### Invoices
- **Path**: `/invoices`
- **Method**: `GET`
- **Response Data Structure**: JSON array of invoice objects.
- **Primary Key**: `Id`

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**:
  - **limit**: `take`
  - **offset**: Not explicitly used; pagination is handled by the `take` parameter and date windowing.
- **Default/Maximum Page Sizes**: 500

### Data Extraction
- **JSONPath**: Directly extract from the response as it is a JSON array.
- **Key Fields**: Use the `Id` field to uniquely identify records.

## 3. Incremental Data Loading

### Incremental Support

#### Invoices
- **Query Parameter**: `dateFrom` and `dateTo`
- **Date/Timestamp Field**: `Created`
- **Format**: ISO 8601
- **Recommended Initial Values**: Use the `start_date` from the configuration.

#### Orders
- **Query Parameter**: `dateFrom` and `dateTo`
- **Date/Timestamp Field**: `CreatedAt`
- **Format**: ISO 8601

## 4. Endpoint Dependencies

### Resource Relationships
- **Invoices** may depend on **Customers** for customer details.
- **Orders** may depend on **Contracts** for contract details.
- **Mapping Identifiers**: Use `Id` fields to map between related resources.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined; implement backoff strategies for HTTP errors.
- **Burst Limits**: Implement a delay of 10 seconds on HTTP errors.

### Special Requirements
- **Custom Headers**: Ensure `Authorization` header is set with the Bearer token.
- **Response Format Considerations**: JSON format.
- **Error Handling Patterns**: Use backoff strategy for retrying on HTTP errors.
- **Data Type Specifications**: Ensure date fields are handled in ISO 8601 format.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://<your-domain>.billwerk.com/api/v1/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://<your-domain>.billwerk.com/oauth/token/",
        "client_id": "<your-client-id>",
        "client_secret": "<your-client-secret>",
        "grant_type": "client_credentials"
    }
}

ENDPOINTS = [
    {
        "name": "contracts",
        "path": "/contracts",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "Id",
        "pagination": {
            "type": "offset",
            "limit_param": "take",
            "limit": 500
        }
    },
    {
        "name": "customers",
        "path": "/customers",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "Id",
        "pagination": {
            "type": "offset",
            "limit_param": "take",
            "limit": 500
        }
    },
    {
        "name": "invoices",
        "path": "/invoices",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "Id",
        "pagination": {
            "type": "offset",
            "limit_param": "take",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "Created",
            "param_name": "dateFrom",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "invoices",
        "depends_on": "customers",
        "param_mapping": {"customer_id": "Id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Billwerk API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*