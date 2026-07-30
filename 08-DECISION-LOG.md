# Data Preparation

## Purpose

This document explains how AdventureWorks source data is assessed, profiled, selected and prepared for the **Executive Sales & Profit Analytics** solution.

Detailed table- and column-level decisions, alternatives and trade-offs are maintained in [08-DECISION-LOG.md](08-DECISION-LOG.md).

---

## Source and Storage Mode

- **Source:** AdventureWorksDW2022 hosted in SQL Server
- **Power BI storage mode:** Import
- **Preparation tool:** Power Query

Import mode was selected because the solution focuses primarily on historical analysis, executive KPIs and scheduled refresh.

It provides responsive reports and broad Power BI functionality while avoiding the performance requirements of continuous source queries.

---

## Data Assessment Approach

The project follows four stages:

1. **Discovery** – understand the available schema and business processes.
2. **Profiling** – inspect data quality, values, distributions and potential redundancy.
3. **Preparation** – transform and validate the required data.
4. **Model design** – retain only data justified by the analytical solution.

During discovery, potentially useful fields may remain available for investigation.

This does not mean every available field will enter the final semantic model.

---

## Source Documentation Limitations

Source-level documentation was not available during the current assessment.

Unavailable documentation included:

- Entity Relationship Diagram (ERD)
- Data dictionary
- Formal column definitions
- Source-to-target mappings

Decisions were therefore based on:

- Confirmed business requirements
- Power Query structure
- Observable data values
- Full-dataset profiling
- Temporary validation logic where required
- Model design
- Performance
- Maintainability
- Governance

Decisions should be reassessed if stronger source documentation becomes available.

---

## Full-Dataset Profiling

Power Query profiling was changed from the default **first 1,000 rows** to **entire dataset** before final decisions were made using:

- Distinct counts
- Unique values
- Nulls
- Errors
- Minimum and maximum values
- Value distribution

This materially affected the project.

For example, `SalesOrderLineNumber` appeared constant in the initial preview but contained eight distinct values when the full dataset was profiled.

---

## Temporary Validation Columns

Temporary helper columns are used when profiling alone cannot prove whether two fields match row-by-row.

For example, a temporary Boolean comparison was created between:

`ExtendedAmount`

and:

`UnitPrice`

The comparison returned `TRUE` for every available record.

The helper column was removed after validation.

---

## Logical Table Assessment Order

Tables are not assessed alphabetically or according to the source order.

The order follows:

- Primary business events
- Business requirements
- Visible relationship keys
- Analytical dependency
- Business hierarchy

Current assessment order:

1. FactInternetSales
2. FactResellerSales
3. DimProduct
4. DimProductCategory
5. DimProductSubcategory
6. DimDate
7. DimReseller
8. DimEmployee
9. DimPromotion
10. DimCurrency
11. DimSalesTerritory
12. DimCustomer
13. FactInternetSalesReason
14. DimSalesReason
15. FactProductInventory
16. DimDepartmentGroup
17. DimGeography
18. DimOrganization

The remaining queries currently retain their source order pending deeper assessment:

- DimScenario
- FactCallCenter
- FactCurrencyRate
- FactFinance
- FactSalesQuota
- FactSurveyResponse
- NewFactCurrencyRate
- ProspectiveBuyer

---

## Primary Sales Processes

The two primary business events currently identified are:

- `FactInternetSales`
- `FactResellerSales`

Other fact tables represent different business processes and will not automatically be included in the final model.

Each fact table must be assessed according to:

- Grain
- Measures
- Keys
- Relationships
- Business requirement
- Analytical value

---

## Navigation Columns

Unused navigation columns are removed when:

- The related dimension is available separately
- The scalar relationship key is retained
- The navigation column is not required for transformation logic

Relationships will be deliberately designed in the semantic model rather than relying on automatically exposed navigation objects.

---

## Keys and Developer Continuity

Relationship and technical keys may remain available even if they are hidden from report users.

These fields can support:

- Relationships
- Troubleshooting
- Reconciliation
- Developer handover
- Future integration

Not every model field must appear in reports.

---

## Alternate Keys

Alternate keys are retained during the current scoping stage when they may support:

- Business identifiers
- Cross-source reconciliation
- Integration
- Future relationships
- Developer understanding

Alternate keys are not inherently required for inactive relationships.

Their final inclusion will be reviewed during semantic-model design.

---

## Date Handling Principle

The project retains required **date roles**, not automatically every representation of every date.

Examples include:

- Order Date
- Ship Date
- Due Date

Date keys are retained where required for relationships to the date dimension.

Redundant physical-date columns may be removed when they add no separate analytical or validation value.

---

## Column Selection Strategy

During discovery and preparation, potentially valuable information can remain available while its purpose is investigated.

Before production, every retained field must justify its cost through one or more of:

- Business analysis
- Relationship
- Validation
- Reconciliation
- Governance
- Developer support

This prevents premature deletion during discovery while avoiding unnecessary fields in the final semantic model.

---

# FactInternetSales

## Confirmed Retained Fields

