# Ccxt REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the `tap-ccxt` REST API source. The configuration is designed to extract data from cryptocurrency exchanges using the CCXT library.

## 1. Client Configuration

### Base URL
- The `tap-ccxt` does not use a single base URL as it interacts with multiple exchanges. Each exchange has its own base URL managed internally by the CCXT library.

### Authentication
- **Type**: `api_key`
- **Location**: Header
- **Parameter Name**: `apiKey` and `secret`
- **Format**: Each exchange requires its own API key and secret, which are passed during the initialization of the exchange object.

## 2. Available Endpoints

### OHLCV Data Endpoint
- **Path**: Managed internally by CCXT for each exchange.
- **HTTP Method**: GET
- **Required Parameters**:
  - `symbol`: The trading pair (e.g., "BTC/USD").
  - `timeframe`: The granularity of the data (e.g., "1m", "1h").
  - `since`: Timestamp in milliseconds to fetch data from.
- **Response Data Structure**: An array of OHLCV data points, each containing:
  - `timestamp`
  - `open`
  - `high`
  - `low`
  - `close`
  - `volume`

### Pagination Configuration
- **Type**: `single_page`
- **Details**: The CCXT library handles pagination internally for OHLCV data. Data is fetched in chunks based on the `since` parameter until the current time.

### Data Extraction
- **JSONPath**: Direct extraction from the response array.
- **Primary Key**: Combination of `exchange`, `base`, `quote`, `timeframe`, and `timestamp`.
- **Key Fields**: `timestamp` uniquely identifies each record within a timeframe.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `since`
- **Date/Time Field**: `timestamp` in milliseconds
- **Format**: Unix timestamp in milliseconds
- **Initial Value**: Configurable via `start_date` in the configuration for each trading pair.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None explicitly defined, but data for each trading pair is fetched independently.
- **Mapping**: Each trading pair is defined by `base` and `quote` currencies.

## 5. API Behavior & Limits

### Rate Limiting
- **Details**: Rate limits are managed by the CCXT library and vary by exchange. Users should refer to the specific exchange's documentation for rate limits.
- **Burst Limits**: Handled internally by CCXT.

### Special Requirements
- **Custom Headers**: None beyond API key and secret.
- **Response Format**: JSON array of OHLCV data.
- **Error Handling**: Managed by CCXT, which raises exceptions for common errors like `BadSymbol`.

## Configuration Example

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "exchanges": [
        {
            "id": "binance",
            "api_key": "your_api_key",
            "secret": "your_secret",
            "pairs": [
                {
                    "base": "BTC",
                    "quote": "USDT",
                    "timeframe": "1m",
                    "start_date": "2023-01-01T00:00:00Z"
                }
            ]
        }
    ]
}

ENDPOINTS = [
    {
        "name": "ohlcv",
        "method": "GET",
        "data_selector": "direct",
        "primary_key": ["exchange", "base", "quote", "timeframe", "timestamp"],
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "timestamp",
            "param_name": "since",
            "format": "unix_milliseconds"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the `tap-ccxt` source, enabling efficient data extraction from multiple cryptocurrency exchanges.

---
*dlt REST API Source Configuration Guide*