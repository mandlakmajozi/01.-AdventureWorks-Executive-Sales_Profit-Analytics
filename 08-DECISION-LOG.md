# Decision Log

## Purpose

This log captures the key decisions made during the **AdventureWorks Executive Sales & Profit Analytics** project.

It focuses on decisions that demonstrate:

- Business reasoning
- Technical judgement
- Data governance
- Trade-off analysis
- Maintainability
- Solution design

Detailed Power Query steps and transformation formulas are available within the Power BI solution and are intentionally not repeated here.

---

## Project Context

Formal source documentation such as an ERD and data dictionary was not available during the initial assessment.

Where field definitions could not be confirmed, decisions were based on:

- Business requirements
- Available source structure
- Full-dataset profiling
- Observable data behaviour
- Validation within Power Query

Decisions can be revisited if stronger source documentation or new requirements become available.

---

## Decision Register

| ID | Decision | Rationale / Business Value | Status |
|---|---|---|---|
| D001 | Use **Import mode** | The solution focuses on historical sales, profitability and executive KPIs rather than live transaction monitoring. Import provides strong report performance and broad Power BI functionality. | Confirmed |
| D002 | Profile the **entire dataset** before final data-quality decisions | Profiling only the first 1,000 rows produced misleading results. Full profiling exposed additional values and improved the reliability of column-retention decisions. | Confirmed |
| D003 | Assess queries in a **business-led logical order** | FactInternetSales and FactResellerSales represent the main sales events. Their structure and keys were used to prioritise the dimensions required to explain those events. | Confirmed |
| D004 | Separate **discovery/staging** from the final semantic model | Potentially useful fields are retained during investigation when necessary, but only justified fields should remain in the final model. This prevents premature deletion without creating an unnecessarily wide production model. | Confirmed |
| D005 | Remove unused **navigation columns** while retaining required keys | Related dimensions will be deliberately modelled rather than expanded automatically into fact queries. This provides greater control and supports a cleaner star-schema design. | Confirmed |
| D006 | Retain technical and alternate keys where they support relationships, reconciliation or developer continuity | Not every retained field must be visible to report users. Some fields exist to make the solution understandable and maintainable for future developers. | Confirmed |
| D007 | Remove `OrderQuantity` from FactInternetSales | Full-dataset profiling showed one distinct value across the available data, so the field added no analytical variation to the current solution. | Confirmed |
| D008 | Remove `ExtendedAmount` from FactInternetSales | Full-data validation confirmed that it duplicated `UnitPrice` on every available row, providing no additional analytical value. | Confirmed |
| D009 | Retain both `ProductStandardCost` and `TotalProductCost` | Although their current values match, their names indicate potentially different business concepts. Without source definitions, removing one could discard information whose intended meaning differs. | Confirmed / Review if documentation becomes available |
| D010 | Assess FactResellerSales independently from FactInternetSales | Internet and reseller sales represent different business processes. A field that has no value in Internet Sales may still support reseller fulfilment, tracing or performance analysis. | Confirmed |
| D011 | Retain Product Start Date, End Date and Status | These attributes can help explain product introductions, discontinuations and availability changes that affect sales performance. | Confirmed |
| D012 | Exclude reseller employee count from the final analytical scope | AdventureWorks needs to evaluate reseller performance, not manage the reseller's internal workforce efficiency. Executive attention should remain on factors that materially affect AdventureWorks. | Confirmed |
| D013 | Minimise unnecessary employee and customer personal information | Contact details and operational personal information should not enter the analytical model unless they support an explicit business requirement. Analytical attributes are prioritised over unnecessary identifying information. | Confirmed |
| D014 | Do not combine fact tables until their **grain and business meaning** are understood | Different fact tables may represent different events and levels of detail. Combining incompatible facts could create duplication, double counting and incorrect measures. | Confirmed |
| D015 | Consider flattening related dimensions during semantic-model design | The source database structure does not need to be reproduced exactly in Power BI. Related dimensions may later be simplified where this improves the star schema and report usability. | Deferred to modelling |
| D016 | Compare `FactCurrencyRate` and `NewFactCurrencyRate` before selecting or combining them | Their names and structures suggest overlap, but their relationship is not documented. They will be compared before deciding whether one is redundant, newer or suitable for appending. | Investigate |
| D017 | Do not automatically include FactFinance without its required analytical context | FactFinance contains an Account key, but its related account dimension is not currently loaded separately. Finance will only be included once its supporting structure and business requirement are understood. | Investigate |
| D018 | Confirm the business purpose of ProspectiveBuyer before including it | Similarity to customer data does not automatically justify prospect analysis. The table should only enter the final model if prospective-customer or conversion analysis supports the agreed requirements. | Investigate |
| D019 | Preserve meaningful nulls instead of applying blanket replacements | Profiling showed that missing values can represent legitimate absence, conditional applicability, unrecorded information or open-ended values. Replacing them with `0`, `Unknown` or `Not Applicable` without evidence could change business meaning or data types. | Confirmed |
| D020 | Leave DimReseller order-history fields null for resellers with no recorded reseller sales | The same 44 reseller keys with null order-month/year fields do not appear in `FactResellerSales`. The available data therefore supports “no recorded reseller sales” rather than an invented order period. | Confirmed |
| D021 | Leave DimReseller minimum-payment fields unchanged | `MinPaymentAmount` is populated only for `MinPaymentType = 3`; Types 1 and 2 consistently have null amounts. The amount therefore appears conditionally applicable, and filling nulls would add unsupported meaning. | Confirmed |
| D022 | Retain `DimCustomer[Suffix]` and leave missing values null | A suffix may be required to distinguish people with otherwise similar names. Low population does not make the attribute analytically or operationally meaningless, and absence of a suffix is legitimate. | Confirmed |
| D023 | Reconstruct `NewFactCurrencyRate[DateKey]` from `Date` | `DateKey` was completely null while `Date` was fully populated. The `YYYYMMDD` DateKey convention was validated against both `FactCurrencyRate` and `DimDate`, allowing the missing foreign key to be derived reliably rather than replaced arbitrarily. | Confirmed |
| D024 | Defer business-friendly renaming until preparation and validation are complete | Retaining source names during active preparation improves traceability and reduces rework. Surviving columns will be renamed in a controlled pass immediately before load. | Confirmed |

---

## Decision Principles

The project follows four rules:

1. **Business requirements come first.**
2. **Observed evidence is preferred over assumptions.**
3. **Discovery can be broad; the final semantic model should be lean.**
4. **Technical decisions must remain understandable to the next developer.**

---

## Open Decisions

The following will be resolved as the project progresses:

- Final scope of Finance analysis
- FactCurrencyRate vs NewFactCurrencyRate
- Final employee analytical attributes
- Final customer identifying/contact fields
- Role of ProspectiveBuyer
- Role of Call Center and Survey data
- Final dimension flattening
- Final fact-table relationships and grain
