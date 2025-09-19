# SampleData.Product

Generates product dimension data based on provided ProductKey values, with hierarchical categories and realistic product attributes.

## Parameters

- `product_keys` (ANYREF): Column containing ProductKey values
- `RI_violations` (INT64): Number of keys to randomly exclude
- `missing_values` (BOOLEAN): Whether to include missing values (10-25% per column)
- `seed` (INT64): Random seed for reproducibility

## Returns

Product records with the following columns:
- ProductKey, SKU, ProductName
- ProductCategory, ProductSubcategory (hierarchical structure)
- Brand, Color, Size, Weight
- StandardCost, ListPrice
- ProductStatus (Active, Discontinued, Out of Stock, Pending)
- LaunchDate, DiscontinuedDate

## Usage Example

```dax
SAMPLEDATA.PRODUCT (
    'Orders'[ProductKey],  // Use product keys from Orders table
    0,                     // No referential integrity violations
    FALSE,                 // No missing values
    1337                   // Random seed
)
```

## Features

- Hierarchical product categories (5 main categories, 5-6 subcategories each)
- Realistic product naming with quality modifiers (Ultra, Pro, Elite, Premium, etc.)
- Fantasy-themed brand names (GoblinTech, DragonForge, MysticBrand, etc.)
- Proper SKU formatting with category codes
- Product lifecycle management with launch and discontinuation dates
- Realistic cost/price relationships with markup calculations
- Wide variety of colors, sizes, and weights
- 20% more records than unique keys provided (simulates orphaned dimension records)