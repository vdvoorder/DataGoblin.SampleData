# SampleData.RLS_UserMapping

Generates user-to-value permission mappings for testing Row-Level Security (RLS) with realistic permission distribution patterns. Enables dynamic, fine-grained security testing without external data sources or Azure AD setup.

## Parameters

- `dimension_column` (ANYREF): Column containing dimension values to map (e.g., Store[Country])
- `role_name` (TEXT): Role name for this permission level (e.g., "Country", "State")
- `num_users` (INT64): Number of test users to generate
- `avg_permissions_per_user` (DECIMAL): Average permissions per user (can be decimal like 2.5)
- `stddev_permissions_per_user` (DECIMAL): Standard deviation of permissions per user (controls variance)
- `seed` (INT64): Random seed for reproducibility

## Returns

Permission mapping records with the following columns:
- UserPrincipalName (e.g., shadow.prime@data-goblins.com)
- Role (from role_name parameter)
- Value (from dimension_column)

## Usage Example

```dax
// Generate Country-level permissions
Country_RLS = DataGoblins.SampleData.RLS_UserMapping(
    Store[Country],  // Dimension values to assign
    "Country",       // Role name
    10,              // 10 test users
    2.0,             // Average 2 permissions per user
    1.0,             // Standard deviation of 1
    42               // Seed for reproducibility
)

// Generate State-level permissions (same seed = same users = overlapping permissions)
State_RLS = DataGoblins.SampleData.RLS_UserMapping(
    Store[State],
    "State",
    10,              // Same user count
    3.0,             // Average 3 permissions per user (more granular)
    1.5,             // Higher variance
    42               // Same seed = overlapping users
)

// Combine for full RLS table
RLS_User_Permissions = UNION(Country_RLS, State_RLS)
```

## Features

- **Realistic Permission Distribution**: Uses normal distribution (via Central Limit Theorem) to generate realistic permission counts per user
- **Natural Orphaned Users**: Users with zero permissions emerge naturally from distribution tail (realistic "no access" scenarios)
- **Fantasy/Sci-Fi Themed Users**: Generates themed usernames like shadow.prime@data-goblins.com, nexus.core@quantum-sys.com
- **Statistical Control**: Adjustable mean and standard deviation for permission counts enables testing different access patterns
- **Composable Security**: Same seed across calls generates overlapping users, enabling hierarchical security testing
- **Reproducible**: Same seed produces identical user-permission mappings
- **Fine-Grained Security**: Supports multiple hierarchy levels (Country, State, Region, etc.) through composition

## Permission Distribution Behavior

With `avg_permissions_per_user = 2.5` and `stddev_permissions_per_user = 1.0`:
- Most users get 1-4 permissions (~68%)
- Some users get 0 permissions (left tail, realistic "no access")
- Some users get 5+ permissions (right tail, realistic "power users")
- Total permissions ≈ `num_users × avg_permissions_per_user`

Adjust `stddev_permissions_per_user` to control variance:
- Low (0.5): Tight clustering, most users have similar permissions
- Medium (1.0-1.5): Realistic variance, balanced distribution
- High (2.0+): Wide spread, clear distinction between restricted and power users

## Testing Scenarios Enabled

- **Hierarchy Security**: Generate Country-level and State-level permissions with same seed to test cascading filters
- **User Segmentation**: Test users with broad access vs. restricted access
- **Orphaned Users**: Validate behavior when users have no permissions
- **Permission Overlap**: Test many-to-many user-to-value relationships
- **Performance Testing**: Generate large permission sets to test RLS filter performance
- **Dynamic RLS**: Parameterize permission generation for instant test data regeneration
