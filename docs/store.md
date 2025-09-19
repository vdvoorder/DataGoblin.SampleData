# SampleData.Store

Generates store dimension data based on provided StoreKey values, with hierarchical region/district structure and realistic store attributes.

## Parameters

- `store_keys` (ANYREF): Column containing StoreKey values
- `RI_violations` (INT64): Number of keys to randomly exclude
- `missing_values` (BOOLEAN): Whether to include missing values (10-25% per column)
- `seed` (INT64): Random seed for reproducibility

## Returns

Store records with the following columns:
- StoreKey, StoreCode, StoreName
- StoreRegion (North, South, East, West, Central), StoreDistrict
- StoreType (Flagship, Express, Outlet, Warehouse, Boutique, Mega)
- StoreFormat (Physical, Online, Hybrid, Pop-up)
- Country, State, City, PostalCode
- SquareFootage, EmployeeCount, ParkingSpaces
- StoreManager, OpenDate, LastRenovationDate, Status

## Usage Example

```dax
SAMPLEDATA.STORE (
    'Orders'[StoreKey],    // Use store keys from Orders table
    0,                     // No referential integrity violations
    FALSE,                 // No missing values
    1337                   // Random seed
)
```

## Features

- Hierarchical geographic structure (Region → District → Store)
- Multiple store formats including online and hybrid models
- Realistic store names with location descriptors (Downtown Plaza North, Mall Express #01)
- Global presence across 10 countries with appropriate cities
- Store metrics including square footage, employee count, and parking spaces
- Manager assignments with realistic names
- Store lifecycle tracking with open dates and renovation history
- 20% more records than unique keys provided (simulates orphaned dimension records)