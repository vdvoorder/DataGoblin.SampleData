# DataGoblin.SampleData - AI Mission Context

> **Progressive Disclosure Design**: This file uses a three-level architecture (TL;DR → Core Mission → Deep Reference) to optimize AI context budgeting while providing comprehensive guidance when needed.

---

## TL;DR - Mission Statement

**Expand the DAX UDF library to enable infrastructure-as-code for Power BI demo/test data, prioritizing parameterized flexibility over industry-specific templates.**

**Critical Evaluation Stance**: Be a constructive critic. The human is skilled with words and persuasion—push back on ideas that sacrifice generalizability, add complexity without proportional value, or deviate from the core philosophy. Be neither a yes-man nor a pedant.

**Development Workflow**: NEVER commit or push without explicit user approval. User tests functions in Power BI first, reports bugs/issues, then approves final commit/push.

---

## Core Mission

### Why This Project Exists

**The Problem**: Power BI professionals maintain personal PBIX files with carefully groomed sample data (Contoso, AdventureWorks, etc.) for testing and demos. Introducing deliberate issues for demonstration purposes (RI violations, missing values, RLS testing) often causes unwanted side effects and risks corrupting the file if accidentally saved. The safer approach—managing base files and disposable copies—creates significant overhead for individual developers.

**The Solution**: DAX User-Defined Functions that generate realistic, parameterized sample data entirely within DAX. No data sources, no Power Query, no state management. Pure declarative data generation.

**The Value Proposition**:
- **Immutability**: Change a parameter, regenerate clean data—no corruption risk
- **Reproducibility**: Seed values ensure identical datasets across demos
- **Parameterized Testing**: Toggle RI violations, missing values, row counts on demand
- **Disposability**: Destructive testing without consequences—regenerate in seconds
- **Shareability**: Share function calls, not multi-megabyte PBIX files

**The Middle Ground**:
```
Individual Dev              This Library           SQLBI/Tabular Editor
├─ Ad-hoc files      →     ├─ Parameterized   →   ├─ Managed asset library
├─ High risk               ├─ Code-driven          ├─ Version controlled
├─ Hard to share           ├─ Reproducible         ├─ Full infrastructure
└─ Minimal overhead        └─ Shareable params     └─ High overhead
```

This library delivers **80% of professional demo infrastructure with 20% of the overhead**.

### Current State (v0.1.0-beta)

**Six Core Functions**:
1. `DataGoblins.SampleData.Orders` - Transaction fact table (14 columns)
2. `DataGoblins.SampleData.Inventory` - Warehouse snapshots (18 columns)
3. `DataGoblins.SampleData.Customer` - Customer dimension (12 columns)
4. `DataGoblins.SampleData.Product` - Product dimension (14 columns)
5. `DataGoblins.SampleData.Store` - Store dimension (17 columns)
6. `DataGoblins.SampleData.Dates` - Date dimension (44 columns)

**Key Technical Achievements**:
- Deterministic pseudo-randomness using MOD operations
- Parameterized data quality (RI violations, missing values)
- Realistic patterns (seasonal variance, product-type behaviors)
- Line-level variations (25% of order lines differ from header)
- Zero external dependencies (pure DAX)

**Documentation Quality**: 8.5/10 - comprehensive function docs, working sample project, minor gaps (LICENSE, diagrams, troubleshooting)

### Expansion Objectives

**Explore New Surfaces** (prioritized):
1. **Data Patterns**: Time series, slowly changing dimensions, many-to-many relationships, hierarchies, bridge tables
2. **Testing Scenarios**: Late-arriving facts, orphaned records, duplicates, outliers, ragged hierarchies
3. **Data Quality Issues**: Schema drift scenarios, type mismatches, referential cascades
4. **Advanced Features**: Row-level security test data, calculation group scenarios, field parameter configurations

**Explicitly Deprioritized**:
- Industry-specific functions (manufacturing, healthcare, finance) → reduces generalizability
- Complex domain models → favors flexibility and composability

---

## Evaluation Framework

### Decision Criteria for New Functions

When evaluating new function proposals, apply these filters sequentially:

#### 1. Generalizability Test
**Question**: Does this function enable scenarios across multiple industries/domains, or is it niche?

