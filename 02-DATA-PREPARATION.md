# Data Preparation

## Purpose

This document summarises how the source data was assessed and prepared for the **AdventureWorks Executive Sales & Profit Analytics** solution.

Detailed reasoning behind material decisions is recorded separately in [08-DECISION-LOG.md](08-DECISION-LOG.md).

The final Power Query transformations and semantic model provide the technical implementation details.

---

## Source

- **Database:** AdventureWorksDW2022
- **Platform:** SQL Server
- **Power BI Storage Mode:** Import
- **Data Preparation Tool:** Power Query

The source contains multiple fact and dimension tables representing different business processes.

Only data relevant to the agreed analytical requirements will ultimately be included in the final semantic model.

---

## Source Documentation

Formal source documentation such as an:

- Entity Relationship Diagram (ERD)
- Data dictionary
- Formal table definitions
- Formal column definitions

was not available during the initial preparation phase.

Data assessment was therefore based on:

- Business requirements
- Available query structures
- Column names
- Observable data values
- Power Query profiling
- Relationship keys
- Validation tests

Where the meaning of a field could not be confirmed, the uncertainty was considered before a final decision was made.

---

## Data Profiling

Power Query profiling tools were used to inspect:

- Data quality
- Null values
- Errors
- Distinct values
- Unique values
- Distributions
- Minimum and maximum values
- Potential duplicate or constant columns

Profiling was changed from the default **first 1,000 rows** to **entire dataset** before making final decisions based on column behaviour.

This was necessary because the first 1,000 rows were not always representative of the complete dataset.

---

## Missing Values and Data Quality

Missing values were reviewed before any transformation was applied.

Nulls were not automatically replaced because they can represent different business situations, including:

- A value that does not apply
- A value that is not recorded
- A conditionally populated attribute
- A legitimately open-ended value

Potential resolutions were reviewed against related columns and tables before implementation. Values were only changed where the available evidence supported a reliable transformation.

One confirmed issue was `NewFactCurrencyRate[DateKey]`, which was completely null while the corresponding `Date` column was fully populated. The relationship between `Date` and `DateKey` was validated against both `FactCurrencyRate` and `DimDate`. A replacement DateKey was then derived using the existing `YYYYMMDD` convention and validated before the original empty column was removed.

---

## Value Standardisation

Populated values were also reviewed for abbreviations, coded categories and inconsistent business labels.

Replacements were only approved where the meaning of the source code could be validated from the available data. The same code was not assumed to have the same meaning across different columns.

Validated mappings included:

- Product clothing sizes: `S`, `M`, `L`, `XL` expanded to business-friendly size descriptions while numeric product sizes were left unchanged
- Product style: `M`, `F`, `U` expanded to `Male`, `Female`, `Unisex`
- Employee marital status: `M`, `S` expanded to `Married`, `Single`
- Gender attributes using the validated `M` / `F` coding expanded consistently to `Male` / `Female`

Whole-value matching was used so that short codes such as `M` or `S` could not accidentally alter characters inside longer text values.

Potential replacements whose meaning was not yet proven, such as `NA`, were left unchanged pending further evidence.

An additional standardisation candidate was identified in `ProspectiveBuyer[Education]`, where abbreviated or incomplete education descriptions require mapping validation before any transformation is applied.

---

## Validation

Where profiling alone was insufficient, temporary validation columns were created.

These were used to test assumptions such as whether two columns contained identical values on every row.

Temporary validation columns were removed after the investigation was completed.

Transformations affecting missing, derived or standardised values were validated against related source fields, distinct-value profiles or dimension keys before being accepted.

---

## Query Assessment

Queries were assessed according to:

- Business requirements
- Main business events
- Analytical importance
- Relationship requirements
- Business hierarchy

The primary sales processes were assessed first:

- FactInternetSales
- FactResellerSales

Dimensions and supporting tables were then prioritised according to the information required to analyse those sales processes.

---

## Column Selection

Columns were reviewed against the business requirements.

During discovery, potentially useful fields may remain available while their purpose is investigated.

Before the final semantic model is completed, unnecessary fields will be removed to improve:

- Model simplicity
- Performance
- Maintainability
- Governance
- User experience

Both **Choose Columns** and **Remove Columns** may be used depending on the structure of the query and the stage of preparation.

---

## Navigation Columns

Unused navigation columns were removed where the related tables were available separately.

The corresponding relationship keys were retained where required.

This allows relationships to be deliberately designed in the semantic model instead of expanding related tables directly into fact queries.

---

## Keys

Relationship and technical keys were retained where they may support:

- Relationships
- Reconciliation
- Troubleshooting
- Future transformations
- Developer handover

Technical keys do not need to be displayed to report users and may later be hidden in the semantic model.

Alternate keys were retained during the current scoping stage where they may support future relationships, integration or reconciliation.

Their final requirement will be reviewed during modelling.

---

## Date Fields

Required date roles were retained for later time-intelligence modelling.

Examples include:

- Order Date
- Due Date
- Ship Date
- Inventory Movement Date

Duplicate representations of the same date may be removed where the Date dimension provides the required analytical functionality.

---

## Column Naming

Source column names are being retained during preparation to preserve traceability and reduce rework while transformations are still being assessed.

Once preparation and validation are complete, surviving columns will be renamed in a controlled final pass before load where necessary to improve:

- Readability
- Business understanding
- DAX readability
- Developer handover
- Report usability

Abbreviations will be expanded and spaces added where appropriate, provided the revised name preserves the original business meaning.

---

## Data Minimisation

Fields that did not support the current analytical requirements were removed or identified for later removal.

Particular attention was given to:

- Operational identifiers
- Duplicate fields
- Constant-value fields
- Unnecessary personal information
- Fields outside the current business scope

Potentially useful fields that could not yet be validated were retained temporarily for further investigation.

---

## Fact Table Preparation

The primary sales fact tables are being assessed independently because they represent different business processes.

A field removed from Internet Sales is not automatically removed from Reseller Sales.

Each fact table is assessed according to:

- Its business event
- Available measures
- Relationship keys
- Analytical requirements
- Data behaviour

Fact tables will not be combined unless their grain and business meaning are proven to be compatible.

---

## Dimension Preparation

Dimensions are being assessed according to their role in explaining the business events.

Potential dimension simplification or flattening will be considered during semantic-model design rather than during initial source preparation.

---

## Queries Requiring Further Investigation

Some queries require additional assessment before their final role can be determined.

These currently include areas such as:

- Currency rates
- Finance
- Inventory
- Prospective buyers
- Call centre information
- Survey responses

These tables will only enter the final model where they support an approved business requirement.

---
