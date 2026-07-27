## Power Query Fundamentals

### Objectives
- Connected to AdventureWorksDW2022 through SQL Server.
- Explored the Power Query Editor interface.
- Learned how queries retrieve and transform data.
- Understood the Applied Steps pipeline.
- Used the Formula Bar to inspect M expressions.
- Verified Query Folding using View Native Query.

### Key Decisions
- Use Import mode for executive reporting.
- Perform transformations in Power Query rather than modifying the source database.
- Keep transformations foldable where possible to improve refresh performance.

## Data Assessment Approach and Limitations

Source-level documentation, including an entity relationship diagram and data dictionary, was not available during data preparation.

Column-retention decisions were therefore based on:

- Confirmed business requirements
- Power Query inspection
- Full-dataset column profiling
- Row-level validation using temporary comparison columns where required
- Analytical value, model size, maintainability and governance considerations

Power Query profiling was changed from the default first 1,000 rows to the entire dataset before final decisions were made.

The decisions documented in this project reflect the evidence available at the time and should be reassessed if additional source documentation, stakeholder requirements or changes in data behaviour become available.
