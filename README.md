# DataGoblins.SampleData

This repository (https://github.com/data-goblin/DataGoblin.SampleData) contains examples and sample files for the DataGoblins.SampleData DaxLib library (https://daxlib.org/package/DataGoblins.SampleData#overview). It provides sample data generation functions for Power BI semantic model or report development, or testing anything that requires semantic models and/or reports.

## Purpose

The purpose of this sample data is for personal projects, testing, prototyping, or just general fucking around in Power BI. It's not advised to use in production projects or for commercial use.

### Why generate it in DAX?

**I've got a better question:** Why not?

**But seriously:** It's fast and easy to adjust and make changes. Giving a demo and want to show what an RI violation looks like? Change the RI violation parameter in a dimension table from 0 to 10. Done in 1-3 seconds.

### Warning

While in beta, I may remove or adjust both functions and parameters. While out of beta, I will try to avoid changing parameter order or amount.

## Available Functions

The functions generate the following data.

### Fact tables

- **[SampleData.Orders](docs/orders.md)** - Generates orders fact table with order lines and realistic variations
- **[SampleData.Inventory](docs/inventory.md)** - Generates warehouse inventory snapshots (weekly)

### Dimension tables

- **[SampleData.Customer](docs/customer.md)** - Generates customer dimension data with optional RI violations
- **[SampleData.Product](docs/product.md)** - Generates product dimension data with hierarchical categories
- **[SampleData.Store](docs/store.md)** - Generates store dimension data with region/district structure
- **[SampleData.Dates](docs/dates.md)** - Generates comprehensive date dimension table

## Example Model

For a complete working example, see the Power BI project file:
[sample-pbip.pbip](sample-files/sample-pbip/sample-pbip.pbip)

## AI Use Disclaimer

The DAX functions in this library were created by agentic coding tools and workflows with minor assistance and orchestration by myself. 

For more information, see [this whitepaper on SQLBI](https://www.sqlbi.com/whitepapers/ai-workflows-and-agentic-development-for-power-bi-an-initial-review/).