**Pass**: Time series generation, SCD Type 2 tracking, many-to-many bridges
**Fail**: Healthcare claim codes, manufacturing BOM structures, financial instrument hierarchies

**Rationale**: Library value compounds with broad applicability. Industry-specific functions create maintenance burden without proportional benefit.

#### 2. Parameterization Potential
**Question**: Can this function support meaningful parameter variation without becoming unwieldy?

**Pass**: `SCD2_Dimension(num_records, num_versions_avg, start_date, change_frequency, seed)`
**Fail**: Function with 15+ parameters where most users need default values for 12 of them

**Rationale**: Parameter explosion signals overfitting to specific scenarios. Aim for 4-8 meaningful parameters.

#### 3. Demonstration Value
**Question**: Does this enable demonstrating Power BI features/concepts that are currently hard to show?

**Pass**: Generating RLS-ready data with user-to-region mappings, many-to-many relationship scenarios
**Fail**: Replicating what existing functions already demonstrate

**Rationale**: Each function should unlock new demo/test scenarios. Redundancy dilutes library focus.

#### 4. Complexity-to-Value Ratio
**Question**: Is the implementation complexity justified by the scenarios it enables?

**Pass**: Moderate DAX complexity unlocking hard-to-demo features (bi-temporal tables, ragged hierarchies)
**Fail**: Extremely complex DAX for marginal scenario coverage

**Rationale**: Maintainability matters. If a human can manually create the scenario faster than understanding the function, it fails the test.

#### 5. Compositional Fit
**Question**: Does this integrate cleanly with existing functions, or does it require parallel infrastructure?

**Pass**: Function that accepts output from existing functions as input (e.g., keys from Orders → SCD2 dimension)
**Fail**: Function requiring entirely separate parameter conventions or return schemas

**Rationale**: Composability multiplies value. Functions should be building blocks, not silos.

### Red Flags - Auto-Reject Signals

- **"This would be perfect for [specific industry]"** → Generalizability fail
- **"Just add one more parameter for..."** (repeatedly) → Parameter explosion
- **"This is like [existing function] but..."** → Likely redundant
- **"It's complex but covers edge case X"** → Complexity-to-value imbalance
- **"We could hard-code the mapping for..."** → Sacrificing parameterization

### Green Flags - Strong Candidates

- **"This would let you demo [Power BI feature] that's currently hard to show"**
- **"Combines with existing functions to enable..."** → Composability
- **"Common testing scenario across domains"** → Generalizability
- **"Adjustable via 3-5 intuitive parameters"** → Right complexity level
- **"Makes destructive testing safe for..."** → Core value prop alignment

---

## Technical Constraints & Conventions

### DAX Implementation Standards

**Deterministic Randomness**:
```dax
VAR Seed = [seed_param]
VAR R1 = MOD(RecordID * 7919 + Seed, 1000000) / 1000000
VAR R2 = MOD(RecordID * 104729 + Seed * 17, 1000000) / 1000000
```
Use prime number multipliers for pseudo-random generation. Same seed = same output (reproducibility).

**Parameter Conventions**:
- `num_*` for counts (num_records, num_customers)
- `*_date` for date parameters (start_date, end_date)
- `seed` always last parameter, default value 42
- `RI_violations` and `missing_values` for data quality toggles (boolean or percentage)

**Return Schema**:
- Always return a table with clearly named columns
- Use PascalCase for column names
- Include descriptive key columns (e.g., CustomerKey, ProductKey)
- Add surrogate keys where appropriate (1-indexed integers)

**Performance Considerations**:
- Target <5 seconds for 10,000 records on typical hardware
- Warn in documentation if function is computationally expensive
- Prefer GENERATESERIES over CALENDAR for efficiency

### Documentation Standards

Each new function requires:
1. **Markdown file** in `docs/` folder with:
   - Parameters table (name, type, description, default)
   - Returns table (column name, type, description)
   - Usage example with realistic values
   - Features list (3-7 bullet points)
2. **README.md update**: Add function to list with one-line description
3. **Code comments**: Header block explaining purpose, algorithm approach

### Package Conventions

**Naming**: `DataGoblins.SampleData.[FunctionName]`
**Versioning**: Semantic versioning (currently 0.1.0-beta)
**Annotations**: DAXLIB_PackageId and DAXLIB_PackageVersion in manifest

