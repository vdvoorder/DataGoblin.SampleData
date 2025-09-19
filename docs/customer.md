# SampleData.Customer

Generates customer dimension data based on provided CustomerKey values, with options for referential integrity violations and missing values.

## Parameters

- `customer_keys` (ANYREF): Column containing CustomerKey values
- `RI_violations` (INT64): Number of keys to randomly exclude (creates referential integrity issues)
- `missing_values` (BOOLEAN): Whether to include missing values (10-25% per column)
- `seed` (INT64): Random seed for reproducibility

## Returns

Customer records with the following columns:
- CustomerKey, CustomerName
- CustomerType (B2B, B2C, Partner), CustomerSegment (Premium, Standard, Basic, Enterprise)
- Industry, Country, Region, City
- LifetimeValue, CreditLimit
- AccountOpenDate, Status (Active, Inactive, Suspended, Closed)

## Usage Example

```dax
SAMPLEDATA.CUSTOMER(
    'Orders'[CustomerKey], // Use customer keys from Orders table
    0,                     // No referential integrity violations
    FALSE,                 // No missing values
    1337                   // Random seed
)
```

## Features

- Generates 20% more records than unique keys provided (simulates orphaned dimension records)
- Realistic company names (30% B2B) and individual names (70% B2C/Partner)
- Fantasy-themed naming conventions (Goblin Industries, Dragon Corp, etc.)
- Geographic distribution across 10 countries with appropriate cities
- Customer segmentation with credit limits and lifetime values
- Configurable missing values for testing data quality scenarios
- Referential integrity violation simulation for testing