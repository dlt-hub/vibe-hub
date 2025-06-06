# Singer Tap Basecone REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Basecone API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.basecone.com/v1/`
- **Version Path**: `/v1`
- **Common Prefixes**: None

### Authentication
- **Type**: `basic`
- **Header**: `Authorization`
- **Format**: `Basic {auth_token}`

## 2. Available Endpoints

### Endpoint: Transaction Collection

**Endpoint Details:**
- **Path**: `/transactions`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `companyId`: The ID of the company.
  - `transactionDate`: The date for which transactions are requested, in `YYYY-MM-DD` format.
- **Optional Query Parameters**: None
- **Response Data Structure**: JSON object with a key `transactions` containing an array of transaction records.

**Pagination Configuration:**
- **Type**: `single_page` (No pagination required as each request is for a specific date)
- **Parameter Names**: Not applicable
- **Next Page Determination**: Not applicable
- **Default/Maximum Page Sizes**: Not applicable

**Data Extraction:**
- **JSONPath**: `$.transactions`
- **Record Identification**: Each record is identified by the `transactionID` field.
- **Key Fields**: `transactionID`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter**: `transactionDate`
- **Date/Timestamp Field**: `transactionDate`
- **Expected Format**: `YYYY-MM-DD`
- **Recommended Initial Values**: Start from the earliest date of interest, e.g., `2020-01-01`.

## 4. Endpoint Dependencies

**Resource Relationships**:
- **Dependencies**: None specified for the transaction collection endpoint.
- **Identifier Mapping**: Not applicable
- **Processing Order**: Not applicable

## 5. API Behavior & Limits

**Rate Limiting**:
- **Requests per Second**: Not specified
- **Burst Limits**: Not specified
- **Recommended Delays**: Implement retry logic with exponential backoff in case of rate limit errors.

**Special Requirements**:
- **Custom Headers**: `Authorization` header with the format `Basic {auth_token}`.
- **Response Format Considerations**: Ensure JSON parsing is robust to handle any unexpected data structures.
- **Error Handling Patterns**: Implement error handling for HTTP status codes, especially 404 (not found) and 500 (server error).
- **Data Type Specifications**: Ensure date fields are parsed correctly using the provided timezone mappings.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.basecone.com/v1/",
    "auth": {
        "type": "basic",
        "location": "header",
        "name": "Authorization",
        "format": "Basic {auth_token}"
    }
}

ENDPOINTS = [
    {
        "name": "transaction_collection",
        "path": "/transactions",
        "method": "GET",
        "data_selector": "transactions",
        "primary_key": "transactionID",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "transactionDate",
            "param_name": "transactionDate",
            "format": "YYYY-MM-DD"
        }
    }
]

DEPENDENCIES = []
```

## Focus Areas

1. **API Communication**: Ensure correct HTTP methods and headers are used for authentication.
2. **Data Location**: Extract data from the `transactions` array in the JSON response.
3. **Pagination**: Not applicable for this endpoint as each request targets a specific date.
4. **Incremental Updates**: Use the `transactionDate` parameter to fetch data incrementally.
5. **Resource Dependencies**: No dependencies for the transaction collection endpoint.

This configuration guide provides all necessary details to set up a dlt data pipeline for the Basecone API, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*