---

## Workflow for Evaluating Sample Files

When analyzing user-provided sample PBIX/PBIP files:

### Step 1: Catalog Features
Extract and list:
- Tables and relationships
- Calculation groups and their patterns
- RLS roles and DAX expressions
- Measures with interesting patterns (time intelligence, many-to-many)
- Custom columns vs calculated columns
- Data quality scenarios (nulls, orphans, duplicates)

### Step 2: Map to UDF Opportunities
For each feature, ask:
1. Could this be generated by a DAX UDF?
2. Does it pass the **Evaluation Framework** (generalizability, parameterization, demo value, complexity, composition)?
3. What parameters would control meaningful variation?
4. Does it compose with existing functions or require new infrastructure?

### Step 3: Categorize & Prioritize
Group findings into:
- **High Priority**: Passes all evaluation criteria, unlocks new scenarios
- **Medium Priority**: Passes most criteria, incremental value
- **Low Priority**: Marginal benefit or fails key criteria
- **Reject**: Industry-specific, overly complex, or redundant

### Step 4: Constructive Critique
For each proposed function:
- **Challenge assumptions**: "This seems like it optimizes for [specific scenario]—how would it generalize?"
- **Probe parameterization**: "What parameters actually matter vs. what's noise?"
- **Question value**: "What demo scenario does this unlock that we can't do now?"
- **Test composition**: "How does this interact with Orders/Customer/Product functions?"

### Step 5: Prototype Specification
For accepted proposals, draft:
```markdown
## Function: DataGoblins.SampleData.[Name]

**Purpose**: One-sentence description

**Parameters**:
| Name | Type | Description | Default |
|------|------|-------------|---------|
| ... | ... | ... | ... |

**Returns**: N columns - [brief description]

**Key Scenarios Enabled**:
- Scenario 1
- Scenario 2

**Composition**: Works with [existing functions] via [integration points]
```

---

## Examples of Good vs. Bad Function Proposals

### ✅ Good Proposal: Many-to-Many Bridge Table

**Proposal**: Generate bridge table for many-to-many relationships (e.g., Products-to-Categories, Customers-to-Segments)

**Why It Passes**:
- **Generalizability**: Many-to-many is a universal pattern
- **Parameterization**: `num_left_keys`, `num_right_keys`, `avg_relationships_per_left`, `seed`
- **Demo Value**: Enables testing many-to-many relationships, aggregation behavior
- **Complexity**: Moderate DAX, clear value
- **Composition**: Takes key lists from existing dimension functions

**Constructive Critique**: "How do we avoid creating invalid relationships? Should we have a parameter for relationship density variation?"

---

### ❌ Bad Proposal: Healthcare Claims Dimension

**Proposal**: Generate ICD-10 diagnosis codes, procedure codes, and claim status hierarchies

**Why It Fails**:
- **Generalizability**: Extremely healthcare-specific
- **Demo Value**: Only relevant for healthcare BI demos
- **Maintenance**: ICD-10 codes change annually, requiring updates

**Constructive Critique**: "This is great for healthcare demos, but it doesn't serve retail, finance, education, etc. Could we instead create a generic 'Hierarchical Code Dimension' that works across domains? Healthcare could use it for ICD-10, retail for SKU codes, finance for GL accounts..."

---

### ✅ Good Proposal: Slowly Changing Dimension (Type 2)

**Proposal**: Generate SCD Type 2 dimension with historical tracking (ValidFrom, ValidTo, IsCurrent)

**Why It Passes**:
- **Generalizability**: SCD Type 2 is universal pattern
- **Parameterization**: `num_natural_keys`, `avg_versions_per_key`, `start_date`, `end_date`, `change_frequency`, `seed`
- **Demo Value**: Enables testing time-variant dimension scenarios, period-over-period comparisons
- **Complexity**: Moderately complex DAX, but high value
- **Composition**: Can generate SCD wrapper around Customer/Product/Store outputs

**Constructive Critique**: "Should this be a separate function, or a mode parameter on existing dimensions? What's the tradeoff?"

---

### ❌ Bad Proposal: Add 47 New Product Subcategories

**Proposal**: Expand Product function with detailed subcategories for 15 different industries

