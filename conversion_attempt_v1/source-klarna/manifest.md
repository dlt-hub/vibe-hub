# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for resources such as payouts and transactions, utilizing a cursor-based pagination strategy and basic HTTP authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "payout_date",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "payouts",
            "endpoint": {
                "path": "/v1/payouts",
                "data_selector": "payouts",
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('pagination', {}).get('next', {})"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "transactions",
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('pagination', {}).get('next', {})"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic HTTP Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

## Resources

### Payouts

- **Endpoint Path**: `/v1/payouts`
- **HTTP Method**: GET
- **Data Selector**: `payouts`
- **Primary Key**: `payout_date`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Limit**: 500
  - **Stop Condition**: No `next` value in response

### Transactions

- **Endpoint Path**: `/v1/transactions`
- **HTTP Method**: GET
- **Data Selector**: `transactions`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Limit**: 500
  - **Stop Condition**: No `next` value in response

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Payouts

- **totals**: Object containing total payout data
  - **commission_amount**: Integer, total commissions
  - **repay_amount**: Integer, total repaid amount
  - **sale_amount**: Integer, total sales
  - **holdback_amount**: Integer, total holdback
  - **tax_amount**: Integer, total tax
  - **settlement_amount**: Integer, total settlement
  - **fee_correction_amount**: Integer, total fee correction
  - **reversal_amount**: Integer, total reversals
  - **release_amount**: Integer, total release from holdback
  - **return_amount**: Integer, total returns
  - **fee_amount**: Integer, total fees
  - **charge_amount**: Integer, total charges
  - **credit_amount**: Integer, total credits
- **payment_reference**: String, reference ID of the payout
- **payout_date**: String, ISO 8601 date-time
- **currency_code**: String, ISO 4217 currency code
- **merchant_settlement_type**: String, net or gross amounts
- **merchant_id**: String, merchant ID
- **transactions**: String, link to transactions

### Transactions

- **amount**: Integer, transaction amount
- **merchant_id**: String, merchant ID
- **shipping_address_country**: String, shipping country
- **consumer_vat**: Array, consumer VAT ID
- **capture_id**: String, capture ID
- **merchant_reference1**: String, merchant reference
- **sale_date**: String, ISO 8601 date-time
- **type**: String, transaction type
- **capture_date**: String, ISO 8601 date-time
- **payment_reference**: String, payout reference
- **order_id**: String, order ID
- **payout**: String, link to payout
- **refund_id**: String, refund ID
- **short_order_id**: String, short order ID
- **merchant_reference2**: String, merchant reference
- **currency_code**: String, ISO 4217 currency code
- **purchase_country**: String, ISO Alpha-2 country code
- **vat_rate**: Integer, VAT rate
- **vat_amount**: Integer, VAT amount
- **shipping_country**: String, shipping country
- **initial_payment_method_type**: String, payment method
- **initial_number_of_installments**: Integer, number of installments
- **initial_payment_method_monthly_downpayments**: Integer, monthly downpayments
- **merchant_capture_reference**: String, capture reference
- **merchant_refund_reference**: String, refund reference
- **detailed_type**: String, detailed transaction type
- **tax_in_currency_of_registration_country**: Integer, tax amount in registration currency
- **currency_code_of_registration_country**: String, registration currency code

This configuration provides a comprehensive guide to setting up a REST API source using dltHub, ensuring a clean and vendor-neutral integration.