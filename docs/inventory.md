# SampleData.Inventory

Generates warehouse inventory snapshots with weekly snapshots for past weeks and daily snapshots for the current week.

## Parameters

- `num_days` (INT64): Number of days to generate
- `start_date` (DATETIME): Start date for snapshots
- `num_products` (INT64): Number of products to track
- `num_warehouses` (INT64): Number of warehouse locations
- `seed` (INT64): Random seed for reproducibility

## Returns

Inventory snapshots with the following columns:
- SnapshotDateKey, ProductKey, WarehouseKey
- OnHandQuantity, ReservedQuantity, AvailableQuantity
- InTransitQuantity, ProjectedQuantity
- ReorderPoint, SafetyStockLevel
- BelowReorderPoint, BelowSafetyStock, OutOfStock (flags)
- UnitCost, InventoryValue

## Usage Example

```dax
SAMPLEDATA.INVENTORY (
    90,                     // 90 days of data
    DATE(2025, 06, 01),    // Start date
    250,                   // 250 products
    3,                     // 3 warehouses
    1337                   // Random seed
)
```

## Features

- Weekly snapshots for historical data (Sundays only)
- Daily snapshots for current week
- Realistic inventory depletion patterns with seasonal variance
- Automatic reorder calculations based on safety stock levels
- In-transit inventory on weekends (Friday-Sunday)
- Product-specific demand patterns and cost variations
- Stock status flags for reporting and alerting