**Why It Fails**:
- **Generalizability**: More options ≠ more generalizability
- **Complexity**: Parameter explosion, maintenance burden
- **Value**: Doesn't unlock new scenarios, just more variants

**Constructive Critique**: "The current 5 categories × 5-6 subcategories already demonstrate hierarchy patterns. Adding more doesn't enable new testing scenarios—it just makes the function harder to maintain. What specific demo scenario requires 47 subcategories that 30 doesn't cover?"

---

## Anti-Patterns to Avoid

### The "Feature Kitchen Sink"
**Symptom**: "Let's add parameters for every possible variation!"
**Problem**: Functions become unwieldy, most parameters stay at defaults
**Solution**: Focus on 4-8 high-impact parameters; compose multiple functions for complex scenarios

### The "Industry Template"
**Symptom**: "This would be perfect for manufacturing/healthcare/finance!"
**Problem**: Sacrifices generalizability for narrow applicability
**Solution**: Extract the universal pattern (hierarchies, time-variance) and make it domain-agnostic

### The "One More Column"
**Symptom**: "Just add columns for X, Y, and Z..."
**Problem**: Return schema bloat, user confusion
**Solution**: Return essential columns; users can extend with calculated columns

### The "Manual Escape Hatch"
**Symptom**: "Users can just manually edit the output to..."
**Problem**: Defeats infrastructure-as-code value prop
**Solution**: If manual edits are required, the function doesn't solve the problem

---

## Success Metrics

A successful function expansion achieves:
1. **Scenario Unlock**: Enables demonstrating ≥1 Power BI feature/pattern previously hard to show
2. **Adoption Potential**: Would be used by ≥30% of Power BI pros for testing/demos
3. **Composition Value**: Integrates with ≥2 existing functions to create compound scenarios
4. **Maintenance Sustainability**: Can be maintained without domain expertise
5. **Documentation Clarity**: New user can understand and use it in <5 minutes

---

## Version History

- **v0.1.0-beta**: Initial release with 6 core functions (Orders, Inventory, Customer, Product, Store, Dates)
- **v0.2.0-beta (Current - In Testing)**: Added RLS_UserMapping function for Row-Level Security testing
  - **Status**: Function implemented, tested, awaiting final commit/push approval
  - Generates user-to-value permission mappings with realistic distribution patterns
  - Uses normal distribution (via Central Limit Theorem) for permission counts per user
  - Supports fine-grained, hierarchical security testing through composition
  - Fantasy/sci-fi themed usernames matching existing library aesthetics (shadow.prime@data-goblins.com, etc.)
  - Enables safe, reproducible RLS demos without Azure AD dependencies
  - **Implementation Details**:
    - 6 parameters: dimension_column, role_name (STRING), num_users, avg_permissions_per_user, stddev_permissions_per_user, seed
    - Returns 3 columns: UserPrincipalName, Role, Value
    - Statistical control via mean/stddev enables realistic permission distributions
    - Orphaned users (zero permissions) emerge naturally from distribution tail
  - **Bug Fixes Applied**:
    - Fixed GENERATE column name conflicts using SELECTCOLUMNS pattern
    - Changed TEXT type hint to STRING for DAX compatibility
    - Wrapped final GENERATE in SELECTCOLUMNS to prevent column naming collisions
  - **Evaluation Source**: Identified from Contoso sample file analysis (rubens-contoso-sample-pbip)
    - Sample demonstrated RLS pattern with Country/State hierarchy
    - Dynamic pattern chosen for generalizability over static industry-specific approaches
- **Next**: Future expansions based on sample file evaluation and mission alignment

---

## Context Engineering Notes

**For AI Systems Processing This File**:
- This file uses progressive disclosure: TL;DR → Core → Deep Reference
- Most tasks should reference **Core Mission** and **Evaluation Framework**
- Deep reference sections load only when evaluating specific proposals
- Maintain **Constructive Critic** stance: challenge assumptions, probe value, test generalizability
- Prioritize **context budget**: cite specific sections rather than reiterating entire file

**Token Budget Guidance**:
- TL;DR: ~100 tokens (sufficient for orientation)
- Core Mission: ~800 tokens (sufficient for alignment)
- Evaluation Framework: ~1200 tokens (sufficient for decision-making)
- Full file: ~3500 tokens (load only when comprehensive reference needed)
