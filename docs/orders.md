# SampleData.Orders

Generates a comprehensive orders fact table with order lines, SAP-style numbering, and realistic line-level variations.

## Parameters

- `num_orders` (INT64): Number of orders to generate
- `start_date` (DATETIME): Start date for orders
- `end_date` (DATETIME): End date for orders
- `num_customers` (INT64): Number of unique customers
- `num_products` (INT64): Number of unique products
- `num_stores` (INT64): Number of store locations
- `seed` (INT64): Random seed for reproducibility

## Returns

Order lines with the following columns:
- OrderNumber, OrderLineNumber
- CustomerKey, ProductKey, StoreKey
- Currency
- OrderDate, RequestedDeliveryDate, ApprovedDate, FulfilledDate, ShippedDate, DeliveredDate
- OrderStatus, DeliverySpeed
- Quantity, UnitPrice

## Usage Example

```dax
SAMPLEDATA.ORDERS (
    100,                    // Generate 100 orders
    DATE(2025, 09, 01),    // Start date
    DATE(2025, 09, 18),    // End date
    100,                   // 100 unique customers
    250,                   // 250 unique products
    5,                     // 5 store locations
    1337                   // Random seed
)
```

## Features

- Generates realistic order progressions through statuses (Pending → Approved → Fulfilled → Shipped → Delivered)
- Multiple order lines per order (1-5 lines typically)
- SAP-style order numbering starting from 4500000000
- Line-level date variations (25% of lines have different dates from order header)
- Multi-currency support (EUR, CAD, GBP, SEK, NOK, AUD, NZD)
- Realistic delivery speed variations (2, 5, or 7 days)
- Currency-specific pricing adjustments