- ProductKey
- OrderDateKey
- DueDateKey
- ShipDateKey
- CustomerKey
- PromotionKey
- CurrencyKey
- SalesTerritoryKey
- SalesOrderNumber
- SalesOrderLineNumber
- UnitPrice
- UnitPriceDiscountPct
- DiscountAmount
- ProductStandardCost
- TotalProductCost
- SalesAmount
- TaxAmt
- Freight

## Confirmed Removed Fields

- RevisionNumber
- OrderQuantity
- ExtendedAmount
- CarrierTrackingNumber
- CustomerPONumber
- OrderDate
- DueDate
- ShipDate
- Unused navigation columns

---

## FactInternetSales Validation Findings

### SalesOrderLineNumber

Initial profiling of the first 1,000 records suggested a single value.

Full-dataset profiling revealed eight distinct values.

**Decision:** Retain.

---

### OrderQuantity

Full-dataset profiling showed one distinct value.

The field therefore provides no analytical variation in the available dataset.

**Decision:** Remove from the current model.

Review if future data behaviour changes.

---

### ExtendedAmount

The values appeared identical to `UnitPrice`.

A temporary row-level Boolean comparison returned `TRUE` for every available record.

**Decision:** Remove as an exact duplicate in the available dataset.

---

### ProductStandardCost and TotalProductCost

Both fields currently contain identical values.

They are retained because their names imply potentially different business concepts, and no source documentation is available to prove that they are semantically identical.

**Decision:** Retain both pending stronger evidence.

---

## FactInternetSales Naming Standard

| Source | Business-Friendly Name |
|---|---|
| ProductKey | Product Key |
| OrderDateKey | Order Date Key |
| DueDateKey | Due Date Key |
| ShipDateKey | Ship Date Key |
| CustomerKey | Customer Key |
| PromotionKey | Promotion Key |
| CurrencyKey | Currency Key |
| SalesTerritoryKey | Sales Territory Key |
| SalesOrderNumber | Sales Order Number |
| SalesOrderLineNumber | Sales Order Line Number |
| UnitPrice | Unit Price |
| UnitPriceDiscountPct | Unit Price Discount Percentage |
| DiscountAmount | Total Discount |
| ProductStandardCost | Standard Unit Cost |
| TotalProductCost | Total Product Cost |
| SalesAmount | Sales Amount |
| TaxAmt | Tax Amount |
| Freight | Freight Amount |

Naming changes must improve readability without changing or inventing the source field's business meaning.

---

# FactResellerSales

`FactResellerSales` is retained as a primary sales-event query.

Its fields are assessed independently from Internet Sales because reseller transactions may contain different operational and analytical information.

`CarrierTrackingNumber` and `CustomerPONumber` remain available during investigation because they may support fulfilment, order tracing or reseller-performance investigation.

They are not yet confirmed as final executive-reporting fields.

---

# DimProduct

`StartDate`, `EndDate` and `Status` are retained.

They may support product-lifecycle analysis including:

- Product introductions
- Discontinuations
- Product removals
- Availability-related changes in sales performance

---

# DimReseller

`NumberOfEmployees` is excluded from the current final-model scope.

AdventureWorks evaluates resellers according to their measurable contribution to AdventureWorks performance rather than how the reseller manages its own workforce.

Bank and payment-related fields remain under investigation and require an approved risk/fraud business requirement before final inclusion.

---

# DimEmployee

Direct contact and operational fields currently identified for removal include:

- Login ID
- Email
- Phone
- Emergency contact details
- Employee photo

Employee analytical fields such as performance, territory, department, compensation and availability remain subject to requirement validation and governance review.

---

# DimCustomer

Customer segmentation attributes may support executive analysis.

Direct contact information such as email and telephone does not automatically belong in the final executive semantic model and requires a specific analytical requirement.

---

# FactProductInventory

`DateKey` and `MovementDate` are currently retained until their separate meanings can be validated.

No field will be removed solely because two values appear similar during early profiling.

---

# FactCurrencyRate and NewFactCurrencyRate

Both queries remain under investigation.

Before either is selected, appended or removed, compare:

- Structure
- Row count
- Date coverage
- Currency coverage
- Nulls and errors
- Duplicate combinations
- Rate values

---

# FactFinance

FactFinance remains under investigation.

The query contains `AccountKey`, but a separately loaded `DimAccount` query is not currently available.

If finance analysis is confirmed in scope, the required dimension must be deliberately sourced and assessed rather than relying on navigation columns.

---

# ProspectiveBuyer

ProspectiveBuyer remains under investigation.

Its inclusion depends on whether prospect or conversion analysis becomes an approved project requirement.

---

## Current Status

Completed:

- Power Query fundamentals
- Query folding
- Data profiling
- Data types
- Data categories
- Remove Columns vs Choose Columns
- Column naming principles
- Full-dataset profiling
- Initial source scoping
- FactInternetSales assessment
- Initial multi-table field assessment

Next:

Continue table-level scoping and begin applying the approved Power Query transformations.
