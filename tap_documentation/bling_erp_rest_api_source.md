# Bling Erp REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Bling ERP system. It includes detailed instructions on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.bling.com.br/`
- **Version Path**: `/v2/`
- **Example**: `https://api.bling.com.br/v2/`

### Authentication
- **Type**: `api_key`
- **Location**: `query`
- **Parameter Name**: `apikey`
- **Format**: The API key is passed as a query parameter in each request.

## 2. Available Endpoints

### Orders Endpoint
- **Path**: `/pedidos/page={page}/json`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `filters`: `dataAlteracao[{start_time} TO {end_time}]`
  - `apikey`: Your API key
  - `historico`: `true`
- **Response Data Structure**: JSON with a key `retorno` containing `pedidos`.

#### Pagination Configuration
- **Type**: `page_number`
- **Parameter Name**: `page`
- **Default Page Size**: Determined by the API, typically 100 records per page.
- **Next Page Determination**: Increment the `page` parameter until no more data is returned.

#### Data Extraction
- **JSONPath**: `retorno.pedidos`
- **Primary Key**: `id`
- **Key Fields**: `dataAlteracao` for incremental updates.

### Invoices Endpoint
- **Path**: `/notasfiscais/page={page}/json`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `filters`: `dataEmissao[{start_time} TO {end_time}]`
  - `apikey`: Your API key
- **Response Data Structure**: JSON with a key `retorno` containing `notasfiscais`.

#### Pagination Configuration
- **Type**: `page_number`
- **Parameter Name**: `page`
- **Default Page Size**: Determined by the API, typically 100 records per page.
- **Next Page Determination**: Increment the `page` parameter until no more data is returned.

#### Data Extraction
- **JSONPath**: `retorno.notasfiscais`
- **Primary Key**: `id`
- **Key Fields**: `dataEmissao` for incremental updates.

## 3. Incremental Data Loading

### Orders
- **Incremental Parameter**: `filters` with `dataAlteracao`
- **Date Field**: `dataAlteracao`
- **Format**: `YYYY-MM-DDTHH:MM:SSZ` (ISO 8601)
- **Initial Value**: Use the `start_date` from the configuration.

### Invoices
- **Incremental Parameter**: `filters` with `dataEmissao`
- **Date Field**: `dataEmissao`
- **Format**: `YYYY-MM-DDTHH:MM:SSZ` (ISO 8601)
- **Initial Value**: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

- **Orders and Invoices**: These endpoints are independent and can be processed in parallel.
- **Mapping Identifiers**: Not applicable as there are no direct dependencies between these endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: The API documentation does not specify exact limits, but it is recommended to implement a delay between requests to avoid throttling.
- **Burst Limits**: Implement a retry mechanism with exponential backoff in case of rate limiting.

### Special Requirements
- **Custom Headers**: None required beyond standard HTTP headers.
- **Response Format**: JSON
- **Error Handling**: Check for HTTP status codes and handle non-200 responses appropriately.
- **Data Type Specifications**: Ensure date fields are correctly parsed and converted to the appropriate timezone.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.bling.com.br/v2/",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "apikey"
    }
}

ENDPOINTS = [
    {
        "name": "orders",
        "path": "/pedidos/page={page}/json",
        "method": "GET",
        "data_selector": "retorno.pedidos",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "dataAlteracao",
            "param_name": "filters",
            "format": "iso"
        }
    },
    {
        "name": "invoices",
        "path": "/notasfiscais/page={page}/json",
        "method": "GET",
        "data_selector": "retorno.notasfiscais",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "dataEmissao",
            "param_name": "filters",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and instructions to set up a dlt data pipeline for the Bling ERP REST API, ensuring efficient data extraction and incremental updates.

---
*dlt REST API Source Configuration Guide*