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

### Lessons Learned
- Power Query stores transformation logic rather than transformed data.
- Applied Steps form a sequential pipeline.
- Query Folding pushes supported transformations back to SQL Server.
- SQL Server executes one optimized query whenever possible.
